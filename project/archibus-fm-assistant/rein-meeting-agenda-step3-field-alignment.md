---
publish: true
---

# Meeting Agenda: Step 3 — Field Alignment & Edge Cases
[[client-archibus]]

## Meeting Goal

Resolve 8 undefined areas across Step 3 Elements 4, 5, and 6.

1. **Hierarchy edge cases** — Floor All placement + multi-level emptiness (Element 4)
2. **JSON field specification** — naming scheme, country mapping, new fields, missing values (Element 5)
3. **API insertion model** — validate-then-insert proposal + error response format (Element 6)

## Pre-Read

[Chain 1B: Step 3 Fill Level-by-Level — Design Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)

Focus on Approach sections 4, 5, and 6.

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### Element 4: Hierarchy Edge Cases

#### 1. Building-wide equipment has no specific floor

- 33% of Building A rows have Floor = "All" (elevators, fire systems, plumbing)
- "All" is not a specific floor — current design attaches to lowest known level
- Room number on these rows might be access point, not actual location
- Where do these assets attach in the hierarchy?

#### 2. The fallback rule has only been confirmed for one level

- Confirmed: empty room → equipment attaches to floor
- Not confirmed: empty room AND empty floor → does equipment go to building?
- Is the fallback recursive (always climb up until a filled level)?

### Element 5: JSON Field Specification

#### 3. Two naming schemes exist for the same fields

- AssetImportDescription uses PascalCase — current import app standard
- Hierarchical Body JSON uses lowercase — Rein's example for new API
- Conflict fields:

| Field Concept | AssetImportDescription | Hierarchical Body JSON |
|---------------|----------------------|----------------------|
| Asset type | `AssetType` | `type` |
| Serial number | `SerialNumber` | `serial_no` |
| Purchase date | `DatePurchased` | `purchase_date` |
| Manufacturer | **not in schema** | `manufacturer` |
| Model | **not in schema** | `model` |
| Country | `Country` | **not in JSON** |
| Model specific | `ModelSpecific` | **not in JSON** |

- Everything else is just PascalCase vs snake_case — mechanical translation

#### 4. Country values in Excel are names, Bruce BEM stores IDs

- AssetImportDescription Row 18: "Bruce has country IDs, but in Excel normally Country Names"
- Does the insertion API handle the translation internally?
- If not, does a lookup endpoint or table exist?

#### 5. The Hierarchical Body JSON has fields not in the ImportTemplate

- `manufacturer` and `model` appear in Rein's JSON but not in the 36-field schema
- Both fields exist in client source data (CAFM sample has Manufacturer/Model columns)
- Does the new API accept these fields?

#### 6. Empty cells in client data have no defined behavior

- Optional fields may be empty in the client's Excel
- AI-generated JSON needs a rule: omit the field, send null, or use a default?
- Does the API handle missing vs null differently?

### Element 6: API Insertion Model

#### 7. The proposed insertion model is validate-then-insert

- Send full building JSON → API validates → returns first error
- AI corrects → resubmits → repeat until clean → then insert everything
- Nothing inserted until entire payload validates — no partial state
- Proposed error response:

```json
{
  "error": {
    "field": "AssetType",
    "value": "HVAC",
    "reason": "Invalid value. Must match predefined type.",
    "valid_values": ["HVAC equipment", "Air Cooling", "..."]
  }
}
```

*Example — actual format to be defined during API development.*

#### 8. A validation-only endpoint would help development

- Dry-run mode: validate JSON without inserting
- Enables rapid iteration on JSON structure during development
- Nice-to-have, not a requirement

## Meeting Format

- **Type:** Technical review
- **Expectation:** Rein brings clarity on API field format and insertion behavior
- **Outcome:** 8 topics resolved → Step 3 design doc updated → undefined items removed

## Related

- **Issue:** [#373 — ARCHIBUS: AI Bulk Data Entry Prototype](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Design Doc:** [Chain 1B: Step 3 Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

