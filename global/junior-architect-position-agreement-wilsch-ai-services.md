# Junior Architect Position Agreement - Wilsch AI Services
[[life-vision]]

---
publish: true
---

Position Agreement for the Junior Architect position at Wilsch AI Services. Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 14; E-Myth Writing Position Agreements guide.

---

## Position Identification

| Field | Value |
|-------|-------|
| **Position Title** | Junior Architect |
| **Reports To** | Solution Architect |
| **Direct Reports** | None (worker level) |
| **AI Mode** | Interactive only — design requires human thinking aided by AI. No autonomous agents. |

---

## Result Statement

**Define the result this position is accountable for producing:**

> To produce complete design docs and epic structures ready for Developer decomposition — through iteratively working through design doc sections, writing what can be defined and converting what cannot into meeting agendas until no undefined sections remain — so that the delivery pipeline maintains continuous flow and clients receive what was aligned upon.

**The design doc is both the output and the diagnostic tool.** Working through each section reveals whether you have enough context to define it. If yes — write it. If no — you need external context — create a meeting agenda. The loop runs: transcript → design doc → uncertainties → meeting agenda → meeting → transcript → design doc → ... until all sections are defined.

**Two artifacts per iteration:**
- **Design doc** — produced by processing transcripts (synthesis of understanding)
- **Meeting agenda** — produced by processing uncertainties (targeting specific unknowns)

**Done test:** "Can I write a meeting agenda with open design questions?" → If NO → design is done.

---

## Work Listing

**List all strategic and tactical work for which this position is accountable.**

### Strategic Work (ON the position)

1. Determine which design doc sections to prioritize for a given project
2. Evaluate which stakeholders need to be involved in which alignment meetings
3. Assess complexity of unknowns to estimate iteration depth
4. Structure the epic for Developer consumption (decomposition approach)

### Tactical Work (IN the position)

1. Process initial transcripts into first design doc draft (creation — the cycle trigger)
2. Work through each design doc section — determine what can be defined vs what requires external context
3. Convert sections requiring external context into meeting agenda discussion topics
4. Attend alignment meetings with client stakeholders
5. Update design doc with resolved decisions after each meeting (iteration, date-versioned)
6. When all sections defined: create epic, link design doc, mark handoff-ready
7. Maintain scope boundaries explicitly in the doc (what's in, what's out)

---

## Standards

**List quantity, quality, and behavior standards for which this position is accountable.**

### Position-Specific Standards

1. Each design doc section must have a clear state — **Defined** (content written in full) or **Undefined** (content cannot be defined with current understanding, marked with `**Undefined:**` inline marker routing to meeting agenda). The design doc itself is the state: if a section is written → Defined; if it has an Undefined marker → Undefined. No separate tracking labels.
2. Meeting agendas must target specific uncertainties — no "general catch-up" meetings. Organizational discussion is SA/VP territory.
3. Design docs are versioned via Git commit history. No version strings in document content. Source section records session references for chronological traceability.
4. Handoff requires two gates passed:
   - **(a) Design Complete:** Zero sections requiring external context remain. Test: "Can I write a meeting agenda with open design questions?" → must be NO.
   - **(b) Handoff Ready:** All context captured within the epic — Developer needs nothing from outside. Design doc follows 4-section structure: **Problem Statement** → **Success Definition** → **Approach** → **Source**. Context includes: problem statement, technical decisions, data schemas, scope boundaries, client agreements, dependencies, evaluation criteria, and source material links.
5. Scope boundaries (in/out) must be explicitly documented in every design doc.
6. Escalate to Solution Architect when alignment cannot be achieved after 2 meeting iterations on the same uncertainty.
7. Always bring a prepared meeting agenda into every meeting — prevents topic sprawl and ensures meeting time targets specific unknowns. No meeting without an agenda.

### Company-Wide Standards

1. All work will be performed using documented systems (Operations Manual)
2. All work will be orchestrated and quantified when appropriate
3. Flag friction in the Knowledge Extraction process to Solution Architect via `/flag-for-improvement`
4. Immediate manager (Solution Architect) will be notified of blockers before deadlines

---

## Manager's Commitment

**What the Solution Architect commits to provide:**

### Working Environment

- Claude Code license (interactive mode)
- Access to Fireflies transcripts (meeting recordings)
- Access to hippocampus (design doc publishing + GH Action workflows)
- Access to GitHub Issues (DaveX2001/deliverable-tracking for epic creation)
- Access to Google Workspace MCP

### Necessary Resources

- Initial transcript(s) for each project (the trigger for the cycle)
- Design doc templates (4-section structure via hippocampus skill + design-doc-workflow reference)
- Operations Manual for the Knowledge Extraction process (to be built — Stage 2)

### Appropriate Training

- Onboarding period with SA shadowing (same training method from Strategic Objective — up to 25 issues, should click by 10)
- Daily grooming sync during training phase

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

---

## Related Documents

- [Primary Aim - Life Vision](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)
- [Strategic Objective - Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Organization Chart - Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- [Organizational Strategy (Component #3)](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organizational-strategy-lifecycle)
- [Position Build Process](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/position-build-process)
- [Developer Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services)
- [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router)
- [EMyth Writing Position Agreements](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-writing-position-agreements)

---

## Source

- E-Myth Organizational Strategy interview (2026-02-07) — Junior Architect Position Contract
- Evidence: #373 (Archibus Chain 1B), #513 (AVO Sourcing), #629 (Rekers Workshop 2)
- Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 14
- Template: E-Myth Writing Position Agreements guide

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

