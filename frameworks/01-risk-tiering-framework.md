# AI Risk Tiering Framework
### Trust-by-Design Toolkit — Artifact 01

**Author:** Guru Krish | ex-Google · ex-Amazon  
**Framework Alignment:** NIST AI RMF · ISO 42001 · EU AI Act  
**Last Updated:** May 2026

---

## Why Risk Tiering Matters

Not all AI systems carry equal risk. A document summarization tool used internally operates in a fundamentally different risk environment than an autonomous agent with write access to financial systems.

Applying uniform governance controls to all AI deployments is both operationally impractical and strategically ineffective — it creates compliance theater while leaving high-risk systems under-governed.

The Risk Tiering Framework solves this by providing a **structured, repeatable method** for classifying any AI system based on three primary dimensions, then routing it to the appropriate governance path.

---

## The Three Classification Dimensions

Every AI system is evaluated across three dimensions. The combination determines the risk tier.

### Dimension 1 — Autonomy Level
*How much does the system act without human review or approval?*

| Score | Description |
|-------|-------------|
| 1 | Read-only, no actions taken — summarization, search, Q&A |
| 2 | Drafts outputs for human review before any action |
| 3 | Takes limited, reversible actions with human oversight |
| 4 | Takes autonomous, potentially irreversible actions with minimal human review |

### Dimension 2 — Data Sensitivity
*What data does the system access, process, or generate?*

| Score | Description |
|-------|-------------|
| 1 | Public or anonymized data only |
| 2 | Internal business data — non-regulated, non-PII |
| 3 | Confidential data — PII, financial records, HR data |
| 4 | Regulated data — PHI, PCI, legal, security-classified |

### Dimension 3 — Decision Impact
*What happens if the system is wrong or compromised?*

| Score | Description |
|-------|-------------|
| 1 | No downstream impact — output is informational only |
| 2 | Minor impact — incorrect output causes rework, not harm |
| 3 | Significant impact — incorrect output affects business decisions, customer trust, or compliance posture |
| 4 | Severe impact — incorrect output causes financial loss, legal liability, safety risk, or security incident |

---

## Risk Tier Assignment

### Scoring Method
Add the three dimension scores. Divide by 3 to get average. Map to tier below.

| Average Score | Risk Tier | Governance Path |
|--------------|-----------|----------------|
| 1.0 – 1.7 | **Tier 4 — Low** | Standard logging + AUP acknowledgment |
| 1.8 – 2.4 | **Tier 3 — Medium** | Baseline controls + quarterly review |
| 2.5 – 3.2 | **Tier 2 — High** | Core control set + security architecture review |
| 3.3 – 4.0 | **Tier 1 — Critical** | Full guardrail suite + red team + human-in-loop |

---

## The Four Tiers — Detailed

### 🔴 Tier 1 — Critical
**What it looks like:** Autonomous AI agents with access to sensitive systems; AI making or heavily influencing decisions with financial, legal, or safety consequences; AI with write access to production systems or regulated data.

**Real examples:**
- Agentic AI that autonomously triages and routes security incidents
- LLM with access to HR systems that can initiate employment actions
- AI that generates and submits regulatory filings
- Autonomous code deployment agents in production environments

**Required controls:**
- Full guardrail suite (input validation, output filtering, PII detection, prompt injection defense)
- Human-in-the-loop checkpoint for every consequential action
- Immutable audit logging with full input/output capture
- Red team evaluation required before production deployment
- Dedicated incident response playbook
- Monthly guardrail efficacy testing
- Security architecture sign-off required

**Review cadence:** Continuous monitoring + pre-deploy gate + monthly review

---

### 🟠 Tier 2 — High
**What it looks like:** LLM-assisted workflows with access to confidential data; systems that inform significant decisions without full autonomy; cross-system integrations handling sensitive information.

**Real examples:**
- LLM pipeline that classifies and routes support tickets containing customer PII
- AI that analyzes security logs and surfaces risk patterns for human review
- Generative AI that drafts contract language for legal team approval
- AI-assisted hiring tools that screen and rank candidates

**Required controls:**
- Input validation and sanitization
- PII detection and redaction before processing
- Output content filtering
- Rate limiting and anomaly detection
- Audit logging (input, output, user, timestamp)
- Security architecture review before deployment
- Monthly guardrail testing

**Review cadence:** Monthly + incident-triggered reassessment

---

### 🟡 Tier 3 — Medium
**What it looks like:** Internal productivity tools; AI used to draft, summarize, or assist with non-sensitive work; limited data access scope.

**Real examples:**
- Internal knowledge base Q&A system on non-confidential documentation
- Meeting summarization tool for general business meetings
- Code suggestion tool for internal tooling (not production systems)
- AI-assisted writing tool for internal communications

**Required controls:**
- Output content filtering
- Basic audit logging
- Acceptable use policy acknowledgment at onboarding
- Quarterly review of system behavior

**Review cadence:** Quarterly

---

### 🟢 Tier 4 — Low
**What it looks like:** Read-only, non-sensitive queries; sandboxed experimentation; public data only; no action capability.

**Real examples:**
- Public-facing FAQ chatbot on non-sensitive product information
- Internal AI sandbox for experimentation with synthetic data
- Search augmentation with public web data

**Required controls:**
- Standard application logging
- User acknowledgment of AI-generated content
- Annual review

**Review cadence:** Annual

---

## Intake Assessment — Step by Step

Every new AI system enters governance through this structured intake. It takes 15–30 minutes and produces a tier assignment and governance routing decision.

### Step 1 — System Description
Document in one paragraph: what the system does, who uses it, what data it touches, and what actions it can take.

### Step 2 — Score Each Dimension
Answer the three dimension questions above. Assign scores 1–4. Calculate the average.

### Step 3 — Assign Preliminary Tier
Map the average score to a tier using the table above.

### Step 4 — Apply Override Rules
Certain conditions automatically escalate to Tier 1 regardless of score:
- System has write access to any production database or API
- System processes data classified as PHI, PCI, or legally privileged
- System can initiate financial transactions of any amount
- System operates without any human review checkpoint
- System has access to security tooling or vulnerability data

### Step 5 — Route to Governance Path
- **Tier 1 / 2:** Route to security architecture review. Schedule red team (Tier 1 only).
- **Tier 3:** Self-service governance checklist. Quarterly review scheduled.
- **Tier 4:** Standard logging configuration. Annual review scheduled.

### Step 6 — Register in AI System Inventory
All AI systems regardless of tier must be registered in the enterprise AI inventory with: system name, owner, tier, data classification, deployment date, and next review date.

---

## Re-Tiering Triggers

Risk tier assignments are not permanent. The following changes trigger a mandatory re-assessment:

| Trigger | Required Action |
|---------|----------------|
| New data access (any classification increase) | Re-score Dimension 2, reassign tier |
| New autonomous action capability added | Re-score Dimension 1, reassign tier |
| Expanded user base to regulated function | Full re-assessment |
| Any security or compliance incident | Immediate re-assessment + root cause review |
| Underlying model update (new version, provider change) | Re-assessment of Dimension 1 and 3 scores |
| Integration with new downstream system | Re-score all dimensions |

---

## Worked Example — LLM Security Ticket Classifier

**System:** An LLM pipeline on AWS Bedrock that ingests security access tickets, classifies them by risk type, surfaces abuse patterns, and routes for human review. Based on implementation at Amazon Security Data Intelligence.

**Dimension scoring:**

| Dimension | Score | Reasoning |
|-----------|-------|-----------|
| Autonomy | 2 | Classifies and routes — humans make final decisions |
| Data Sensitivity | 3 | Access tickets contain user identity, system names, access patterns (PII-adjacent, confidential) |
| Decision Impact | 3 | Misclassification could route a security incident incorrectly, causing delayed response |

**Average:** (2 + 3 + 3) / 3 = **2.67 → Tier 2 — High**

**Governance path applied:**
- Input validation on ticket ingestion
- PII detection before Bedrock processing
- Output filtering on classification results
- Full audit logging (ticket ID, model input, output, confidence score, routing decision, timestamp)
- Security architecture review completed pre-deployment
- Monthly guardrail efficacy testing against test suite

**Outcome:** System deployed with Tier 2 controls. Classified 2,500+ tickets. Reduced resolution time from 7 weeks to 1 week. Governance policy updated based on pattern findings.

---

## NIST AI RMF Alignment

| Framework Function | This Framework's Coverage |
|-------------------|--------------------------|
| **GOVERN** | Tier assignment process, roles and responsibilities, re-tiering triggers |
| **MAP** | Three-dimension classification, intake assessment, override rules |
| **MEASURE** | Scoring matrix, review cadence by tier, efficacy testing |
| **MANAGE** | Control requirements by tier, incident response triggers, inventory |

---

## How to Use This Framework

1. **For a new AI deployment:** Run the intake assessment. Assign a tier. Implement the required controls for that tier before going to production.

2. **For an existing AI system inventory:** Score each system against the three dimensions. Identify any systems operating without governance appropriate to their tier. Prioritize remediation by tier.

3. **For a CISO or Security CTO report:** Use the tier distribution across your AI portfolio as a governance maturity metric. Track how it changes over time as new systems are deployed and existing ones are reassessed.

4. **For a consulting engagement:** This framework is your assessment instrument. Run clients through the intake assessment for their top 10 AI use cases. The output is your initial risk register.

---

## Related Artifacts in This Toolkit

- `02-llm-guardrail-patterns.md` — The specific controls to implement for each tier
- `03-agentic-ai-threat-model.md` — Threat catalog to apply when assessing Tier 1 systems
- `04-evaluation-framework.md` — How to test and validate controls once deployed
- `07-ai-governance-starter-kit.md` — Quick-start guide linking all artifacts

---

*Part of the Trust-by-Design Toolkit by Guru Krish. MIT License.*
