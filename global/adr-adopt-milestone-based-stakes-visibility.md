---
publish: true
---

# ADR: Adopt Milestone-Based Stakes Visibility for Deliverable Tracking
[[team-scaling]]

## Status
**Created** | Date: 2025-12-12

## Context
- Tasks in deliverable tracking lack visibility into their monetary value
- Prioritization is difficult without knowing which work leads to payment
- Need to answer: "Which tasks directly generate revenue?"
- Existing forcing function doc defines WHY stakes visibility matters (see `stakes-visibility-forcing-function.md`)
- GitHub milestones are native, underutilized infrastructure

## Decision
We will use a three-layer model for stakes visibility:

**Layer 1: Milestone = Contract**
- Each milestone represents a revenue-generating contract or deliverable
- Title format: `[CLIENT] Brief description €Value`
- Description: Expanded context (status, payment trigger, notes)
- GitHub auto-calculates progress % as issues close

**Layer 2: Contract Task = Gatekeeper**
- Each milestone contains a contract/signing task
- Open = contract pending, Closed = contract signed
- Work tasks are blocked until gatekeeper closes

**Layer 3: Work Tasks = Inherit Readiness**
- Tasks in milestone = leads to money
- Tasks not in milestone = doesn't directly lead to money
- Blocked label indicates specific task-level blockers

## Consequences

### Positive
- Instant visibility: see €value on any task
- Auto progress tracking without manual updates
- No new infrastructure (native GitHub features)
- Clear prioritization signal for evening shutdown ritual

### Negative
- €value encoded as text in title (no typed field)
- Contract status requires manual gatekeeper task management
- Milestones are repo-scoped (no cross-repo grouping)

### Neutral
- Existing blocked label system continues unchanged
- Requires discipline to create contract gatekeeper task per milestone
- Milestone descriptions available for additional context if needed

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Labels only (e.g., `revenue`)** | Binary visibility only, no grouping or progress tracking |
| **GitHub Projects custom fields** | Overkill for current scale, adds complexity |
| **External tool (Notion, Asana)** | Fragments workflow, loses GitHub integration |
| **Title prefixes on issues** | No grouping, clutters issue titles |

---

**Key Resources:**
- [[stakes-visibility-forcing-function]] - The WHY (forcing function framework)
- [GitHub Milestones Docs](https://docs.github.com/en/issues/using-labels-and-milestones-to-track-work/about-milestones)
- `gh milestone` extension: `gh extension install valeriobelli/gh-milestone`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

