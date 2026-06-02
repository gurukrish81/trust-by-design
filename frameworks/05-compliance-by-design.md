# Compliance by Design Playbook
### Trust-by-Design Toolkit — Artifact 05

**Author:** Guru Krish | ex-Google · ex-Amazon  
**Framework Alignment:** GDPR · HIPAA · SOX · CCPA · EU AI Act · NIST AI RMF  
**Last Updated:** May 2026

---

## The Core Principle

Compliance retrofitted is compliance theater.

When legal and compliance teams are brought in after an AI system is deployed, they face an impossible choice: shut down a production system that the business depends on, or sign off on controls that were never designed to meet the requirement.

Neither outcome is acceptable. And both are entirely preventable.

Compliance by Design means embedding regulatory requirements into AI system architecture from the first design decision — not discovering them during audit. This playbook provides the practical framework for doing that across the regulations most commonly applicable to enterprise AI systems.

---

## How to Use This Playbook

For each regulation, this playbook provides:
- What it requires from AI systems specifically
- When it applies (triggering conditions)
- The design decisions that create compliance from the start
- The controls to implement and when in the development lifecycle
- The evidence you need to demonstrate compliance

---

## Regulation 1 — GDPR (General Data Protection Regulation)

**Jurisdiction:** European Union — applies to any organization processing EU residents' data regardless of where the organization is located.

**Why it matters for AI:** GDPR Article 22 creates specific obligations around automated decision-making. If your AI system makes or significantly influences decisions about people — credit, employment, access, pricing — GDPR applies with heightened requirements.

### Key GDPR Requirements for AI Systems

| Requirement | Article | What It Means for Your AI System |
|-------------|---------|----------------------------------|
| Lawful basis for processing | Art. 6 | You must have a legal reason to use personal data in training or inference |
| Data minimization | Art. 5(1)(c) | Collect and process only what the AI actually needs |
| Purpose limitation | Art. 5(1)(b) | Data collected for one purpose cannot be used to train an AI for another |
| Right to explanation | Art. 22 | Users have the right to meaningful explanation of automated decisions |
| Right to erasure | Art. 17 | If user requests deletion, their data must be removable from training sets |
| Privacy by design | Art. 25 | Privacy controls must be designed in — not added later |
| Data breach notification | Art. 33 | AI-related data breaches must be notified within 72 hours |

### Compliance by Design — GDPR Checklist

**Before you build:**
```
□ Document the lawful basis for every personal data element used
□ Conduct a DPIA (Data Protection Impact Assessment) for high-risk processing
□ Map all personal data flows — where does it enter, how is it processed, 
  where does it go?
□ Define data retention periods for all personal data in the system
□ Confirm right-to-erasure can be technically implemented
□ Get Data Processing Agreement signed if using third-party AI providers
```

**In the architecture:**
```
□ PII detection and redaction before data enters the model (see Artifact 02)
□ Pseudonymization of personal data in training datasets
□ Logging of all personal data processing for audit trail
□ Separate storage of personal data from model weights
□ Data minimization enforced at ingestion — collect only required fields
□ Purpose binding — technical controls preventing data use outside defined scope
```

**In the user experience:**
```
□ Clear disclosure that AI is being used in decision-making
□ Mechanism for users to request human review of automated decisions
□ Explanation capability — system can produce a human-readable account of 
  how a decision was reached
□ Easy data deletion request pathway
```

**Evidence for compliance demonstration:**
```
□ DPIA document
□ Data flow diagram with classification labels
□ Audit logs showing data processing records
□ PII detection and redaction logs
□ Data subject request log and response records
□ DPA with AI provider
```

### The Article 22 Challenge — Explainability

Article 22 is the most challenging GDPR requirement for AI systems. It grants data subjects the right to "meaningful information about the logic involved" in automated decisions.

**Practical implementation:**
```python
def generate_gdpr_explanation(
    decision_output: str,
    input_features_used: list,
    model_confidence: float,
    decision_factors: list,
    user_id: str
) -> dict:
    """
    Generates a human-readable explanation of an AI decision
    suitable for GDPR Article 22 compliance.
    """
    explanation = {
        "decision_summary": decision_output,
        "confidence_level": f"{model_confidence * 100:.0f}%",
        "key_factors": decision_factors[:3],  # Top 3 most influential factors
        "data_used": [f for f in input_features_used if f != "sensitive"],
        "human_review_available": True,
        "human_review_contact": "ai-decisions@company.com",
        "right_to_object": "You have the right to request human review of this decision",
        "explanation_generated_at": "timestamp",
        "user_id": user_id  # For audit trail
    }
    
    return explanation
```

---

## Regulation 2 — HIPAA (Health Insurance Portability and Accountability Act)

**Jurisdiction:** United States — applies to covered entities and business associates handling Protected Health Information (PHI).

**Why it matters for AI:** Any AI system that processes, stores, or transmits PHI — patient records, diagnoses, treatment information, billing data — is subject to HIPAA. This includes AI used by healthcare organizations AND any vendor providing AI services to healthcare organizations (making you a Business Associate).

### Key HIPAA Requirements for AI Systems

| Requirement | Rule | What It Means for Your AI System |
|-------------|------|----------------------------------|
| PHI safeguards | Security Rule | Technical controls protecting PHI in AI pipelines |
| Minimum necessary | Privacy Rule | AI should access only the PHI required for the specific task |
| Audit controls | Security Rule | Track who accessed what PHI, when, and why |
| Transmission security | Security Rule | PHI sent to LLM APIs must be encrypted in transit |
| Business Associate Agreement | Privacy Rule | Required before any PHI flows to AI vendor |
| Breach notification | Breach Rule | PHI exposure via AI requires notification within 60 days |

### Compliance by Design — HIPAA Checklist

**Before you build:**
```
□ Determine if system will touch PHI — if yes, HIPAA applies in full
□ Execute Business Associate Agreement with all AI/LLM vendors
□ Assign a HIPAA Security Officer accountable for AI system compliance
□ Conduct risk analysis specific to the AI system
□ Document PHI data flows completely
```

**In the architecture:**
```
□ PHI detection before any data enters the LLM (extend PII detector 
  with medical entity types: diagnosis codes, medication names, 
  provider identifiers, insurance member IDs)
□ De-identification of PHI before model processing where clinically feasible
□ Encryption at rest and in transit for all PHI
□ Access controls: minimum necessary access to PHI for each system component
□ Automatic session timeout for user-facing AI interfaces
□ Audit logging with: user ID, PHI accessed, timestamp, action taken
```

**PHI entity types to add to PII detector:**
```python
HIPAA_PHI_ENTITIES = [
    "PERSON",              # Patient names
    "DATE_TIME",           # Dates of service, birth dates, death dates
    "LOCATION",            # Geographic subdivisions smaller than state
    "PHONE_NUMBER",        # Phone/fax numbers
    "EMAIL_ADDRESS",       # Email addresses
    "US_SSN",              # Social security numbers
    "MEDICAL_LICENSE",     # Medical record numbers
    "US_BANK_NUMBER",      # Account numbers
    "IP_ADDRESS",          # IP addresses
    # Custom entities for healthcare:
    "DIAGNOSIS_CODE",      # ICD-10 codes tied to individual
    "INSURANCE_ID",        # Health plan beneficiary numbers
    "PROVIDER_ID",         # Provider identification numbers
]
```

---

## Regulation 3 — SOX (Sarbanes-Oxley Act)

**Jurisdiction:** United States — applies to publicly traded companies and their subsidiaries.

**Why it matters for AI:** SOX Section 302 and 404 require executives to certify the accuracy of financial reporting and attest to the effectiveness of internal controls. When AI systems are involved in financial reporting, forecasting, audit, or controls testing — SOX applies to those systems.

### Key SOX Requirements for AI Systems

| Requirement | Section | What It Means for Your AI System |
|-------------|---------|----------------------------------|
| Internal control over financial reporting | Sec. 404 | AI used in financial processes must have documented controls |
| CEO/CFO certification | Sec. 302 | Executives must be able to certify AI-assisted financial outputs |
| Audit trail | Sec. 404 | All AI decisions affecting financial data must be traceable |
| Change management | Sec. 404 | Changes to AI systems affecting financials require controlled process |
| Access controls | Sec. 404 | Segregation of duties must extend to AI system access |

### Compliance by Design — SOX Checklist

```
□ Document all AI systems that touch financial data or decisions
□ Map AI decision points in financial reporting workflows
□ Implement human approval gates for all AI outputs that affect 
  financial statements
□ Ensure audit trail covers: input data, AI processing, output, 
  human review, final decision
□ Apply change management controls to all modifications to 
  financial AI systems
□ Implement segregation of duties — the person who builds the model 
  should not be the person who approves its outputs for financial use
□ Include AI system controls in annual SOX 404 assessment
```

---

## Regulation 4 — CCPA (California Consumer Privacy Act)

**Jurisdiction:** California — applies to businesses meeting size/revenue thresholds that process California residents' personal information.

**Why it matters for AI:** CCPA grants consumers rights over their personal data including the right to know what data is collected, the right to delete, and the right to opt out of sale. AI systems that use consumer data in training or inference must support these rights.

### Key CCPA Requirements for AI Systems

| Right | What It Requires |
|-------|-----------------|
| Right to Know | Disclose what personal data AI uses and how |
| Right to Delete | Remove consumer data from training sets and inference logs |
| Right to Opt-Out | Allow consumers to opt out of data use for AI training |
| Right to Non-Discrimination | Cannot penalize consumers who exercise CCPA rights |

### Compliance by Design — CCPA Checklist

```
□ Update privacy notice to disclose AI use of personal data
□ Build data deletion capability that reaches AI training sets
□ Implement opt-out mechanism for AI training data use
□ Document all personal data categories used in AI systems
□ Ensure consumer rights requests can be fulfilled within 45-day window
□ Audit third-party AI vendors for CCPA compliance
```

---

## Regulation 5 — EU AI Act

**Jurisdiction:** European Union — applies to AI systems placed on the EU market or affecting EU residents. Enforcement begins August 2026.

**Why it matters now:** The EU AI Act is the first comprehensive AI regulation globally. It creates a risk-based framework that maps directly to the Trust-by-Design risk tiering approach. High-risk AI systems face mandatory conformity assessments, technical documentation requirements, and ongoing monitoring obligations.

### EU AI Act Risk Classification

| Category | Examples | Requirements |
|----------|----------|-------------|
| Unacceptable risk | Social scoring, real-time biometric surveillance | Prohibited |
| High risk | HR AI, credit AI, law enforcement AI, education AI | Mandatory conformity assessment, technical documentation, human oversight |
| Limited risk | Chatbots, deepfakes | Transparency obligations — must disclose AI use |
| Minimal risk | Spam filters, recommendation systems | No mandatory requirements |

### Mapping EU AI Act to Trust-by-Design Risk Tiers

| EU AI Act Category | Trust-by-Design Tier | Governance Response |
|---|---|---|
| Unacceptable | N/A — Do not deploy | Do not build |
| High risk | Tier 1 Critical | Full governance suite + conformity assessment |
| Limited risk | Tier 2/3 | Core controls + transparency disclosure |
| Minimal risk | Tier 4 | Standard logging |

### Compliance by Design — EU AI Act Checklist (High Risk)

```
□ Determine if system qualifies as high-risk under Annex III
□ Establish Quality Management System for the AI system
□ Complete technical documentation requirements:
  - System description and intended purpose
  - Training data description and provenance
  - Validation and testing methodology
  - Performance metrics and accuracy
  - Robustness and cybersecurity measures
□ Implement human oversight measures — system cannot override human
□ Register system in EU AI database (when available)
□ Implement transparency obligations — inform users they are interacting with AI
□ Establish post-market monitoring plan
□ Prepare for conformity assessment (internal or third-party depending on system type)
```

---

## Lifecycle Integration Map

Compliance by Design means every regulation has a home in the development lifecycle — not as a gate at the end, but as a requirement that shapes design from the beginning.

```
STAGE          GDPR          HIPAA         SOX           CCPA          EU AI Act
─────────────────────────────────────────────────────────────────────────────────
DESIGN         DPIA          Risk Analysis  Control Map   Privacy       Technical
               Data Map      BAA Signed     Doc           Notice        Doc Start
               
BUILD          PII Detection  PHI Detection Audit Trail   Deletion      Human
               Minimization   Encryption    Segregation   Capability    Oversight
               Purpose Lock   Access Ctrl   Change Ctrl   Opt-Out       Measures

TEST           Erasure Test   BAA Coverage  SOX 404       Rights        Conformity
               Explanation    PHI Audit     Testing       Fulfillment   Assessment
               Test           Log Review    Walkthrough   Test          Prep

DEPLOY         Subject        Breach Plan   CEO/CFO       Consumer      Register
               Rights Flow    Training      Cert Process  Notice Live   EU Database

OPERATE        DSR Response  Audit Logs    Annual 404    Rights        Post-Market
               Breach 72hr   Breach 60day  Assessment    Requests      Monitoring
─────────────────────────────────────────────────────────────────────────────────
```

---

## Compliance Evidence Matrix

When auditors come — and they will — here is what you need to show for each regulation:

| Evidence | GDPR | HIPAA | SOX | CCPA | EU AI Act |
|----------|------|-------|-----|------|-----------|
| Data flow diagram | ✅ | ✅ | ✅ | ✅ | ✅ |
| Risk assessment / DPIA | ✅ | ✅ | ✅ | — | ✅ |
| Audit logs | ✅ | ✅ | ✅ | ✅ | ✅ |
| PII/PHI detection logs | ✅ | ✅ | — | ✅ | ✅ |
| Access control records | ✅ | ✅ | ✅ | — | ✅ |
| Vendor agreements (DPA/BAA) | ✅ | ✅ | — | ✅ | — |
| Incident response records | ✅ | ✅ | ✅ | ✅ | ✅ |
| User rights request log | ✅ | — | — | ✅ | ✅ |
| Technical documentation | — | — | ✅ | — | ✅ |
| Training records | ✅ | ✅ | ✅ | — | ✅ |

---

## From Google: Compliance by Design in Practice

At Google, the 3P Security and Privacy program (Risk Inspector) governed 150+ third-party integrations against GDPR, HIPAA, CCPA, and NIST controls. The key lesson from that program:

**Compliance gates that come at the end of a process will always be bypassed under delivery pressure.** Teams will find workarounds, get temporary exceptions, or launch and promise to fix it later.

The only compliance approach that actually works at scale is embedding the requirements in the intake form, the architecture checklist, and the deployment gate — so that compliance is not a separate lane but the road itself.

Risk Inspector reduced compliance review time from 150 days to 10 hours not by reducing rigor — but by making the compliant path the only path. The controls were built into the workflow, not bolted on at review time.

That is Compliance by Design.

---

## Related Artifacts

- `01-risk-tiering-framework.md` — Risk tier determines which regulations apply
- `02-llm-guardrail-patterns.md` — Technical controls that satisfy regulatory requirements
- `04-evaluation-framework.md` — Evidence collection integrated into the evaluation process

---

*Part of the Trust-by-Design Toolkit by Guru Krish. © 2026 Guru Krish. All rights reserved. This work may be shared with attribution for non-commercial purposes. For consulting or commercial use, contact gurukrish81@gmail.com*
