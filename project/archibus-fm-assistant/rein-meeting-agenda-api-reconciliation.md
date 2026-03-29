---
publish: true
---

# Rein Meeting Agenda: API Reconciliation
[[client-archibus]]

## Meeting Goal

Align design doc assumptions with API reality. Five items surfaced during API testing (Feb 19) where actual behavior differs from documented decisions.

1. **Confirm or propose** — id field enforcement for the backpressure correction loop
2. **Confirm or update** — 4 behavioral differences between design doc and API

## Pre-Read

- [Step 3 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design) — updated Feb 19 with API testing findings

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Id field enforcement for error traceability
⏱️ 5 min

API testing (Feb 19) confirmed that ValidateAssets accepts payloads where nodes have no `id` field. When validation fails on a node without an id, the error response returns `id: null` — making it impossible to locate the failing node in a nested tree.

- With `id: "TEST-ROOM"` on a failing child → `error.id = "TEST-ROOM"` → correction loop can locate and fix the node
- Without id on the same child → `error.id = null` → correction loop is blind (which of 18 rooms failed?)
- The correction loop requires id-based node lookup to function — this is the bridge between error response and JSON tree
- Currently the API silently accepts missing ids and returns unlocatable errors

**To resolve:** Whether `id` should be a required field for ValidateAssets payloads, so that stop-at-first-error includes "missing id" as a validation error before any field validation runs.

### 2. ParentId derivation from nesting
⏱️ 5 min

API testing confirmed: the server auto-generates UUIDs for every node and auto-wires ParentId from the `children[]` nesting structure. AI does not need to provide Id or ParentId — the tree structure alone is sufficient.

- Design doc originally specified ParentId as AI Required (must-have)
- API testing (Feb 19): sent nested payload without any Id/ParentId → server wired all relationships correctly
- If AI sends a string Id (e.g., "PROP-001"), server maps it to OtherCode, not Id
- However: error responses reference the node's Id. Without Ids, the error can't locate the failing node:

  **With id provided** (payload sent `id: "FLR-001"` on the failing child):
  ```json
  {"id": "FLR-001", "field": "AssetType", "value": "InvalidFloorType", "reason": "Invalid AssetType"}
  ```

  **Without id** (no id on the failing child):
  ```json
  {"id": null, "field": "AssetType", "value": "InvalidFloorType", "reason": "Invalid AssetType"}
  ```

**To resolve:** Whether ParentId should still be provided explicitly despite the server deriving it from nesting. Additionally: string Ids appear necessary for error traceability in the backpressure loop — confirm this is the intended error identification mechanism.

### 3. brandSpecific and modelSpecific field mapping
⏱️ 5 min

The API schema includes `brandSpecific` and `modelSpecific` fields (string, nullable). The design doc (Feb 11 meeting) decided "Manufacturer → StatusDetail overflow" because no manufacturer field existed at the time.

- These fields appear to map directly to Manufacturer/Model from client data
- Current decision: overflow to StatusDetail
- If these are the intended mapping: the overflow decision changes (Step 2 concern)

**To resolve:** Whether brandSpecific and modelSpecific are the intended fields for Manufacturer and Model data, replacing the StatusDetail overflow decision.

### 4. OspId preserved behavior
⏱️ 3 min

API testing revealed that OspId preserves any value the caller sends, unlike OwnerId (overwritten from auth) and OtherCode (overwritten from Id field).

- OwnerId: auto-populated from authenticated user → confirmed
- OtherCode: auto-populated from Id field → confirmed
- OspId: preserves caller-provided value → unexpected

**To resolve:** Whether OspId preservation is intentional behavior or a bug, and whether the AI EXCLUDE list (omit all three) is the correct defensive approach.

### 5. ValidateAssets workflow intent
⏱️ 5 min

Both ValidateAssets and ImportAssets accept the same payload and return the same error format. ImportAssets validates internally before inserting (stop-at-first-error confirmed). ValidateAssets never persists.

- The AI pipeline can either: validate first via ValidateAssets → then import, or skip directly to ImportAssets
- ImportAssets rejects invalid data with the same error format — no garbage gets through either way
- The practical difference: ValidateAssets is a risk-free iteration loop

**To resolve:** Whether the intended workflow is ValidateAssets loop → ImportAssets, or direct ImportAssets (since it validates internally).

## Meeting Format

- **Type:** Review / alignment
- **Duration:** ~25 min
- **Expectation:** Rein reviews the 5 topics against his API implementation intent
- **Outcome:** Design doc Undefined markers either resolved or confirmed as intentional

## Related

- **Issue:** [#838 EPIC: AI Bulk Data Import Pipeline](https://github.com/DaveX2001/deliverable-tracking/issues/838)
- **Design Doc:** [Step 3 Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **API Spec:** API reference 2026-02-18.json (provided by Rein)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

