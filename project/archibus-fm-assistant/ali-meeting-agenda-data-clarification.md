---
publish: true
---

# Ali Meeting: Data Clarification & Equipment Data Delivery
[[client-archibus]]

## Meeting Goal

Two data artifacts have been received from the ARCHIBUS team, but neither contains the equipment data the AI Bulk Data Import pipeline needs for real-world testing. Ali can clarify what these files are and identify where actual equipment data lives.

1. **Clarify existing data artifacts** — the Housekeeping Tracker (FMM) and Ali's earlier Excel are not equipment data. Understand what they contain and whether any part is usable.
2. **Commit to equipment data delivery** — identify which real client datasets contain asset inventories (serial numbers, models, hierarchy) and agree on a delivery timeline.

## Pre-Read

[Step 1+2 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design) — Part 1 (hierarchy inference) and Part 2 (column mapping) describe what the pipeline expects as input: an Excel with location hierarchy columns (building, floor, room) and equipment attribute columns (serial number, model, manufacturer, status).

The Housekeeping Tracker (FMM, Jan 2026 v1) will be screen-shared during the meeting.

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The Housekeeping Tracker Contains Work Orders, Not Equipment
⏱️ 10 min

The Housekeeping Tracker (FMM, Jan 2026 v1) has five sheets: WO Sheet (work order reports with requestor, priority, description like "DESK CLEANING REQUIRED"), Tracker (work order status tracking by priority tier), shift update/staff list, pending summary, and supporting data. All content relates to housekeeping operations at what appears to be a Qatar airport facility (CONCOURSE A, PTC, DEPARTURE GATE C4).

- No equipment attributes found — no serial numbers, models, manufacturers, or asset types
- Location references exist (AREA, SUB AREA OF FACILITY, LOCATION) but describe work order locations, not an asset hierarchy
- Rein confirmed (Feb 27): "it cannot be used at all" for asset import — possibly employees or work requests
- The file's origin and intended purpose are unclear to both Rein and Marius

**To resolve:** What this file represents in the ARCHIBUS team's workflow, and whether any derivative dataset from the same source contains actual equipment records.

### 2. Real Equipment Data for Pipeline Testing
⏱️ 15 min

The pipeline has been validated against one dataset (CAFM sample, 201 rows, 16 columns, 4 buildings). This is a near-perfect happy case — all columns map cleanly, the hierarchy is obvious, and there are no real edge cases. Real-world validation requires data with unmappable columns, ambiguous hierarchies, and domain-specific naming.

- The CAFM sample was requested and delivered early — it proved the concept but not the robustness
- Additional examples have been requested from the ARCHIBUS team at least three times since January (most recently via Rein → Miguel escalation, Feb 27)
- The pipeline specifically needs: an Excel with equipment rows (serial number, model, manufacturer, asset type, status) plus location hierarchy columns (building, floor, room or equivalent)
- Messier data is more valuable than clean data at this stage — it exercises the AI's inference and the implementer correction loop

**To resolve:** Which real client equipment datasets Ali has access to or can extract, and a delivery date for at least one dataset that exercises the pipeline beyond the happy case.

## Meeting Format

- **Type:** Alignment
- **Attendees:** Marius, Ali
- **Expectation:** Ali brings knowledge of available client datasets and context on the Housekeeping Tracker
- **Outcome:** Clarity on existing data artifacts + committed delivery date for real equipment data

## Related

- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852)
- **Design Doc:** [Step 1+2 Setup Phase](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
- **Parent:** [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
- **Prior Meeting Agenda:** [Step 1+2 Hierarchy & Interpretation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation) — Topics 2+3 overlap (Ali data + client examples)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

