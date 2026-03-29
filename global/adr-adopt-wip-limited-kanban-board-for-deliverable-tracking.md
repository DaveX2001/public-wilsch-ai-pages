---
publish: true
---

# ADR: Adopt WIP-Limited Kanban Board for Deliverable Tracking
[[team-scaling]]

## Status
**Created** | Date: 2025-12-05

## Context

**Solo Operator Scaling Problem:**
- 7 concurrent clients with 65 open issues in deliverable tracking
- 46 items in To-Do, 12 in In-Progress - no way to prioritize
- No quality gate between "captured" and "ready to execute"
- 19 items missing Definition of Done (contract extraction artifacts with "t.b.d.")
- Cognitive overload when scanning board - can't "pick in the moment" like notebook system

**Research Findings:**
- Context-switching cost: 23 minutes to refocus; 5+ concurrent projects = only 20% time on actual work
- Sustainable client load for solo consultants: 3-6 active clients
- Recommended WIP limit: 2-3 tasks TOTAL (not per-client) - cognitive capacity doesn't scale with client count
- Backlogs over 60 items become unmanageable

**User's Vision:**
- "I just want to execute" - separate deciding from doing
- Board should feel like the notebook: scan, pick, work
- Two hats: Grooming (planning) vs Execution (doing)

## Decision

We will implement a **4-column Kanban board** with strict WIP limits and quality gates:

**Board Structure:**
```
Backlog (unlimited) → To-Do (5-10 max) → In-Progress (2-3 max) → Done
```

**Quality Gates:**
- **Backlog → To-Do:** Item has Definition of Done + selected for near-term work
- **To-Do → In-Progress:** WIP slot available

**Limits:**
- In-Progress: 2-3 items TOTAL across all clients
- To-Do: 5-10 items (scannable for "pick in moment")
- Backlog: Archive if >60 items or untouched for 3 months

**Grooming (Joint, During Daily Sync):**
- Time allocation: 5-10 minutes max
- Focus: Only next 1-3 items that could be pulled
- One Question Test per item: "Is this clear enough to pull tomorrow?"
- 15-minute time-box per item - defer unclear items with research task
- Archive aggressively: Items not prioritized in 3 months get archived

**Execution (Solo):**
- Marius picks from To-Do when WIP slot opens
- "Decide in the moment" - no pre-assigned priority
- All To-Do items are ready to execute (quality gate enforced)

## Consequences

### ✅ Positive

**Reduced Cognitive Load:**
- To-Do limited to 5-10 scannable items vs 46 overwhelming items
- WIP limit prevents 12-way context switching (research: each switch costs 23 min refocus)
- Separation of "ready" vs "needs clarification" items

**Clear Role Separation:**
- Hat 1 (Grooming): Joint decision during daily sync
- Hat 2 (Execution): Solo work, just pick and do
- Matches user's vision: "I just want to execute"

**Quality Enforcement:**
- Definition of Ready gate prevents unclear items polluting To-Do
- One Question Test provides clear exit criterion for grooming
- Archive discipline keeps backlog manageable (<60 items)

### ❌ Negative

**Discipline Required:**
- Must enforce limits even when all clients have urgent work
- Grooming adds 5-10 min to daily sync
- Archive decisions may feel like "giving up" on items

**Capacity Constraint Visible:**
- 2-3 WIP limit makes overcommitment obvious
- May need to say "no" or delay client work
- 7 clients with 2-3 WIP means most clients wait

### ⚪ Neutral

**Board Restructure:**
- Add Backlog column to existing workflow
- Move 19 unclear items out of To-Do
- Establish limit enforcement mechanism

**Process Change:**
- Grooming becomes explicit activity vs implicit
- Joint decision-making for what enters To-Do
- Daily sync structure adapts to include grooming

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Per-client WIP limits** | Doesn't scale - 7 clients × 2-3 each = 14-21 concurrent tasks, still cognitive overload |
| **Priority labels (P0/P1/P2)** | Requires constant re-prioritization; user wants "pick in moment" not pre-sorted lists |
| **David proposes daily priorities** | Creates dependency; user wants autonomy in execution, joint only for grooming |
| **No To-Do limit** | 30+ items not scannable; defeats "notebook feel" of quick pick |
| **Weekly batch grooming** | Delays item readiness; daily sync already exists as touchpoint |

---

**Key Resources:**
- [Personal Kanban WIP Limits](https://kanbantool.com/kanban-library/personal-kanban) - Jim Benson's 3-task recommendation
- [Context Switching Costs](https://www.atlassian.com/blog/loom/cost-of-context-switching) - Research on productivity loss
- [Backlog Refinement Best Practices](https://www.atlassian.com/agile/scrum/backlog-refinement) - Grooming principles
- Related: `change-request-deliverable-tracking-system-2025-11-26.md` - Original operating model

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

