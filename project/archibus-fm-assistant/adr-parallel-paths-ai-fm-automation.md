---
publish: true
---

# ADR: Parallel Paths for ARCHIBUS AI FM Automation
[[client-archibus]]

## Status
**Created** | Date: 2026-01-24

## Context
- AI-assisted FM automation spans both broad framework capabilities and specific quick wins
- Framework work (#373) depends on external schema understanding (Rein meeting)
- Marketing needs demo content for client engagement now
- Quick wins (#591 CCI codes, #605 Equipment codes) have no schema dependency
- Different stakeholders drive different tracks (capability vs marketing)

## Decision
We will separate framework development from marketing quick wins as **parallel tracks** with independent dependencies and timelines.

**Framework Path (Parent: #267 Value Discovery):**
- Broad capabilities requiring schema understanding
- Proceeds when external dependency resolves
- Examples (not exhaustive): #373

**Quick Wins Path (Parent: #594 Marketing):**
- Marketing-driven demos
- No schema dependency - proceed immediately
- Workflow: Spec-design → David spec-implement → SDK Agent
- Examples (not exhaustive): #591, #605

## Consequences

### ✅ Positive
- Quick wins proceed without waiting for framework blockers
- Marketing has demo content for client engagement
- Framework work continues at its own pace when ready
- Clear ownership: marketing owns quick wins timeline

### ❌ Negative
- Two coordination surfaces instead of unified track
- Potential divergence in implementation approaches

### ⚪ Neutral
- Different implementation paths (framework vs SDK agents)
- Requires explicit communication when paths reconnect

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Unified track** | Blocks all progress on schema dependency |
| **Quick wins only** | Loses broader framework capabilities |
| **Sequential (framework first)** | Delays marketing demos indefinitely |

---

**Key Resources:**
- **Framework Path Parent:** #267 Value Discovery Methodology
  - Example: #373 AI Bulk Data Entry Prototype
- **Quick Wins Path Parent:** #594 YouTube Marketing Funnel
  - Examples: #591 CCI codes, #605 Equipment codes

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

