---
publish: true
---

# Evidence Chain System — Meeting Agenda

[[claude-code-sdk-agents]]

## Meeting Goal

Resolve the Phase 2 REVIEW mechanism for the Evidence Chain System. Phase 1 (PULL) is fully defined — on-demand capture via `/flag`. Phase 2 is the proactive complement: how accumulated evidence gets reviewed for patterns across agent epics.

1. **Review mechanism design** — trigger, format, and surface for periodic evidence review

## Pre-Read

- [Evidence Chain System Design](evidence-chain-system-design) — Parts 1-4 (resolved), Part 5 (this meeting's scope)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Phase 2 REVIEW — periodic evidence review across agent epics
⏱️ 15 min

Phase 1 captures individual agent failures on-demand. Over time, evidence comments accumulate on per-agent position epics in CCI (#628 Fireflies, #632 Transcript, etc.). Phase 2 is the proactive complement — stepping back to review patterns across all agents.

- Evidence volume is likely sparse: 2–5 traces per agent per 48 hours, not every agent fails every week
- The review output is already defined: sub-issues when patterns warrant action (Part 2)
- Available surfaces: GitHub (read epics directly), WhatsApp (Moltbot digest), Claude Code (`--sdk-url` for interactive review)
- Three decisions are coupled: trigger (scheduled vs. on-demand), format (raw comments vs. cross-agent summary), surface (where the review happens)

**To resolve:** The review rhythm and interface — whether evidence review is a scheduled push, an on-demand pull, or simply reading the existing epic comments directly.

## Meeting Format

- **Type:** Discovery
- **Expectation:** Familiarity with how evidence accumulates on agent epics (Part 1-2 of design doc)
- **Outcome:** Defined review mechanism ready to write into Part 5 of the design doc

## Related

- **Issue:** [#851: Evidence Chain System](https://github.com/DaveX2001/deliverable-tracking/issues/851)
- **Design Doc:** [Evidence Chain System Design](evidence-chain-system-design)
