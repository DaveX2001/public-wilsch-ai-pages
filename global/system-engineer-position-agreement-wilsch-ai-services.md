---
publish: true
---

# System Engineer Position Agreement - Wilsch AI Services

[[life-vision]]

Position Agreement for the System Engineer position at Wilsch AI Services. Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 15; E-Myth Writing Position Agreements guide.

---

## Position Identification

| Field | Value |
|-------|-------|
| **Position Title** | System Engineer |
| **Reports To** | COO/President |
| **Direct Reports** | Autonomous Improver (AI) × N (future) |

---

## Result Statement

**Define the result this position is accountable for producing:**

> To ensure AI behavior continuously improves through building evidence-handling systems (e.g., conversation extraction, behavior flagging, transcript ingestion), diagnosis of behavior issues from session evidence, systematic artifact fixes (skills, commands, hooks, protocols/personas, plugins, external tool configs), and validated deployment — so that every position in the organization has an AI environment that works the way the company works.

---

## Work Listing

**List all strategic and tactical work for which this position is accountable.**

### Strategic Work (ON the position)

1. Design and extend the evidence pipeline — new sources (e.g., SDK agents, transcripts, emails), new routing mechanisms
2. Design and maintain the CCI board structure — label taxonomy, epic organization, position interaction model, routing rules for how other positions submit and receive from CCI
3. Evaluate artifact types — determine when new artifact types or mechanisms are needed for shaping AI behavior
4. Plan infrastructure improvements that make the operating environment easier to use for all positions
5. Evaluate behavior improvement proposals from AI diagnosis
6. Improve the three-session model itself (flag → improve → verify)

### Tactical Work (IN the position)

1. Run the three-session improve-system cycle (diagnose → fix → verify)
2. Write/fix instruction artifacts (skills, commands, hooks, protocols/personas, plugins, external tool configs) using manage-artifact methodology
3. Build and maintain evidence-handling tooling (conversation-reader, flag-for-improvement, transcript agents)
4. Triage incoming CCI issues — label by affected position, link to parent epics, verify evidence quality
5. Quality gate CCI issues — ensure issues have sufficient evidence and correct diagnosis before execution begins
6. Close resolved CCI issues — review stale issues periodically, close completed work
7. Infrastructure smoothing (Docker scripts, CLI tools, server configs, README standards)
8. Validate behavior changes in fresh sessions before considering done

---

## BDP Loop (Working ON the System Engineer Position)

**This section defines the current improvement cycle for this position. It evolves with use — as new friction is discovered and new tools emerge, these steps update.**

| Step | Current Practice | Tool |
|------|-----------------|------|
| **Innovation (Identify)** | Receive flagged behavior issues from all positions. Proactively identify behavior gaps from CCI patterns. | `/flag-for-improvement`, CCI board review, conversation-reader |
| **Innovation (Solve)** | Diagnose root cause from session evidence. Determine artifact type. Design the fix. | `/improve-system` → conversation extraction → `/rubber-duck` → clarity phases → `manage-artifact` |
| **Quantification** | Blind verification — trigger same scenario in a fresh session without the AI knowing it's a test. Compare behavior before/after. | Session C (fresh project session), before/after behavioral comparison |
| **Orchestration** | Deploy the fix. Update the artifact. The fix becomes the new standard behavior. | `git push` to main, CCI issue closed |

---

## Standards

**List quantity, quality, and behavior standards for which this position is accountable.**

### Position-Specific Standards

1. **No fix ships without blind verification.** The same scenario must be triggered in a fresh session where the AI doesn't know it's being tested. Deployed ≠ Done. Blindly verified = Done.

2. **Evidence must be captured and connected to an issue at flagging time.** Every flagged behavior issue must include the conversation path, linked to the CCI issue at the moment of discovery. An issue without evidence is nearly impossible to fix — it is extremely hard for a human to explain what's going wrong. The AI needs to see the conversation to understand the problem.

3. **All CCI issues must have evidence at creation time** — conversation path, transcript link, or equivalent. Issues should preferably collect additional evidence over time as more instances of the behavior are observed.

4. All CCI issues must be labeled by affected position and linked to parent epic within 2 business days of creation.

4. **All artifact fixes must include an evidence path or equivalent.** Every artifact change (skill, command, hook, protocol/persona, plugin, external tool config) must link to the evidence that motivated the change — conversation path, transcript URL, or issue reference.

5. Infrastructure changes must be documented before deployment.

### Company-Wide Standards

1. All work will be performed using documented systems (Operations Manual)

2. All work will be orchestrated and quantified when appropriate

3. COO/President will be notified of issues that cannot be resolved, or deadlines that cannot be met, before the deadline has arrived

---

## Manager's Commitment

**What the COO/President commits to provide:**

### Working Environment
- Claude Code license
- Access to all repos, servers, CCI board, and project infrastructure
- Access to conversation logs across all positions (for evidence extraction)

### Necessary Resources
- Operations Manual (documented systems to follow)
- Prioritized improvement backlog — COO/President determines which position improvements align with current business goals

### Accountability Partnership
- Regular system review discussions — what's working, what's not
- Business goal alignment — ensure improvements address real business needs, not just technical goals
- Priority appointment — the COO/President decides which positions' improvements matter most right now

### Appropriate Training
- Onboarding to the three-session model and artifact methodology
- Ongoing access for escalation

---

## Signatures

### Statement of the position holder:

> I accept the accountabilities of this position and agree to produce the results, perform the work and meet the standards set forth in this Position Agreement.

| Field | Value |
|-------|-------|
| **Date** | _________________ |
| **Signature** | _________________ |
| **Printed Name** | _________________ |

### Statement of the position holder's manager:

> I agree to provide a working environment, necessary resources, and appropriate training to enable the accountabilities of this position (result, work, standards) to be accomplished.

| Field | Value |
|-------|-------|
| **Date** | _________________ |
| **Signature** | _________________ |
| **Printed Name** | _________________ |

---

## Related Documents

- [Primary Aim - Life Vision](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)
- [Strategic Objective - Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Organization Chart - Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router)
- [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router)
- [/improve-system Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture)
- [Position Build Process](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/position-build-process)

---

## Source

- [E-Myth Management Strategy interview (2026-02-16)](/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/e836f995-00f3-4b6b-8e23-6e52a4cf6413.jsonl)
- [Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 15](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-writing-position-agreements)
- [Template: E-Myth Writing Position Agreements guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-writing-position-agreements)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

