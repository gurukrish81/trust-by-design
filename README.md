# Trust-by-Design Toolkit
Status: Active development · Last updated May 2026

### A Practical Governance Framework for Safe Enterprise AI

**Author:** Guru Krish | Director — AI Governance, Security & Platform Systems  
**Contact:** gurukrish81@gmail.com | [linkedin.com/in/gurukrish](https://linkedin.com/in/gurukrish)  
**Background:** ex-Google · ex-Amazon | Austin, TX

---

## What This Is

The Trust-by-Design Toolkit is an open-source governance framework for security and risk leaders who need to deploy AI systems safely at enterprise scale — not as a policy exercise, but as a repeatable, enforceable control architecture.

It is built from direct implementation experience:
- Architecting an LLM-driven governance pipeline on **AWS Bedrock** at Amazon, reducing data access resolution from 7 weeks to 1 week
- Designing **Risk Inspector**, a global security governance platform across **150+ business units** at Google
- Leading AI/ML governance across **50+ programs and 14 corporate verticals** at Google

---

## The Four Pillars

| Pillar | What It Covers |
|--------|---------------|
| **Risk Tiering** | Classify any AI system into 4 risk levels with scoring matrix and controls |
| **Threat Modeling** | Agentic AI attack vectors, failure modes, and adversarial patterns |
| **Guardrail Patterns** | Input validation, output filtering, PII defense, prompt injection controls |
| **Evaluation Framework** | Pre-deployment gates, red team protocol, continuous monitoring |

---

## Artifacts

```
trust-by-design/
├── README.md                          ← You are here
├── frameworks/
│   ├── 01-risk-tiering-framework.md   ← How to classify AI risk (4 tiers)
│   ├── 02-llm-guardrail-patterns.md   ← Guardrail enforcement patterns
│   ├── 03-agentic-ai-threat-model.md  ← Threat model for autonomous AI (coming soon)
│   ├── 04-evaluation-framework.md     ← Pre-deployment + ongoing evaluation (coming soon)
│   └── 05-compliance-by-design.md     ← GDPR, HIPAA, SOX touchpoints in AI (coming soon)
├── case-studies/
│   └── 06-enterprise-llm-case-study.md ← Anonymized Bedrock pipeline case study (coming soon)
├── notebooks/ 
│   └── 01-pii-detector.ipynb          ← Working PII detection tool (Python) (coming soon)
└── starter-kit/
    └── 07-ai-governance-starter-kit.md ← Quick-start guide for enterprises (coming soon)
```

---

## Who This Is For

- **CISOs and Security CTOs** building AI governance programs
- **AI Governance leads** operationalizing responsible AI at scale
- **Enterprise architects** designing safe LLM deployment patterns
- **Risk and compliance teams** integrating AI into existing control frameworks

---

## Framework Alignment

All artifacts align to:
- **NIST AI RMF** — Map, Measure, Manage, Govern functions
- **NIST CSF** — Identify, Protect, Detect, Respond, Recover
- **ISO 42001** — AI management system standard
- **GDPR / CCPA** — Privacy and data protection for AI systems
- **EU AI Act** — Risk-based AI classification

---

## License

© 2026 Guru Krish. All rights reserved. This work may be shared with attribution for non-commercial purposes. For commercial or enterprise use, contact gurukrish81@gmail.com

