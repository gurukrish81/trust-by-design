# AI Developer Productivity Measurement Framework
### Trust-by-Design Toolkit — Artifact 06

**Author:** Guru Krish | ex-Google · ex-Amazon  
**Framework Alignment:** DORA Metrics · SPACE Framework · AI-Assisted Development  
**Applicable Roles:** Principal TPM, AI Platform, Developer Productivity, AmpAI teams  
**Last Updated:** June 2026

---

## The Core Problem

Every engineering organization deploying AI developer tools faces the same measurement failure:

**They measure adoption. They don't measure impact.**

"80% of engineers have GitHub Copilot" tells you nothing about whether code quality improved, review cycles shortened, or incidents increased. Adoption is a vanity metric. What you need is a productivity signal — and specifically, a signal that separates genuine acceleration from acceleration that creates downstream cost.

This framework adapts DORA's four key metrics for AI-assisted development environments, adds three AI-specific metrics that DORA doesn't cover, and provides the measurement infrastructure to track all seven continuously.

---

## Why DORA — and Why It's Not Enough

DORA (DevOps Research and Assessment) is the most validated engineering productivity framework in the industry. Its four metrics predict software delivery performance better than any other combination:

| DORA Metric | What It Measures |
|---|---|
| Deployment Frequency | How often you ship |
| Lead Time for Changes | How fast you go from idea to production |
| Change Failure Rate | How often your changes break things |
| Time to Restore Service | How fast you recover when things break |

**The problem:** DORA was designed before AI-assisted development existed. It measures outcomes but doesn't distinguish between human-generated and AI-generated changes — which means it can't tell you whether your AI tooling is helping or hurting on any of these dimensions.

**The solution:** Run DORA metrics segmented by AI-assisted vs. human-only changes, then add three AI-specific metrics to capture what DORA was never designed to measure.

---

## The Seven Metrics

### DORA Metric 1 — Deployment Frequency (AI-Adapted)

**Standard definition:** How often changes are deployed to production per day/week.

**AI adaptation:** Segment by change origin.

```
Deployment Frequency (AI) = 
  AI-assisted deployments per week / Total deployments per week

Target signal: AI-assisted deployment frequency should be HIGHER than 
human-only, indicating velocity gain without quality loss.

Red flag: AI-assisted deployment frequency is high but Change Failure 
Rate (Metric 3) is also high — AI is accelerating the wrong things.
```

**How to measure:**
```python
def deployment_frequency_by_origin(deployments: list) -> dict:
    """
    Expects each deployment record to include:
    - timestamp
    - ai_assisted: bool (PR had >30% AI-generated code)
    - outcome: 'success' | 'rollback' | 'incident'
    """
    ai_deployments = [d for d in deployments if d['ai_assisted']]
    human_deployments = [d for d in deployments if not d['ai_assisted']]
    
    return {
        "ai_assisted_per_week": len(ai_deployments) / weeks_in_period,
        "human_only_per_week": len(human_deployments) / weeks_in_period,
        "ai_velocity_multiplier": len(ai_deployments) / max(len(human_deployments), 1),
        "signal": "positive" if len(ai_deployments) > len(human_deployments) else "monitor"
    }
```

**Your baseline question:** Before deploying AI tooling, measure human-only deployment frequency for 30 days. That's your baseline. Everything after is delta.

---

### DORA Metric 2 — Lead Time for Changes (AI-Adapted)

**Standard definition:** Time from code committed to running in production.

**AI adaptation:** Measure design-to-test time, not just commit-to-production.

```
AI Lead Time = 
  Time from task assignment → first AI-assisted PR opened
  + PR review time (AI-assisted vs human-only PRs)
  + Time to merge

Target signal: AI-assisted PRs should have SHORTER lead time.
Watch for: AI writing the code fast but review taking longer — 
this means AI is shifting bottleneck from coding to review, 
not eliminating it.
```

**The insight most teams miss:** If AI cuts coding time by 40% but code review time doubles because reviewers don't trust AI output, your lead time doesn't improve. The Review Burden Index (Metric 6) captures this. Track both together.

---

### DORA Metric 3 — Change Failure Rate (AI-Adapted)

**Standard definition:** Percentage of changes that result in service degradation or require rollback.

**AI adaptation:** This is your quality gate for AI-assisted development.

```
AI Change Failure Rate = 
  Incidents/rollbacks caused by AI-assisted code
  ─────────────────────────────────────────────
  Total AI-assisted deployments

Target: AI CFR should be ≤ human-only CFR
Red flag: AI CFR > human-only CFR — AI is creating technical debt 
faster than it's creating velocity. Stop, investigate, retrain or 
restrict scope before expanding adoption.

Elite benchmark (from DORA research): <5% overall CFR
AI-specific target: Match or beat your human-only baseline
```

**This is the single most important metric.** An organization where AI accelerates deployments but increases failure rate is not more productive — it's accumulating risk that will surface as incidents and tech debt. Change Failure Rate is your quality floor.

---

### DORA Metric 4 — Time to Restore Service (AI-Adapted)

**Standard definition:** Time to recover from a production incident.

**AI adaptation:** Track whether AI tooling helps or hurts incident response.

```
AI MTTR Impact = 
  Avg time to restore: AI-caused incidents
  vs
  Avg time to restore: Human-caused incidents

Key question: When AI-generated code fails, is it harder to debug?
Common finding: AI code that isn't well-documented is harder to 
diagnose because the engineer didn't write it and doesn't intuitively 
know what it does. This increases MTTR even if the failure rate is similar.
```

**The mitigation:** Require AI-generated code to include inline comments explaining logic before PR approval. This is a governance rule, not a coding preference — it directly protects your MTTR.

---

### AI-Specific Metric 5 — AI Code Acceptance Rate

**What it measures:** Of all AI-generated suggestions presented to engineers, what percentage do they accept without modification?

```
Acceptance Rate = 
  Suggestions accepted (verbatim or minor edit)
  ───────────────────────────────────────────
  Total suggestions presented

Benchmarks:
  >75% acceptance = High trust, possible over-reliance risk
  40-75% acceptance = Healthy range — engineers are critically reviewing
  <40% acceptance = Tooling not matching codebase context, 
                    poor prompt engineering, or wrong model for task

Segmentation that matters:
  - By engineer seniority (senior engineers should accept less)
  - By task type (boilerplate vs. complex logic)
  - By codebase area (familiar vs. unfamiliar territory)
```

**The trap:** High acceptance rate feels like success. It can actually signal engineers are rubber-stamping AI output without review — which is your highest-risk scenario. Track acceptance rate alongside Change Failure Rate. If both are high, you have a problem.

---

### AI-Specific Metric 6 — Review Burden Index

**What it measures:** Does AI-generated code increase or decrease the cognitive burden on code reviewers?

```
Review Burden Index = 
  Avg review time: AI-assisted PRs (minutes)
  ──────────────────────────────────────────
  Avg review time: Human-only PRs (minutes)

RBI < 1.0 = AI is reducing review burden ✅
RBI = 1.0 = No change in review burden
RBI > 1.0 = AI is increasing review burden ⚠️
RBI > 1.5 = AI is creating significant review overhead — 
            investigate before expanding adoption ❌

Qualitative signal (quarterly survey):
  "Reviewing AI-generated PRs takes more/less/same effort as 
   human PRs?" — track sentiment alongside the quantitative metric.
```

**Why this matters for Reddit specifically:** You have 800+ engineers (or equivalent scale). If AI tooling adds 20 minutes to every PR review and you have 500 PRs per week, that's 166 engineering hours per week in additional overhead. At senior engineer cost, that's a significant number — and it erases the velocity gain from AI-assisted writing.

---

### AI-Specific Metric 7 — Hallucination Escape Rate

**What it measures:** AI errors that pass code review and reach production.

```
Hallucination Escape Rate =
  Production incidents traced to AI hallucination
  (fabricated API, wrong function signature, invented library behavior)
  ────────────────────────────────────────────────────────────────────
  Total AI-assisted deployments in period

Target: <0.5% — any higher indicates review process is insufficient

Categories of AI hallucination in code:
  H1: Fabricated API endpoints or method signatures that don't exist
  H2: Invented library behavior (calling a function that doesn't do 
      what the AI claimed)
  H3: Wrong assumptions about system state or data structure
  H4: Security anti-patterns presented as correct patterns
  H5: Outdated API usage from training data (deprecated methods)
```

**H4 is your highest-risk category.** When AI suggests a security implementation that looks plausible but is subtly wrong (e.g., a JWT validation pattern that misses a critical check), it passes review, reaches production, and creates a vulnerability. Your review checklist for AI-assisted PRs should explicitly flag security-adjacent code for mandatory senior review regardless of AI confidence.

---

## The Measurement Dashboard

Four views, each for a different audience:

### View 1 — Engineering Leadership (Weekly)

| Metric | This Week | Last Week | Baseline | Signal |
|--------|-----------|-----------|----------|--------|
| AI Deployment Frequency | | | | |
| AI Lead Time (days) | | | | |
| AI Change Failure Rate | | | | |
| AI MTTR (hours) | | | | |
| Acceptance Rate | | | | |
| Review Burden Index | | | | |
| Hallucination Escape Rate | | | | |

**Green:** At or better than baseline  
**Yellow:** Within 20% of baseline (monitor)  
**Red:** >20% worse than baseline (investigate immediately)

### View 2 — CISO / Security (Monthly)
Focus: Change Failure Rate, Hallucination Escape Rate (H4 security category), AI-caused incident timeline.

### View 3 — Executive / Board (Quarterly)
Focus: Velocity gain (Deployment Frequency, Lead Time delta), quality maintained (CFR at or below baseline), ROI calculation.

### View 4 — Individual Engineer (Self-service)
Focus: Personal Acceptance Rate, personal contribution to Review Burden, peer comparison (anonymized). Enables self-directed improvement without surveillance culture.

---

## The ROI Calculation

When a VP or CFO asks "what is the return on our AI developer tooling investment":

```
Monthly ROI = 
  (Hours saved by AI assistance × avg engineer hourly cost)
  - (Additional review burden hours × avg senior engineer cost)
  - (AI-caused incident MTTR hours × avg engineer hourly cost)
  - (AI tooling license cost)

Example calculation (1000 engineers, $250K avg total comp):
  Engineer hourly cost: ~$120/hr (loaded)
  AI saves 2 hrs/engineer/week = 2000 hrs/week = $240K/week
  
  Review burden increase: 15 min/PR × 400 PRs/week = 100 hrs/week = $12K/week
  AI incidents: 2/month × 4hr MTTR × 5 engineers = 40 hrs/month = $4.8K/month
  Tooling cost: $50K/month (1000 seats × $50/seat)
  
  Monthly net: ($240K × 4wks) - $48K review overhead - $4.8K incidents - $50K tooling
             = $960K - $102.8K
             = $857K monthly net value
```

Show this math. Most AI tooling discussions are qualitative. Showing the actual ROI model — including the costs, not just the benefits — signals that you understand the full system, not just the headline number.

---

## Implementation Roadmap

### Week 1 — Baseline (before expanding AI tooling)
- Tag all PRs by AI-assisted / human-only origin (GitHub label or PR template checkbox)
- Measure all seven metrics for 30 days human-only — this is your baseline
- Survey engineers on current review burden and tooling satisfaction

### Week 2-4 — Instrumentation
- Build automated tracking for Metrics 1-4 from your CI/CD pipeline data
- Build acceptance rate tracking from your AI tooling API (Copilot, Claude Code, Windsurf all expose this)
- Build review time tracking from PR open → approve timestamps

### Month 2 — Baseline Complete, AI Rollout Begins
- Deploy AI tooling to pilot group (20% of engineers)
- Measure all seven metrics for pilot vs. control group
- Gate broader rollout on: CFR ≤ baseline AND RBI ≤ 1.2 AND Escape Rate < 0.5%

### Month 3 — Broader Rollout + Optimization
- If pilot gates pass: expand to 60% of engineers
- Publish dashboard to all engineering leadership
- Monthly calibration: adjust tooling scope, prompting guidelines, or review requirements based on metric trends

---

## Connection to Trust by Design Risk Tiers

AI developer tooling isn't all the same risk level:

| Use Case | Risk Tier | Measurement Focus |
|---|---|---|
| Autocomplete / boilerplate | Tier 1 | Acceptance Rate, velocity |
| Test generation | Tier 1 | CFR, test coverage delta |
| Code review assistance | Tier 2 | Review Burden Index |
| Architecture suggestions | Tier 2 | Senior review rate, CFR |
| Security code generation | Tier 3 | Hallucination Escape H4, mandatory senior review |
| Autonomous PR creation (agentic) | Tier 3/4 | All 7 metrics + HITL gate before merge |

Agentic AI that creates and merges its own PRs without human review is Tier 4. No organization is ready for this until Metrics 3 and 7 are below threshold for at least 90 days of supervised agentic operation.

---

## Worked Example — Realtor.com AmpAI Context

AmpAI serves 800+ internal engineers across four pillars: LLM platform/governance, AI dev tooling (Claude Code, Windsurf), agentic AI solutions, and AI Hub/Champions enablement.

**How this framework applies:**

Week 1: Baseline measurement across all 800+ engineers — current Deployment Frequency, Lead Time, CFR, and MTTR without AI assistance. Survey on review burden.

Week 2-4: Tag Claude Code and Windsurf-assisted PRs automatically via pre-commit hook that detects AI tool signatures in git history.

Month 2: Dashboard live. AI Champions program (pillar 4) becomes the distribution channel for metric results — Champions get their team's data and are accountable for healthy metrics.

Month 3: If CFR is at baseline and RBI < 1.2 — expand tooling access. If either is worse — restrict scope to lower-risk use cases and investigate.

**The executive story:** "We went from 'we think AI tooling is making engineers faster' to 'AI-assisted PRs deploy 2.3x more frequently with the same failure rate and 15% lower review burden.' That's a defensible business case for continued investment."

---

## Related Artifacts

- `01-risk-tiering-framework.md` — Risk classification for AI developer tools by use case
- `03-agentic-ai-threat-model.md` — Threat model for agentic coding assistants
- `04-evaluation-framework.md` — Pre-deployment gates before rolling out AI tooling

---

*Part of the Trust-by-Design Toolkit by Guru Krish. © 2026 Guru Krish. All rights reserved. This work may be shared with attribution for non-commercial purposes. For consulting or commercial use, contact gurukrish81@gmail.com*
