---
publish: true
---

# Monday Meeting — Step 3 Demo + Field Recommendations
[[client-archibus]]

## Meeting Goal

1. **Demo the pipeline** — show the full Step 3 flow (raw Excel → structured JSON → inserted into BruceBEM) across all buildings
2. **Validate AI Recommended fields** — agree on which optional fields AI should proactively suggest to implementers
3. **Signal known gaps** — existing-database scenario and bulk update are on the roadmap, designed after Step 1+2

## Pre-Read

- [Step 3 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design) — current state of the pipeline design
- [AssetImportDescription — Fields Assets](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) — authoritative field reference

---

## Part 1: Demo

### Step 3 Pipeline — Live
⏱️ 10 min

**Narrative:** The implementer gives us messy Excel. Buildings, floors, and rooms aren't separate rows — they're buried in columns. AI reads the data, creates the structure, and inserts everything into BruceBEM with correct hierarchy. One command.

**Sequence:**
1. Marius: show raw Excel (the implementer's starting point)
2. Marius: show the transformation (fuzzy → structured — the value)
3. Marius: execute live push for all buildings (`--all` flag)
4. Rein: navigate BruceBEM UI — buildings, floors, rooms, equipment with correct hierarchy

**Post-demo:** Invite feedback. "What would make this more useful for your implementations? What data do you work with?"

---

## Part 2: Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. AI Recommended fields — which optional fields should AI flag when missing
⏱️ 10 min

The API requires only Name and AssetType. Everything else is optional. But some fields matter enough that AI should warn the implementer when their data doesn't include them — "Your building data has no address. Consider adding one."

- Four fields identified as starting set: **Address** (buildings), **MainContact** (locations), **Area** (locations), **Manufacturer/BrandSpecific** (equipment)
- Address has a dual purpose: data quality AND future duplicate detection against existing buildings
- The warning is a suggestion, not a blocker — implementer decides

**To resolve:** Whether AI should proactively suggest missing fields to implementers at all — and if yes, which fields to start with.

### 2. Example data from different implementations
⏱️ 5 min

The pipeline currently works with one sample dataset (cafm-asset-upload-sample, 201 rows, 4 buildings). To make Step 2 (column mapping) robust, we need variety — different column schemas, different hierarchy depths, different data quality levels.

- Need 2-3 more Excel files from real implementations
- Different clients have different column naming conventions — that's what makes Step 2 hard
- Target: Ryan, Mujahid, Ali

**To resolve:** Who sends which example files, and by when.

### 3. Known gaps — existing buildings and bulk update
⏱️ 5 min

Two scenarios the team should be aware of. Neither is in scope for the current demo, but both are on the design roadmap.

- **Existing buildings:** When a building already exists in BruceBEM and the implementer uploads new equipment for it, the system must attach children to the existing parent — not create a duplicate. Address is the match key. Designed as part of Step 2.
- **Bulk update:** Mujahid's request — update fields on existing assets via Excel upload. This is Chain 3 (Data Freshness) scope, not Chain 1B.

**To resolve:** Team awareness and any early feedback on priority or constraints.

---

## Meeting Format

- **Type:** All-hands demo + review
- **Duration:** ~30 min
- **Expectation:** Participants come having glanced at the design doc (link above). Think about which fields matter most in your implementations.
- **Outcome:** Validated recommended field list, example data commitments, team aligned on roadmap gaps

## Related

- **Issue:** [#373: AI Bulk Data Entry](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Epic:** [#838: AI Bulk Data Import Pipeline](https://github.com/DaveX2001/deliverable-tracking/issues/838)
- **Design Doc:** [Step 3 Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/719f17b2-3360-4099-a3ba-ffe732076e46.jsonl`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

