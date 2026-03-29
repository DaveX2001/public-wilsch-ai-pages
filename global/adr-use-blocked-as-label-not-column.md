---
publish: true
---
# ADR: Use Blocked as Label Overlay, Not Board Column
[[team-scaling]]

## Status
**Created** | Date: 2025-12-12

## Context
- Kanban boards need a way to represent "blocked" or "waiting" work items
- Two approaches exist: dedicated "Blocked" column vs label/flag overlay
- Industry research shows blocked columns cause workflow visibility issues
- Items in blocked columns lose context about where they were in the pipeline
- Teams often start new work when items block, inflating actual WIP

## Decision
We will use `blocked` as a **label overlay** that can be applied to items in any workflow column, rather than creating a dedicated "Blocked" column.

Items stay in their current column (Backlog, To-Do, In-Progress) with the `blocked` label applied. When unblocked, remove the label - no column movement needed.

## Consequences

### ✅ Positive
- Items retain workflow position and priority context
- When unblocked, immediately ready in correct stage
- Columns represent progress, labels represent temporary state
- Blocked items count toward WIP limits (accurate capacity view)
- Easier to see WHERE in pipeline blockers accumulate

### ❌ Negative
- Requires query filters to exclude blocked items from selection pools
- Less visual separation of blocked work on board view
- Discipline needed to check blocked status across columns

### ⚪ Neutral
- Query syntax changes: add `-label:blocked` to selection queries
- Grooming process checks blocked status in blocked scan step

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Dedicated Blocked column** | Anti-pattern: items become "out of sight, out of mind", lose workflow context, WIP metrics become unreliable |
| **Blocked swimlane** | Adds visual complexity; still separates items from workflow context |
| **No blocked tracking** | Insufficient - need visibility into what's waiting |

---

**Key Resources:**
- [Why Avoid Using Blocked Columns on Kanban Boards](https://www.danieleteti.it/post/why_avoid_using_blocked_columns_on_kanban_boards/)
- [Blocked Columns Obfuscate Workflow - naked Agility](https://nkdagility.com/resources/blog/blocked-columns-on-kanban-boards-obfuscate-workflow-and-undermine-effectiveness/)
- [Jira Flag Documentation](https://support.atlassian.com/jira-software-cloud/docs/flag-an-issue/) - Jira recommends flags over status

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

