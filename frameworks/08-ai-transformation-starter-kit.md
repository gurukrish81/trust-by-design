# Enterprise AI Transformation Starter Kit
### Trust-by-Design Toolkit — Artifact 08

**Author:** Guru Krish | ex-Google · ex-Amazon  
**Purpose:** 30-day foundation for enterprise AI transformation  
**Covers:** AI Transformation · AI Security · AI Governance  
**Last Updated:** June 2026

---

## What This Is — And What It Is Not

This is not a governance checklist.

This is a 30-day operating blueprint for transforming how your enterprise deploys, secures, and scales AI. Governance is one component of that transformation — not the whole thing.

By day 30 you will have:
- Complete visibility into every AI system running in your organization
- A platform foundation that every business unit can build on
- Security controls protecting your highest-risk AI deployments
- An operating model that scales AI adoption without creating a bottleneck
- A measurement system that proves AI is delivering business value

This is how Google unified 50+ siloed AI programs across 14 verticals into a governed, measured, scalable portfolio. It is how Amazon moved from a 7-week AI access backlog to 1-week resolution. The same pattern works at your organization.

---

## Before You Start — The Honest Inventory (Day 0)

Do this before anything else. Ask every engineering and product team lead one question:

**"What AI tools, APIs, or LLM integrations is your team currently using?"**

Do not formalize this. Do not make it an audit. Just ask.

What you will find: 3-5x more AI than you knew about. Tools purchased on credit cards. API keys in repositories. Copilot licenses bought without IT involvement. Claude integrations built over a weekend. This is universal. This is not a failure. This is where every organization is in 2026.

Write down everything. That list is your starting point. Everything in this starter kit governs that list.

---

## Week 1 — Visibility: Know What You Have

**The transformation goal this week:**
You cannot transform what you cannot see. Before making any platform or governance decisions, map the current state completely.

### Day 1-3: Complete the AI Inventory

Beyond the team lead interviews, pull evidence from your infrastructure:
- Cloud audit logs (AWS CloudTrail for Bedrock calls, GCP for Vertex AI calls, Azure for OpenAI calls)
- Software procurement records — what SaaS tools with AI features have been purchased?
- Security scanning — API keys in repositories pointing to AI providers

Build a single inventory. Every AI integration gets one row:

| System | Team | What It Does | Data It Touches | Users | Business Impact |
|---|---|---|---|---|---|
| Contract Summarizer | Legal | Summarizes vendor contracts | Contract PDFs, vendor terms | 8 attorneys | Reduces review time |
| Expense Categorizer | Finance | Auto-categorizes expenses | Employee expense data, amounts | 200 employees | Reduces manual entry |
| Hiring Screener | HR | Ranks candidate resumes | Candidate PII, work history | 12 recruiters | Speeds hiring pipeline |

### Day 4-7: Classify Every System by Risk Tier

Apply four questions to every item in your inventory:

**Q1: Does it make or significantly influence a decision about a person?**
Employment, credit, housing, healthcare, legal outcomes → Tier 3 minimum

**Q2: Does it access sensitive, regulated, or confidential data?**
PII, PHI, financial records, security data, legal documents → Tier 2 minimum

**Q3: Does it take autonomous actions without human review?**
Send emails, modify records, execute transactions, approve requests → Tier 2 minimum, likely Tier 3

**Q4: Could an error cause legal, financial, reputational, or safety harm?**
SOX implications, Fair Housing, HIPAA, GDPR, brand risk → Tier 2 minimum

**The four tiers — what they mean for transformation:**

| Tier | Type | Transformation Implication |
|---|---|---|
| **Tier 1 — Low** | Internal drafting, summarization, search | Enable and scale. Low risk, high productivity. Self-certification only. |
| **Tier 2 — Medium** | Customer-facing AI, workflow automation, data access | Enable with guardrails. Platform handles security. Business unit configures workflow. |
| **Tier 3 — High** | HR decisions, legal outputs, financial reporting, housing recommendations | Enable with controls. Human in the loop for consequential decisions. Platform + workflow-specific gates. |
| **Tier 4 — Critical** | Clinical decisions, enforcement actions, credit determinations | Legal review + executive approval before deployment. Human makes all consequential decisions. |

**Realtor.com-specific examples:**

| Use Case | Tier | Why |
|---|---|---|
| Agent recommendation engine | **Tier 3** | Fair Housing Act — housing AI that influences which properties or agents are shown can discriminate at scale |
| AI-assisted hiring screening | **Tier 3** | Employment decision about a person — HITL mandatory |
| Contract summarization for attorneys | **Tier 2** | Attorney reviews output before any action — AI is a tool, not a decision-maker |
| Property listing description drafting | **Tier 1** | Internal content tool, human reviews before publishing |
| Finance expense categorization | **Tier 2** | Workflow automation with human sampling review |
| Finance forecasting for reporting | **Tier 3** | Influences financial statements — SOX requires human approval gate |

**Week 1 deliverable:** Complete AI inventory with tier assigned to every system. This single document gives you more AI visibility than 70% of enterprises have today.

---

## Week 2 — Platform: Build the Foundation Everyone Builds On

**The transformation goal this week:**
Remove the condition where every team builds their own AI stack. Replace it with a shared platform that handles security, cost, and compliance once — so business units can move fast without building from scratch.

### The LLM Gateway Decision

This is the most important infrastructure decision in your AI transformation.

**Without a gateway:** 40 teams, 40 direct API connections, 40 different logging approaches, 40 different cost tracking methods, zero central visibility, zero central policy enforcement.

**With a gateway:** All traffic flows through one point. You see everything. You control everything. You enforce policy once. Cost attribution is automatic. Audit logging is universal.

**What the gateway handles at the platform level — once, for every business unit:**

| Control | Who Benefits | How It Works |
|---|---|---|
| PII detection | Every team | Runs on every input before any model sees it. Legal team doesn't build this. HR team doesn't build this. It's already there. |
| Audit logging | Every team | Every interaction logged automatically. No business unit builds logging. |
| Cost attribution | Leadership | Every API call tagged to a team and cost center. CFO sees AI spend by department. |
| Rate limiting | IT/Security | No team can accidentally run up a $50,000 AI bill overnight. |
| Acceptable Use Policy | Legal/HR | Prohibited use cases blocked at the gateway. Doesn't require engineering work in each application. |
| Model routing | Engineering | Tier 1 requests route to fast/cheap models. Tier 3 requests route to more capable models. Cost optimized automatically. |

**What individual business units still configure:**
- Their specific workflow (how AI fits into their process)
- Their HITL triggers (which outputs require human review in their context)
- Their success metrics (what good looks like for their use case)
- Their domain-specific prompts

### Build the Intake Process

Every new AI use case goes through intake before build starts. This is how you prevent the next generation of ungoverned AI from accumulating.

The intake process is a form — not a meeting, not a committee, not a review board. A form that:
1. Asks 5-8 questions about the use case
2. Auto-assigns a risk tier based on answers
3. Routes to the right review path automatically
4. Notifies the right people
5. Creates a record that the system was reviewed

**The 5 questions:**
1. What does this system do in one sentence?
2. What data does it access? (select from list)
3. Does it make or influence decisions about people? (yes/no)
4. Does it take autonomous actions without human review? (yes/no)
5. What happens if it's wrong? (describe business impact)

Tier 1 answers → self-certification email, system registered in inventory, done.
Tier 2 answers → lightweight checklist, platform guardrails confirmed, done.
Tier 3 answers → full assessment, HITL gate required, engineering review.
Tier 4 answers → legal review, executive approval, formal assessment.

**Week 2 deliverable:** LLM gateway operational or evaluation complete. Intake form live. Every new AI use case now has a governed path.

---

## Week 3 — Controls: Protect What Matters Most

**The transformation goal this week:**
Apply targeted security controls to your highest-risk existing deployments. Not everything — the 10% that creates 90% of the risk.

### Prioritize Your Tier 3 and Tier 4 Systems

From your Week 1 inventory, identify every Tier 3 and Tier 4 system currently live without adequate controls. These are your urgent items.

**For each Tier 3/4 system currently running without controls:**

**Immediate (this week):**
Add audit logging. Non-negotiable. If something goes wrong and there's no audit trail, you cannot reconstruct what happened. This is the single highest-value control you can add to a live system in the shortest time.

**Short-term (next 2 weeks):**
- Verify access is scoped to minimum necessary users
- Confirm or add HITL gate for consequential decisions
- Run 2-hour adversarial test using Artifact 03 threat model

**What HITL looks like in practice — it is not manual:**

Legal contract review (Tier 2):
```
AI reads 50-page contract →
Surfaces 3 high-risk clauses →
Routes those 3 to attorney →
Attorney reviews in 10 minutes →
Attorney approves → contract moves forward
```
Not manual. The attorney reviews 10 minutes of AI output instead of 3 hours of contract.

Finance expense workflow (Tier 2):
```
AI categorizes 47 line items automatically →
Flags 2 anomalous items →
Manager sees: "47 auto-approved, 2 need review" →
Manager reviews 2 items in 90 seconds →
Workflow completes
```
Not manual. Human time from 49 reviews to 2.

Hiring screening (Tier 3):
```
AI reviews 200 applications →
Ranks by role fit →
Surfaces top 20 with reasoning →
Recruiter reviews AI ranking and reasoning →
Recruiter selects who advances — not AI →
Decision is human, AI prepared the information
```
HITL is not a bottleneck. It is the control that makes the system legally defensible.

### The Finance VP Prototype — A Real Scenario

A VP has a working AI prototype for a finance workflow. They want to go to production. How do you handle this as the AI Transformation Lead?

**Your response:**
> "This is exactly what we should be scaling. Give me 3 days to make sure we're protected going into production. I'll come back with what we need and how long it takes. We can move fast and do this right."

**Day 1 — classify it:**
Finance workflow touching financial reporting data → Tier 3. SOX applies. Means: audit trail mandatory, human approval gate before any output used in reporting, segregation of duties between AI builders and financial approvers.

**Day 2 — gap assessment against production requirements:**
Does the prototype have: audit logging (probably not), HITL gate (probably not), access controls (probably open), success metric defined (probably not). These are product gaps, not compliance gaps. The production version needs these. The prototype proved the concept.

**Day 3 — brief the VP:**
> "The prototype proves the concept. The production version needs three things: audit logging for SOX (2 days engineering), a human approval step before outputs reach financial reporting (1 day), and access restricted to Finance (half a day). 3.5 days of engineering work. One week of shadow mode alongside existing process. Two weeks to production. You get the velocity. The company gets the protection. I own production readiness. You own the business outcome."

**SOX in plain English:** Public companies must have internal controls over financial reporting. The CEO and CFO personally certify this. Any AI that touches financial reporting is part of that control environment. Auditors will ask about it. If there's no audit trail and no human approval gate, that's a material control weakness. Realtor.com is owned by News Corp, a public company. SOX applies.

### Configure Monitoring

For Tier 2, 3, and 4 systems, add to your existing monitoring infrastructure (whatever your company uses — Datadog, CloudWatch, Splunk):

**Three alerts that matter most:**
1. PII detected in output (should never happen if input detection is working) → immediate investigation
2. Guardrail trigger rate spikes 3x above baseline in 1 hour → someone is probing the system
3. Logging stops on any governed system → incident declared immediately

**One daily metric to review:**
Anomalous requests — inputs that don't match the normal pattern for that system. A coding assistant suddenly receiving requests about financial data is an anomaly.

**Week 3 deliverable:** Audit logging on all Tier 3/4 systems. HITL gates verified or added. Three monitoring alerts live. Your highest-risk AI is no longer ungoverned.

---

## Week 4 — Scale: Build the Operating Model

**The transformation goal this week:**
Make AI governance self-sustaining. The central team cannot review everything. The operating model enables distributed ownership with central visibility.

### The AI Champions Program

One AI Champion per major business unit. This is the same model that made Google's AI/ML governance scale across 14 corporate verticals.

The Champion is not a new hire. It is a senior engineer or PM in each team who:
- Completes intake forms for their team's new AI use cases
- Is the first point of contact when governance questions arise in their team
- Gets access to their team's AI health dashboard
- Attends monthly AI governance sync (45 minutes)

What they get in return:
- Training on Trust-by-Design framework (half-day)
- Early access to new AI capabilities before general release
- Recognition in the AI transformation program

### AI Acceptable Use Policy

One page. Plain English. Three sections:

**Section 1 — Use AI freely for:**
Content drafting, summarization, coding assistance, internal search, data analysis where a human reviews outputs. These are Tier 1. No approval needed.

**Section 2 — Get approval before deploying AI for:**
Customer-facing decisions, HR processes, legal document generation, financial reporting inputs, anything that affects housing or credit. These are Tier 2/3. Use the intake form.

**Section 3 — Never use AI for:**
Autonomous decisions about employment, housing, credit, or clinical outcomes without human review. Prohibited regardless of tier.

### Executive Reporting — Monthly, One Page

Your CISO and CTO need to be able to answer one question at any time: "What AI is running in our company and is it safe?"

Four numbers give them that answer:

1. **AI inventory by tier:** 142 Tier 1, 38 Tier 2, 12 Tier 3, 2 Tier 4
2. **New systems this month:** 8 new, 8 governed through intake, 0 ungoverned additions
3. **Open control gaps:** 2 Tier 3 systems still need audit logging (remediation by [date])
4. **Incidents:** 0

That is your monthly executive report. Two minutes to read. Everything leadership needs to know.

**Week 4 deliverable:** AI Champions identified and trained. AUP published and communicated. First executive report drafted. The operating model is live.

---

## 30-Day Milestone Checklist

**Week 1 — Visibility:**
- [ ] Shadow AI audit completed — every team interviewed
- [ ] Cloud audit logs pulled — ungoverned API calls surfaced
- [ ] AI inventory complete — every integration documented
- [ ] Risk tier assigned to every item in inventory

**Week 2 — Platform:**
- [ ] LLM gateway operational or vendor selected
- [ ] Cost attribution visible by team
- [ ] Intake form live — new AI use cases have a governed path
- [ ] AUP drafted (publish in Week 4)

**Week 3 — Controls:**
- [ ] Audit logging on every Tier 3/4 system
- [ ] HITL gates verified for consequential decisions
- [ ] Adversarial test run on highest-risk system
- [ ] Three monitoring alerts configured

**Week 4 — Scale:**
- [ ] AI Champions identified (one per major BU)
- [ ] Champions trained — half-day session
- [ ] AUP published and communicated to all employees
- [ ] First executive report drafted

**If you complete this: you have transformed your AI operating model in 30 days. You have more AI governance infrastructure, more visibility, and more security controls than most Fortune 500 companies.**

---

## What Comes Next

This starter kit gives you the foundation. The full Trust-by-Design toolkit gives you the depth for each dimension.

| Your Next Challenge | Trust-by-Design Artifact |
|---|---|
| Need detailed risk classification examples | 01 — Risk Tiering Framework |
| Need technical security controls with code | 02 — LLM Guardrail Patterns |
| Deploying agentic AI (Claude Code, Antigravity) | 03 — Agentic AI Threat Model |
| Need formal evaluation and red team protocol | 04 — Evaluation Framework |
| Facing GDPR, HIPAA, SOX, EU AI Act requirements | 05 — Compliance by Design |
| Need to prove AI dev tool ROI to leadership | 06 — AI Dev Productivity Measurement |
| Need a real enterprise LLM implementation example | 07 — Enterprise LLM Case Study |

---

## Consulting Engagements

**AI Transformation Assessment** (2 weeks)
Shadow AI audit, inventory build, risk classification, gap analysis, 30-90 day roadmap. Deliverable: written assessment and prioritized action plan. Suitable for organizations beginning their AI transformation.

**Framework Implementation** (4-6 weeks)
Full Trust-by-Design implementation: LLM gateway deployment, guardrail architecture, intake process, AI Champions program, executive reporting. Deliverable: operational AI transformation infrastructure.

**Strategic Advisory Retainer** (ongoing)
Monthly engagement: new AI use case review, CISO/CTO briefing support, agentic AI strategy, regulatory guidance. For organizations scaling AI transformation and needing ongoing governance expertise.

Contact: gurukrish81@gmail.com | gurukrish.org | github.com/gurukrish81/trust-by-design

---

*Part of the Trust-by-Design Toolkit by Guru Krish. © 2026 Guru Krish. All rights reserved. This work may be shared with attribution for non-commercial purposes. For consulting or commercial use, contact gurukrish81@gmail.com*
