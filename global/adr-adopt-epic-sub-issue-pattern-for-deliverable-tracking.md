---
publish: true
---

# ADR: Adopt Epic/Sub-Issue Pattern for Deliverable Tracking
[[team-scaling]]

## Status
**Created** | Date: 2025-12-10

## Context
- Deliverables tracked as flat issues lack relationship visibility
- Parallel work blocked when issues don't have clear boundaries
- Project views cluttered with scattered, unrelated issues
- Dependencies between work items not explicitly documented
- No clear completion criteria for large bodies of work

## Decision
We will use parent epic issues with atomic sub-issues linked via task lists for deliverable tracking.

### Epic vs Sub-Issue

| Aspect | Epic | Sub-Issue |
|--------|------|-----------|
| **Nature** | Outcome with end state | Atomic work unit |
| **Criteria** | "What does done look like?" | "Can I verify this independently?" |
| **Dependencies** | Documents relationships between sub-issues | Independent - no blocking on siblings |
| **Work style** | Tracks overall progress | Single worktree/terminal |

**Epic = Outcome Container**
- Has explicit acceptance criteria and end state
- Groups related work that achieves one business outcome
- Tracks progress across sub-issues
- Documents which sub-issues block others

**Sub-Issue = Atomic Work Unit**
- Can be completed and validated independently
- Fits in a single worktree
- Has binary done/not-done state

### Why Atomic Parallelizable Units?

With multiple terminals open, each running a worktree, you can work multiple sub-issues simultaneously if they're truly independent. This requires:
- No runtime dependencies (A doesn't need B running to test)
- Independent verification (can validate A without B)

**Example:** [DaveX2001/deliverable-tracking#36](https://github.com/DaveX2001/deliverable-tracking/issues/36)

## Consequences

### ✅ Positive
- Parallel execution in separate worktrees/terminals
- Clear scope boundaries for each work unit
- Relationship visibility (siblings, dependencies)
- Clean project view (one epic row vs many scattered issues)

### ❌ Negative
- Requires discipline to define acceptance criteria upfront
- Risk of "junk drawer" epics without end state

### ⚪ Neutral
- Dependency documentation moves to epic level

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Flat issue structure** | No relationship visibility, clutters project view |
| **Labels only** | Doesn't capture dependencies or hierarchy |
| **Milestones** | Time-boxed, not outcome-oriented |

---

**Key Resources:**
- [Atlassian: Epics, Stories, and Initiatives](https://www.atlassian.com/agile/project-management/epics-stories-themes)
- [GitHub Community: Handling Epics](https://github.com/orgs/community/discussions/7267)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

