---
publish: true
---

# Dev Lead Position Agreement - Wilsch AI Services

[[life-vision]]

Position Agreement for the Dev Lead position at Wilsch AI Services. Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 14; E-Myth Writing Position Agreements guide.

---

## Position Identification

| Field | Value |
|-------|-------|
| **Position Title** | Dev Lead |
| **Reports To** | VP/Delivery |
| **Direct Reports** | Developer (Result: deliver working solutions to staging that meet design specifications) |

---

## Result Statement

**Define the result this position is accountable for producing:**

> To ensure Developers successfully navigate the [Issue Lifecycle](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) from creation through closing — through system adherence verification, spec quality review, human witness on staging, daily grooming, training, and continuous improvement of the Developer Operations Manual and Position Agreement — so that the delivery pipeline flows without founder intervention and each Developer iteration is more effective than the last.

The Developer owns the **output** (working solution). The Dev Lead owns the Developer's **ability to produce that output consistently through the system.**

---

## Work Listing

**List all strategic and tactical work for which this position is accountable.**

### Strategic Work (ON the position)

1. Analyze Developer conversations to identify system adherence gaps and learning patterns
2. Improve the Developer Operations Manual based on friction discovered
3. Update the Developer Position Agreement when the role evolves
4. Evaluate Developer performance and fit (personality-role alignment)
5. Provide input to VP/Delivery on delivery progress and team capacity
6. Recommend training adjustments based on Developer progression

### Tactical Work (IN the position)

1. Review DoD + AC quality before grooming — verify decomposition before execution begins
2. Support Developers through training; verify system adherence; answer escalated questions (after Developer exhausts 3 approaches per their PA standard)
3. Spec quality review — verify decomposition and approach meet design specifications
4. Human witness on staging — verify deployed solution works as designed
5. Conduct daily grooming sync with each Developer (Google Meet, 15-30 min)
6. Check Dev Lead Review Queue (dedicated GitHub Project, table layout) before each grooming — process review items FIFO
7. Track delivery progress across all active projects
8. Route Developer escalations to the appropriate position (SA for design gaps, System Engineer for AI behavior)
9. Maintain CCI board hygiene — issues labeled per position, organized into epics, completed work closed

---

## BDP Loop (Working ON the Developer Position)

**This section defines the current improvement cycle for this position. It evolves with use — as new friction is discovered and new tools emerge, these steps update.**

| Step | Current Practice | Tool |
|------|-----------------|------|
| **Innovation (Identify)** | Analyze Developer conversations and work output to spot friction — "what's not working in their process?" | conversation-reader, PR review, grooming observations |
| **Innovation (Solve)** | Design process improvements — "how should it work instead?" | `/flag-for-improvement` → `/improve-system` |
| **Quantification** | Verify the fix — "did the change reduce friction?" Compare Developer behavior before/after | Before/after comparison across Developer sessions |
| **Orchestration** | Codify into Developer Operations Manual + Position Agreement. The fix becomes the new standard. | `git push` to main |

---

## Standards

**List quantity, quality, and behavior standards for which this position is accountable.**

### Position-Specific Standards

1. All Developer code must pass spec quality review + human witness on staging before merge to staging. Merge to main requires additional human witness on main. Zero self-merges by Developers.

2. Spec-design output must be reviewed and commented on **before** grooming — grooming is for discussion of reviewed output, not first review. Review means: read, form opinion, add written comments.

3. Flag-for-improvement items must be triaged and routed within 2 business days of identification:

   | Friction Type | Route To | Action |
   |---------------|----------|--------|
   | **Developer process/workflow** | Dev Lead (self) | Update Developer Operations Manual |
   | **AI behavior / system wrong** | System Engineer | Create CCI issue, label per position |
   | **Design methodology gap** | Solution Architect | Update design doc methodology |
   | **Tooling / infrastructure** | System Engineer | Create CCI issue |

4. Daily grooming sync happens every working day (Google Meet, 15-30 min). Grooming agenda: review spec-design comments, discuss blocked issues, verify Developer is following system.

5. CCI board: every improvement issue must be labeled per position, linked to its parent epic, and closed within 5 business days of merge. No orphaned issues.

6. Developer conversation review must happen before each grooming — the Dev Lead reads the Developer's most recent session to verify system adherence and identify coaching points.

7. Dev Lead Review Queue must be checked and processed before each grooming. Review items older than 2 business days without action = escalation signal (Developer blocked or Dev Lead behind).

### Company-Wide Standards

1. All work will be performed using documented systems (Operations Manual)

2. All work will be orchestrated and quantified when appropriate

3. Flag friction in the delivery process via `/flag-for-improvement`

4. VP/Delivery will be notified of issues that cannot be resolved, or deadlines that cannot be met, before the deadline has arrived

---

## Manager's Commitment

**What the VP/Delivery commits to provide:**

### Working Environment
- Claude Code license
- Access to Developer conversation logs (conversation-reader)
- Access to repos, staging environments, CCI board
- Dev Lead Review Queue (dedicated GitHub Project with auto-add on `label:review`)
- Design docs delivered before delegation to Developers

### Necessary Resources
- Developer Operations Manual (documented systems to follow)
- Developer Position Agreement (standards to enforce)

### Appropriate Training
- Onboarding with VP/Delivery shadowing
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
- [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router)
- [Developer Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services)
- [Developer Operations Manual](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services)
- [Position Build Process](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/position-build-process)

---

## Source

- E-Myth Organizational Strategy interview (2026-02-15)
- Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 14
- Template: E-Myth Writing Position Agreements guide
- Evidence: Issue #712 (Operations Manual update friction), Issue #805 (personality profiling)
- Dev Lead observability extraction pass (2026-02-17) — gate signal mechanism, review queue design
- Session: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/478e31b8-5f19-4f85-b370-7fa437440795.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

