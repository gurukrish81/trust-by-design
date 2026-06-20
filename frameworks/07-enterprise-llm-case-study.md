# Enterprise LLM Governance — Case Study
### Trust-by-Design Toolkit — Artifact 07

**Author:** Guru Krish | ex-Google · ex-Amazon  
**Industry:** Enterprise Technology / Security  
**System Type:** LLM-based security access classification pipeline  
**Last Updated:** June 2026

> **Note on anonymization:** This case study is based on a real production deployment. Organizational identifiers, team names, and internal system names have been changed or removed. Technical architecture, metrics, and governance decisions are documented as they occurred.

---

## The Situation

A large enterprise technology organization operated a security access governance program responsible for processing thousands of data access requests per year. Engineers across the organization submitted requests to access sensitive security datasets — vulnerability information, threat intelligence, incident records, and risk scoring data.

The access governance process was manual. A small team of security analysts reviewed each request, assessed the risk level, determined the appropriate access tier, and either approved, denied, or escalated. The process was thorough. It was also unsustainable.

**The business problem in three numbers:**

- **7 weeks** — average time from access request submission to resolution
- **2,500+** — requests processed annually, growing 40% year-over-year
- **Multiple teams** — analysts from different security domains required for complex requests, creating coordination overhead

Seven weeks to access security data is not a governance posture. It is a bottleneck. Engineers were waiting nearly two months to access data they needed to do their jobs. The backlog created two bad outcomes: legitimate work was delayed, and engineers began finding workarounds — accessing data through informal channels that bypassed governance entirely.

The security team knew the current model couldn't scale. The question was how to fix it without reducing the rigor that made the governance meaningful.

---

## The Constraints

Before designing the solution, the constraints were documented explicitly. This is a governance discipline — the constraints shape the architecture.

**Non-negotiable constraints:**
- Zero reduction in governance rigor for Tier 3 and Tier 4 access requests (high-risk and critical systems)
- Full audit trail — every decision traceable with rationale
- No unauthorized access as a result of automation
- Human review required for any ambiguous or high-stakes request
- Compliance with internal security policy and applicable regulatory requirements

**Operational constraints:**
- Existing security analyst headcount — no new hires
- Integration with existing ticketing system
- No disruption to current workflow during transition
- 90-day implementation timeline

**The key insight from the constraints:** The goal was not to automate human judgment on hard cases. The goal was to automate the classification and routing work that consumed analyst time on straightforward cases — so analysts could focus their judgment on the cases that actually needed it.

---

## The Risk Tiering Decision

Before any architecture was designed, every access request type was classified using the risk tiering framework.

**Tier classification for security data access requests:**

| Request Type | Tier | Rationale | Governance Response |
|---|---|---|---|
| Read access to aggregated, anonymized vulnerability reports | Tier 1 — Low | No PII, no sensitive system identifiers, read-only | Automated approval with logging |
| Read access to team-specific vulnerability data | Tier 2 — Medium | Team-scoped sensitive data, could reveal internal security posture | LLM classification + human review sampling |
| Write access to any security dataset | Tier 3 — High | Modification of security records creates significant risk | LLM classification + mandatory human approval |
| Access to incident response data or active threat intelligence | Tier 4 — Critical | Real-time operational security data, exfiltration risk | Human review only — no LLM involvement in approval decision |

**The Tier 4 decision is important to understand:** The LLM pipeline was explicitly excluded from making approval decisions on Tier 4 requests. The LLM classifies and routes — a human analyst makes the approval decision. This is the HITL gate in practice. It is not optional. It is not overridable.

---

## The Architecture

The solution was a four-component LLM governance pipeline deployed on a cloud-based AI platform.

```
INCOMING REQUEST
      ↓
┌─────────────────────────────────────────┐
│  COMPONENT 1: INPUT VALIDATION LAYER    │
│                                         │
│  • PII detection and redaction          │
│  • Injection pattern detection          │
│  • Request completeness validation      │
│  • Schema enforcement                   │
└─────────────────────────────────────────┘
      ↓ (clean, validated request)
┌─────────────────────────────────────────┐
│  COMPONENT 2: LLM CLASSIFICATION ENGINE │
│                                         │
│  • Risk tier assignment (1-4)           │
│  • Access category classification       │
│  • Justification quality scoring        │
│  • Similar request pattern matching     │
│  • Confidence score generation          │
└─────────────────────────────────────────┘
      ↓ (classification + confidence score)
┌─────────────────────────────────────────┐
│  COMPONENT 3: ROUTING AND DECISION LAYER│
│                                         │
│  • Tier 1: Auto-approve → logging       │
│  • Tier 2: Auto-approve + sample review │
│  • Tier 3: Route to analyst queue       │
│  • Tier 4: Route to senior analyst      │
│  • Low confidence: Route to human       │
│    regardless of tier                   │
└─────────────────────────────────────────┘
      ↓
┌─────────────────────────────────────────┐
│  COMPONENT 4: AUDIT AND COMPLIANCE LAYER│
│                                         │
│  • Immutable log of every request       │
│  • Classification rationale stored      │
│  • Human decisions logged with reviewer │
│  • Retention: 7 years (SOX alignment)   │
│  • Daily compliance report generation   │
└─────────────────────────────────────────┘
      ↓
RESOLUTION (approval / denial / escalation)
```

**Key architectural decisions and why they were made:**

**Decision 1: Confidence threshold routing**
The LLM generates a confidence score for every classification. Any request below the confidence threshold routes to a human analyst regardless of the assigned tier. This means the system fails safe — uncertainty always escalates rather than auto-approves.

```python
def route_request(classification_result: dict) -> str:
    tier = classification_result["risk_tier"]
    confidence = classification_result["confidence_score"]
    
    # Low confidence always escalates — fail safe
    if confidence < CONFIDENCE_THRESHOLD:
        return "human_review_queue"
    
    routing_map = {
        "tier_1": "auto_approve",
        "tier_2": "auto_approve_with_sampling", 
        "tier_3": "analyst_review_queue",
        "tier_4": "senior_analyst_queue"
    }
    
    return routing_map.get(tier, "human_review_queue")
```

**Decision 2: Immutable audit storage**
All logs written to object storage with object lock enabled. Write-once, read-many. The engineering team that operates the pipeline cannot modify or delete logs. This separation is intentional — it makes the audit trail defensible in any regulatory review.

**Decision 3: Human override always available**
Analysts can override any automated decision. Every override is logged with the analyst's identity, the original classification, the override decision, and the rationale. Overrides trigger a weekly review — patterns in overrides surface gaps in the classification model.

**Decision 4: Sampling for Tier 2**
10% of all auto-approved Tier 2 requests are randomly selected for human review after the fact. If the human reviewer disagrees with the automated decision, it triggers a classification model review. This is the continuous quality control mechanism — you don't review everything, but you review enough to catch systematic errors.

---

## The Governance Guardrails

Before the pipeline went to production, the guardrail architecture was implemented and tested.

**Input guardrails:**

| Guardrail | What It Does | Why It Was Needed |
|---|---|---|
| PII detection | Scans request text for names, employee IDs, email addresses | Access requests sometimes contained personal data about the requester or the data subject — this needed to be separated from the classification input |
| Injection detection | Flags requests containing patterns designed to override LLM instructions | A sophisticated requester could attempt to inject instructions that manipulate the classification result |
| Completeness validation | Rejects requests missing required fields before LLM processing | Incomplete requests would generate low-quality classifications — better to reject early than classify badly |

**Output guardrails:**

| Guardrail | What It Does | Why It Was Needed |
|---|---|---|
| Schema validation | LLM output must conform to defined JSON schema | Prevents malformed responses from reaching the routing layer |
| Confidence floor | Classifications below minimum confidence rejected, not routed | Prevents uncertain classifications from being treated as confident decisions |
| Tier escalation only | LLM can only assign a tier equal to or higher than the default for a request type | Prevents the LLM from downgrading a request that should be high-risk |

---

## The Red Team Results

Before production deployment, a structured red team evaluation was conducted using the protocol from the Evaluation Framework.

**Test categories run:**

| Category | Tests Run | Findings |
|---|---|---|
| Direct prompt injection | 24 test cases | 0 Critical, 0 High, 2 Medium |
| Data extraction attempts | 12 test cases | 0 Critical, 0 High, 0 Medium |
| Classification manipulation | 18 test cases | 0 Critical, 1 High, 3 Medium |
| PII leakage in outputs | 16 test cases | 0 Critical, 0 High, 1 Medium |

**The High finding:** A specific pattern of technical jargon in the request description could cause the LLM to assign Tier 1 to a request that should be Tier 2. Root cause: the classification prompt didn't explicitly define the boundary between Tier 1 and Tier 2 for technical system names. Remediation: added explicit examples of Tier 2 technical contexts to the system prompt. Retested — finding resolved.

**The Medium findings:** All involved edge cases in the PII detection layer — specific formats of employee IDs weren't being caught. Added to the detection patterns. Retested — all resolved.

Deployment proceeded after zero Critical findings and zero unresolved High findings.

---

## The Rollout

**Week 1-2: Shadow mode**
The pipeline ran in parallel with the existing manual process. Every request was classified by both the LLM pipeline and the human analyst. Results were compared. Disagreements were reviewed. Classification accuracy was measured against the human analyst baseline.

**Shadow mode result:** 94% agreement between LLM classification and human analyst classification on Tier 1 and Tier 2 requests. The 6% disagreements were reviewed — in most cases, the human analyst and LLM agreed on outcome but disagreed on tier assignment. No cases where LLM classified a Tier 3 request as Tier 1 or Tier 2.

**Week 3: Tier 1 automation live**
Auto-approval for Tier 1 requests went live. Human process continued for all other tiers.

**Week 4-6: Tier 2 automation live**
Auto-approval with 10% sampling for Tier 2 requests went live.

**Week 7+: Full operation**
All tiers operational. Human analysts now focused exclusively on Tier 3 and Tier 4 requests plus the 10% Tier 2 sample review.

---

## The Results — 30-Day Measurement

Measured at 30 days post full deployment, compared to the pre-deployment baseline.

**Operational metrics:**

| Metric | Before | After | Change |
|---|---|---|---|
| Average resolution time | 7 weeks | 1 week | **85% reduction** |
| Tickets requiring human review | 100% | 22% | **78% reduction** |
| Engineering overhead per request | High (multi-team coordination) | Low (routing only for complex cases) | **~70% reduction** |
| Analyst capacity available for complex cases | ~20% | ~80% | **4x increase** |

**Governance metrics:**

| Metric | Result |
|---|---|
| Compliance incidents | Zero |
| Unauthorized access events | Zero |
| Audit trail completeness | 100% — every decision logged |
| Guardrail efficacy (red team retest) | 97% on full test suite |
| Override rate (analyst overriding automation) | 3.2% — within acceptable range |
| Classification accuracy (ongoing sampling) | 96% agreement with human review |

**The result that mattered most to the business:** Security analysts who had been spending 80% of their time on routine classification were now spending 80% of their time on complex, high-stakes requests — the work that actually required their expertise. The pipeline didn't replace analysts. It made them dramatically more effective.

---

## The Lessons Learned

**Lesson 1: The confidence threshold saved us twice**
During the first two weeks of operation, two requests arrived that the LLM couldn't classify with confidence — unusual hybrid access patterns that didn't fit the training distribution. The confidence threshold routed both to human review. Both turned out to be Tier 3 requests that had been described ambiguously. Without the threshold, they might have been auto-approved as Tier 1.

**Lesson 2: Tier 4 human-only was the right call**
There was organizational pressure to automate Tier 4 approvals as well — the volume was low but the analyst time was significant. The governance decision was to keep Tier 4 human-only. During the 30-day review, one Tier 4 request arrived that contained what appeared to be a legitimate business justification but was actually an attempt to gain access to active incident response data for a security event that was still in progress. A human analyst caught it. An automated system would not have had the context to evaluate the real-time risk.

**Lesson 3: The override log was more valuable than expected**
The 3.2% override rate generated 82 logged overrides in 30 days. Reviewing those 82 cases revealed four recurring patterns where the LLM classification was systematically wrong for a specific request type. Those four patterns were fixed in the classification prompt. Override rate dropped to 1.8% in month two.

**Lesson 4: Shadow mode is not optional**
The two weeks of shadow mode where the pipeline ran in parallel with the human process was the highest-value investment in the whole project. It generated the accuracy baseline, built analyst trust in the system, and surfaced the classification edge cases before they became production incidents.

---

## Applying This to Your Environment

This case study demonstrates the core Trust-by-Design implementation pattern:

**1. Risk tier first.** Before any architecture decision, classify your use cases. The tier determines the governance response. Not every request gets the same treatment.

**2. Fail safe.** When in doubt, escalate to human. Build confidence thresholds that route uncertainty to humans, not auto-approval.

**3. Audit everything.** The immutable audit trail is not overhead. It is the evidence you need for compliance, for debugging, and for proving the system is working.

**4. Humans on hard cases.** Automation handles volume. Humans handle judgment. Design the system so automation increases human capacity for complex decisions, not eliminates humans from the process.

**5. Measure before and after.** Define your success metric before deployment. Measure at 30 days. Report to leadership. If you can't prove it's working, you can't defend it.

---

## Connection to Trust-by-Design Artifacts

| This Case Study... | Demonstrates Artifact... |
|---|---|
| Risk tier classification of request types | 01 — Risk Tiering Framework |
| Input validation, injection defense, PII detection | 02 — LLM Guardrail Patterns |
| Red team evaluation before deployment | 04 — Evaluation Framework (Phase 2) |
| 10% sampling for ongoing quality control | 04 — Evaluation Framework (Phase 3) |
| 30-day measurement with defined success metrics | 04 — Evaluation Framework (Phase 4) |
| SOX-aligned 7-year audit retention | 05 — Compliance by Design |
| Confidence threshold routing | 03 — Agentic AI Threat Model (Goal Misgeneralization defense) |

---

*Part of the Trust-by-Design Toolkit by Guru Krish. © 2026 Guru Krish. All rights reserved. This work may be shared with attribution for non-commercial purposes. For consulting or commercial use, contact gurukrish81@gmail.com*
