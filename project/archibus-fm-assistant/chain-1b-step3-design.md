---
publish: true
---

# Chain 1B: Step 3 Fill Level-by-Level - Design Document
[[client-archibus]]

Transform a mapped equipment table into nested JSON that Bruce BEM's insertion API can accept, processing one top-level hierarchy element at a time with self-correction on errors.

---

## Problem Statement

**The gap between client data and system schema is the implementer's burden.**

Populating Bruce BEM's background data requires understanding the client's hierarchy, mapping their columns to schema fields, and filling data level-by-level. Step 3 handles the final phase: given a mapped schema (Step 2 output) and a confirmed hierarchy (Step 1 output), transform the client's equipment table into nested JSON that Bruce BEM's insertion API can accept directly.

**Preconditions:**
- Step 1 complete: hierarchy detected and confirmed by implementer (e.g., Building → Floor → Room)
- Step 2 complete: column mapping from client schema to Bruce BEM ImportTemplate fields established
- Input is an equipment table with hierarchy embedded in columns (not location rows)
- Location-based assets (buildings, floors, rooms) must be created from column values — they don't exist as rows in the input

---

## Success Definition

- **Goal:** Equipment table transformed into AI-generated hierarchical JSON that Bruce BEM can insert, with self-correction on validation errors

- **Success:** For each top-level hierarchy element (e.g., 4 buildings = 4 JSONs): generate nested JSON → validate via API → if error, AI corrects and resubmits → insert when clean → move to next element. All elements populated.

- **Done test:** "Can I produce a valid JSON for one building that the API accepts without errors?" → If YES for all buildings → Step 3 is complete

---

## Approach

### 1. Filter by Top-Level Element

For each unique value at the highest hierarchy level, process one element and all its children at a time. This scopes the work: instead of transforming the entire table at once, AI handles one building at a time. Processing one building keeps individual JSON payloads small for faster API validation and avoids transferring the entire dataset in a single request.

```
Equipment table (N buildings)
    │
    ├─→ Run 1: Building A ← process this first
    ├─→ Run 2: Building B
    ├─→ Run 3: Building C
    └─→ Run N: ...
```

**Example — Building A selected (3 of 18 equipment rows, all columns):**

| Asset | Name | Type | Floor | Room | Manufacturer | Model | Serial No. | Purchase Date | Condition | Status | Assigned To |
|-------|------|------|-------|------|-------------|-------|------------|---------------|-----------|--------|-------------|
| A008 | Safety-008 | Safety | All | 116 | LG | FX100 | SN000008 | 2018-05-04 | New | Inactive | Team B |
| A010 | Vertical Trans.-010 | IT Equipment | 3 | 333 | LG | LEDX200 | SN000010 | 2020-03-14 | Poor | Active | Team C |
| A034 | Electrical-034 | Electrical | 1 | 194 | Honeywell | EliteBook | SN000034 | 2019-02-09 | Fair | Active | Team F |

**Reference:** [cafm-asset-upload-sample.xlsx](https://docs.google.com/spreadsheets/d/1CUaybsk8ZnvvKr3C37xn-md96hRLBKT4/edit) — the raw client input before any transformation.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW), [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ)*

### 2. Extract Hierarchy Chain

Each equipment row contains its full parent chain embedded in columns. Read the hierarchy columns left-to-right to extract the location path.

**Assumption:** Step 1 has already confirmed the hierarchy structure (e.g., Building → Floor → Room). Step 2 has established column mappings AND surfaced awareness flags for anomalous values (e.g., "All", "None", "every"). Step 3 receives this awareness and acts — it transforms, corrects, and backfills without re-investigating. The hierarchy is embedded in the row — we do not infer or create it.

**Example — chains from the 3 Building A rows above:**

```
A008: Building A → Floor All → Room 116
A010: Building A → Floor 3   → Room 333
A034: Building A → Floor 1   → Room 194
```

The chain determines where each equipment asset will attach in the hierarchy. All hierarchy values are treated as valid location-based asset names — "All", "G", numbered floors are all processed identically. No values are skipped or special-cased.

**Reference:** Same source file — each row's Location, Floor, and Room columns encode the chain.

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW)*

### 3. Deduplicate Per Level

Extract unique values at each hierarchy level. Multiple equipment rows sharing the same location = one location asset to create. 18 equipment rows might reference Floor 3 five times — but Floor 3 is one location asset, not five. Deduplication extracts the distinct set of parents that need to be created.

**Example — Building A (18 equipment rows):**

| Level | Unique Values | Count |
|-------|---------------|-------|
| Building | Building A | 1 |
| Floor | 1, 2, 3, 4, All | 5 |
| Room (Floor 1) | 194, 494 | 2 |
| Room (Floor 2) | 286, 321, 395, 436 | 4 |
| Room (Floor 3) | 277, 333, 413, 431, 476 | 5 |
| Room (Floor 4) | 235 | 1 |
| Room (Floor All) | 116, 175, 232, 377, 379, 473 | 6 |
| **Total location assets to create** | | **24** |

**Identity rule:** Room identity = parent floor + room number. Room 333 under Floor 3 is a different asset than a hypothetical Room 333 under Floor 2. This rule is structurally necessary — the nested JSON tree requires each room to be a child of exactly one floor. Sample data confirms: Room 431 exists on both Floor 3 and Floor "All" in Building A.

**Reference:** [Processed Excel Table.xlsx](https://docs.google.com/spreadsheets/d/13Q_P-zbFjbN7VIi4YUWi6bPYDuTxeziK/edit) — the data after Step 2 column mapping.

*Source: [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ)*

### 4. Create Location-Based Assets

The source data contains only equipment rows. Location assets (buildings, floors, rooms) are implicit in the hierarchy columns and must be created as new rows with the **same schema** — they're all entries in the same assets table, differentiated by AssetType.

**What AI does:** Extract unique locations from Part 3's dedup output → create new asset rows for each.

**Example — Processed table for Building A (from [Processed Excel Table](https://docs.google.com/spreadsheets/d/13Q_P-zbFjbN7VIi4YUWi6bPYDuTxeziK/edit)):**

| Asset Code | Asset Name | Asset Type | Location |
|------------|------------|------------|----------|
| *A060* | *IT Equipment-060* | *HVAC* | *Building A* |
| *A065* | *Vertical Trans.-065* | *Plumbing* | *Building A* |
| *...* | *(34 equipment rows)* | | |
| **A201** | **Building A** | **Building** | **Exeter rd 25** |
| **A203** | **All** | **Floor** | **Building A** |
| **A204** | **1** | **Floor** | **Building A** |
| **A207** | **G** | **Floor** | **Building A** |
| **A208** | **Room 201** | **Room** | **Building A** |
| **A215** | **Room 194** | **Room** | **Building A** |

*Italic = original equipment rows. **Bold = AI-generated location assets.***

The hierarchy is expressed in the nested JSON structure (Part 5) using generic `children` arrays with explicit Id/ParentId references. Location asset Names are derived directly from the hierarchy column values: Building name from Location column, Floor number from Floor column, Room number from Room column. Property name comes from the client (if hierarchy includes that level).

**Attachment rule:** Equipment attaches to the lowest available hierarchy level. The recursive fallback applies unconditionally — regardless of floor value ("All", "G", empty, or numbered). If room is missing, attach to floor. If floor is missing, attach to building. No building = data error. This rule has no exceptions.

**Floor "All":** Created as-is — it is a real location asset, not a skip signal. Rooms nest under it normally. Equipment with Floor = "All" and a room attaches to the room (which is a child of Floor "All"). In sample data, all 34 Floor "All" rows have a room number.

**Production constraint: existing location assets.** Part 4 assumes a new database — every building, floor, and room is created from scratch. In production, BruceBEM databases already contain location assets from prior imports or manual entry. Adding equipment to an existing building must attach children to the existing parent, not create a duplicate. The dedup mechanism is a Step 2 responsibility: during data review, AI compares the implementer's location data against existing BruceBEM assets. Address is the primary match key for buildings — without it, duplicate detection is not possible. This constraint is foundational: every import after the first one encounters it.

**Undefined:** The dedup mechanism itself — how AI identifies existing assets, confirms with the implementer, and attaches children to existing parents instead of creating duplicates. Two approaches discussed (name-based pre-check via existing API, UUID-based linking via implementer-provided BruceCode). Designed as part of Step 2 after Step 3 PoC is validated. → *[Monday meeting agenda §3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/monday-meeting-agenda-feb24-demo-feedback)*

*Source: [Feb 20 — Rein meeting](https://app.fireflies.ai/view/01KHXD6NN4Y35G0MJWM8NYZJZH) (existing-database scenario), [Rein Teams chat Feb 19-20](~/.claude/hippocampus/project/archibus-fm-assistant/rein-teams-chat-feb19-20.md) (UUID-based pre-check proposal)*

**Reference:** [asset_types 1.xlsx](https://docs.google.com/spreadsheets/d/1Wc1BL18e5Vaxx7bAzsvxAeWojfvxRrWd/edit) — predefined AssetType enum (includes Building, Floor, Room).

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW) (recursive fallback confirmed), [Feb 11 — Rein meeting](https://app.fireflies.ai/view/01KH67KMEA533C0VVJWH7FE63D) (Floor All as-is confirmed, unconditional fallback confirmed)*

### 5. Build Nested JSON

Transform the flat table (equipment rows + generated location assets from Part 4) into nested JSON that the insertion API can accept. Every asset follows the same schema — location assets and equipment are all rows in the same `assets` table, differentiated by AssetType.

#### Field Tiers

The [AssetImportDescription — Fields Assets sheet](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) assigns each of the 36 import fields to a tier. The sheet is the authoritative field-level reference.

**AI Required** — fields the insert will fail without. Name identifies the asset. AssetType classifies it (ENUM — determines hierarchy level for location assets, equipment category for equipment). These two fields are the only ones the API enforces as mandatory. The `children` array expresses parent-child relationships through nesting — the server auto-generates UUIDs for each node and auto-wires ParentId from the tree structure. Id and ParentId are accepted but optional: if AI provides a string Id (e.g., client Asset Code), the server maps it to OtherCode for traceability. If omitted, the server generates both. ParentId does not need to be provided explicitly — the server derives it from the nesting structure. (Confirmed Feb 19 via API testing.)

**AI Optional** — all other AI-filled fields. The rule: if the client's source data has a value, map it. If not, omit the field from JSON entirely — don't fabricate. The API treats missing fields as null. What counts as "optional" is defined per-field in the Sheet.

**Bruce (EXCLUDE list)** — three fields the system manages. Step 3's JSON builder must exclude these from output. **OwnerId**: auto-populated from the authenticated user's token — API overwrites any provided value. **OtherCode**: auto-populated from the Id field if provided — API overwrites any explicit value. **OspId**: internal system reference — API preserves any value sent (unexpected behavior, confirmed Feb 19). Defensive design: exclude all three from JSON output. **Undefined:** OspId preserved behavior — confirm with Rein whether this is intentional → *[Meeting agenda §3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-api-reconciliation)*

**Skip** — excluded from PoC. Currently only AssignedPortfolioEmployee (requires employee list lookup, deferred to next version).

**Status field clarification (updated Feb 19):** Rein merged the former AssetStatus and Status fields into a single `Status` field. StatusDetail remains a separate overflow field for unmapped column content.

**Overflow rule:** When client columns don't map to any of the 36 import fields, Step 2 evaluates each individually: relevant context overflows to StatusDetail (a 2000-char description field), irrelevant columns are skipped. This is a Step 2 decision — Step 3 executes the mapping without judgment.

**Reference:** [AssetImportDescription.xlsx](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) — full 37-field schema (BrandSpecific added Feb 19). [Countries enum](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) (249 entries — parentheses removed Feb 19 by Rein. Remaining: trailing spaces on some entries, "North Kores" typo — [Rein Teams chat](~/.claude/hippocampus/project/archibus-fm-assistant/rein-teams-chat-feb19-20.md)).

#### The Transform

AI performs four sub-operations on each row:

1. **Column mapping** — which Excel column feeds which JSON field (Step 2 output)
2. **Value transformation** — enum matching, date format conversion, country resolution
3. **Structure transformation** — flat rows → nested tree (children inside parents)
4. **Missing value handling** — omit field from JSON body. API treats missing fields as null via .NET model binding (confirmed by Rein, Feb 11).

**Value transformation categories (including but not limited to):**

| Category | Examples | Rule | Reference |
|----------|----------|------|-----------|
| **Enum fields** | AssetType, AssetStatus, Country | Must match predefined values. Part 6 backpressure catches mismatches. | [asset_types](https://docs.google.com/spreadsheets/d/1Wc1BL18e5Vaxx7bAzsvxAeWojfvxRrWd/edit), [asset-status-enum](https://drive.google.com/file/d/1j0GzUSKKaFeG3nj6G8Qp5v7772jg7vpL/view) |
| **Typed fields** | DateTime, double, Guid, String(len) | Must match format from schema. | [AssetImportDescription → Fields Assets](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) |
| **Freeform fields** | Name, Address, LocationDescription | Pass through as-is. | Same schema for max lengths |

**Example — transform + backpressure loop:**

```
Raw Excel Row: A008 | Safety | Building A | All | 116 | LG | FX100
    ↓
    1. Column mapping (Step 2: "Type" → AssetType)
    2. Value transform (Safety → Safety Equipment, matching Bruce enum)
    3. Structure transform (nest under Building A → Floor All → Room 116)
    4. Missing value handling (empty cells → omit from JSON)
    ↓
AI-Generated JSON node
    ↓
Part 6: Validate via API
    ↓
API accepts ✅  OR  API rejects ❌ → error → AI corrects → resubmit
```

#### JSON Structure: Generic `children`

The nesting uses a generic `children` array at every level, differentiated by `assetType` field (ENUM). The API accepts camelCase field names. This replaced the earlier named-array design (`Buildings`, `floors`, `Rooms`) because the backend needs to process unknown-depth hierarchies with a single recursive function — named arrays would require level-specific code paths for every possible hierarchy combination.

```json
{
  "name": "MyProperty Name",
  "assetType": "Property",
  "children": [
    {
      "name": "Building A",
      "assetType": "Building",
      "children": [
        {
          "name": "1",
          "assetType": "Floor",
          "children": [
            {
              "name": "Room 194",
              "assetType": "Room",
              "children": [
                {
                  "name": "Safety-008",
                  "assetType": "Equipment",
                  "serialNumber": "SN000034"
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

The `children` array is the sole expression of parent-child relationships. The server walks the tree recursively, generates UUIDs for each node, and auto-wires ParentId from the nesting structure. AI does not need to provide Id or ParentId — the tree structure is sufficient. If AI provides a string Id (e.g., "A202"), the server preserves it in OtherCode for traceability but generates its own UUID regardless. (Confirmed via API testing, Feb 19.)

#### Phase Model

**Phase 1 (Steps 1+2): Interactive.** Hierarchy validation and column mapping. Human-AI collaboration with lots of questions. Step 2 produces field mapping + ENUM resolution tables (including but not limited to these — additional outputs emerge empirically).

**Phase 2 (Step 3): Execution + back-pressure.** JSON building and API insertion. Near-zero questions. Step 3 follows Step 2's decisions mechanically. When the API returns errors, AI self-corrects via the back-pressure loop (Part 6) — only escalating to human when self-correction fails.

**ClearanceInstructions (resolved Feb 20):** Applies at both building and equipment levels — not restricted to one asset type. Example: a large generator behind a locked door needs equipment-level access instructions. Rein: "We cannot predict all situations in life."

**BrandSpecific (added Feb 19):** Manufacturer/brand name (e.g., "Nestlé", "LG"). Rein added this field to the AssetImportDescription sheet. Maps to the manufacturer column in client data. ModelSpecific = model name (e.g., "Roomba 5000").

**AddressExplanation / LocationDescription:** Empirical — applicability determined by real client data, not upfront design. Both remain in the schema as potential fields. If client data contains matching content, map them. If not, omit.

**Undefined:** Barcode and QrCode need real-world data examples from client implementations. QrCode DB extension pending — 50 chars insufficient, Rein committed to extending. → *[Monday meeting agenda]*

**Undefined:** OspId preserved behavior — confirm with Rein whether this is intentional or a bug → *[Meeting agenda §3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-api-reconciliation)*

**Undefined:** ValidateAssets as required dry-run step or optional given ImportAssets validates internally → *[Meeting agenda §4](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-api-reconciliation)*

*Source: [Feb 6 — Rein/Marius meeting](https://app.fireflies.ai/view/01KGSBJE39AW3259QVNCQHCBDQ), [Feb 11 — Rein meeting](https://app.fireflies.ai/view/01KH67KMEA533C0VVJWH7FE63D), [Feb 14 — extraction pass](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit)*

### 6. Insert via API + Backpressure

Send the AI-generated JSON for one top-level element to Bruce BEM's asset import API. Sequential processing — no parallel in PoC.

**API Endpoints (live as of Feb 18):**

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/Assets/ValidateAssets` | POST | Dry run — validates, returns structure, never persists |
| `/api/Assets/ImportAssets` | POST | Validates AND inserts if valid |
| `/api/Assets/RevertImport?importId={id}` | GET | Rolls back entire import batch |
| `/api/Assets/HealthTest` | GET | Connectivity check |

**Base URL:** `https://services.bruceplatform.com/api/`

**Auth:** Dual-token — `X-User-Token` (from UserAuth API) + `Authorization: Bearer` (from Auth0 OAuth). Credentials in `.env` of the FM assistant project.

**Insertion model (validate-then-insert, confirmed Feb 19 via API testing):**

```
AI-Generated JSON (one top-level element + all children)
    ↓
ValidateAssets (dry run — no DB write)
    ↓
Error found → return error (one at a time, HTTP 200 with error body)
    ↓
AI reads error → corrects JSON → resubmits to ValidateAssets
    ↓
No errors → submit to ImportAssets
    ↓
ImportAssets validates again internally → inserts if clean
    ↓
Response: full tree with server-generated UUIDs + importID (root only)
```

Nothing is inserted until the entire payload validates — server-enforced, not just client-side discipline. ImportAssets validates internally with the same error format as ValidateAssets (stop-at-first-error, confirmed Feb 19). **Undefined:** Whether ValidateAssets is a required dry-run step or optional given ImportAssets validates internally → *[Meeting agenda §4](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-api-reconciliation)*

**Error response format (confirmed Feb 19 via API testing):**

```json
{
  "message": "",
  "error": {
    "id": null,
    "field": "AssetType",
    "value": "InvalidType999",
    "reason": "Invalid AssetType",
    "valid_values": ["Air Cooling", "Building", "Equipment", "Floor", "..."]
  }
}
```

Errors return HTTP 200 — caller must check for a non-null `error` key, not HTTP status codes. The error identifies the failing field, the submitted value, the reason, and the valid options (for enum fields). The `id` field in the error is null when no Id was provided in the request — if AI provides string Ids, the error will reference the failing node's Id.

#### Backpressure Loop Mechanics

Step 2 creates the intelligence — enum resolution tables, column mappings, transformation rules. Step 3 applies that intelligence to generate JSON. One-shot generation is not reliable: the AI will produce field values that don't match the API's expectations. The backpressure loop catches what the AI gets wrong and self-corrects using the API's own feedback.

**Id contract:** Every node in the JSON tree must carry a synthetic `id` field. Equipment nodes use the client's asset code (e.g., "A034"). Location nodes (buildings, floors, rooms) use AI-generated ids (e.g., "A201"). Without ids, error responses return `id: null`, making failing nodes unlocatable in nested trees. The correction loop cannot function without id-based node lookup. **Undefined:** Whether ValidateAssets should enforce `id` as a required field (reject payloads with missing ids) → *[Meeting agenda §1](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-api-reconciliation)*

**Correction cycle (per round trip):**

1. AI sends full tree to ValidateAssets
2. Error returned → `{id, field, value, reason, valid_values}`
3. AI locates node in the in-memory tree by matching `error.id`
4. AI reads `valid_values` from the error response — this is the self-healing lookup table
5. AI picks the closest match and fixes the node's field value
6. AI resubmits the full tree (mutated in place) to ValidateAssets
7. Repeat until no errors → send to ImportAssets

During the correction cycle, ids are stable — ValidateAssets echoes them back in errors without modification. The id-to-UUID replacement (where the server generates UUIDs and maps the caller's string id to `otherCode`) only occurs on successful ImportAssets calls, after the correction loop is complete.

**Stop-at-first-error:** The API returns one error per submission. The correction strategy: locate the failing node by id, fix it, resubmit.

**importID + RevertImport:** Each successful ImportAssets call returns an `importID` (UUID) on the root node. Children's importID is null (batch import). RevertImport rolls back all assets tagged with that importID. This is a human safety net for semantic issues discovered post-import — not part of the AI backpressure loop. The best undo is not needing one.

**GUID mapping (updated Feb 19):** Available for free in the ImportAssets response. The response returns the full tree with server-generated UUIDs for every node. For PoC (one top-level element per call), this is informational. Post-PoC, the response UUIDs enable multi-element imports where subsequent elements reference parent UUIDs from earlier imports.

**Reference:** [API reference 2026-02-18.json](API spec provided by Rein) — OpenAPI 3.0.4 spec. [FM assistant .env](credentials for auth).

*Source: [Feb 3 — Rein meeting](https://app.fireflies.ai/view/01KGHJWSXV7Y7FP822SKS9VBHW), [Feb 11 — Rein meeting](https://app.fireflies.ai/view/01KH67KMEA533C0VVJWH7FE63D), [Feb 19 — API testing session]()*

### 7. Repeat

Move to next top-level element. The full cycle (validate → correct → dry run → insert) completes per building before moving on — per-building atomic completion. Continue until all elements processed.

---

## Decisions

**Field-level decisions** (field tiers, applicability, descriptions, enum values) are maintained in the [AssetImportDescription — Fields Assets sheet](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit). The Sheet is the authoritative field reference. This table captures **architectural and structural decisions** only.

| Decision | What | Date | Source |
|----------|------|------|--------|
| **Tree-walk** | Process one top-level element and all children at a time | Feb 3 | Rein meeting |
| **Stop at first error** | API returns one error at a time. AI corrects and resubmits. No insertion until all valid. | Feb 3 | Rein meeting |
| **Sequential first** | No parallel processing in PoC | Feb 3 | Rein meeting |
| **AI creates JSON** | With Bruce BEM field names from Step 2 mapping | Feb 3 | Rein meeting |
| **No interactive editing** | AI proposes/processes hierarchy, doesn't modify Excel | Feb 3 | Rein meeting |
| **Id/ParentId optional** | Server generates UUIDs and auto-wires ParentId from nesting. If AI provides a string Id, server maps it to OtherCode. Id and ParentId are could-have, not must-have. | Feb 19 | API testing |
| **Lowest level attachment** | Equipment attaches to lowest known hierarchy level. Empty = fallback up. | Feb 3 | Rein, Feb 3 meeting |
| **Generic `children` array** | All child nodes use `children` key, differentiated by `type` field (AssetType). Enables single recursive function for unknown-depth hierarchies. | Feb 14 | Rein, Feb 14 chat |
| **Nesting = sole relationship** | children[] array is the sole expression of parent-child. Server derives ParentId from nesting structure. | Feb 19 | API testing |
| **Same schema for all levels** | Location assets and equipment are all rows in the same assets table, differentiated by AssetType. | Feb 6 | AssetImportDescription |
| **API live** | All endpoints live (ValidateAssets, ImportAssets, RevertImport). Full pipeline implementable. | Feb 18 | Rein delivery |
| **Floor All as-is** | Create Floor "All" as a real location asset. No special-casing — standard recursive fallback applies. | Feb 11 | Rein, Feb 11 meeting |
| **Recursive fallback unconditional** | Climb up hierarchy regardless of floor value. No exceptions. | Feb 11 | Rein, Feb 11 meeting |
| **Excel = field name source** | AssetImportDescription "Fields Assets" sheet is authoritative for JSON field names. API auto-converts casing. | Feb 11 | Rein, Feb 11 meeting |
| **Country = name match** | Fuzzy-match to country name (column A), not code_2. Exact string required. | Feb 11 | Rein, Feb 11 meeting |
| **Omit empty fields** | Don't include empty fields in JSON body. API treats missing as null. | Feb 11 | Rein, Feb 11 meeting (pending test confirmation) |
| **Manufacturer → BrandSpecific** | Manufacturer maps to brandSpecific field (not StatusDetail). ModelSpecific = model name. | Feb 20 | Rein meeting |
| **OtherCode = Bruce auto-fill** | Bruce copies client Asset Code (from Id field) to OtherCode automatically. AI does not touch this field. | Feb 14 | AssetImportDescription update |
| **Error format = Id-based** | Error response must include failing node's `id` + field + error. AI self-constructs paths from ParentId chain. | Feb 14 | Extraction pass |
| **Dry run confirmed** | Validate without inserting. Only insert after all validations pass. | Feb 11 | Rein, Feb 11 meeting |
| **Casing indifferent** | API accepts any casing (PascalCase, camelCase). No normalization needed. | Feb 14 | Rein chat |
| **Default AssetType = Equipment** | When client's asset type doesn't match any ENUM value, default to "Equipment". | Feb 14 | Rein chat |
| **StatusDetail overflow = Step 2 decision** | Step 2 evaluates unmapped columns: overflow to StatusDetail or skip. Step 3 executes without judgment. | Feb 14 | Extraction pass |
| **Phase model** | Phase 1 (Steps 1+2) = interactive. Phase 2 (Step 3) = execution + back-pressure. | Feb 14 | Extraction pass |
| **AssetType enum updated** | New 23-type facility list replaces old. Other categories (Employee, IT, Location) unchanged. | Feb 14 | Rein chat |
| **EXCLUDE list** | JSON builder must exclude ownerId, ospId, otherCode. Server manages these. | Feb 19 | API testing |
| **Status field merged** | Rein merged AssetStatus + Status into single Status field. Enum validation pending. | Feb 19 | Rein Teams chat |
| **Error format = HTTP 200** | Errors return HTTP 200 with {error: {field, value, reason, valid_values}}. Check error key, not status code. | Feb 19 | API testing |
| **ImportAssets validates internally** | ImportAssets has same validation as ValidateAssets. Stop-at-first-error server-enforced. | Feb 19 | API testing |
| **importID = batch tag** | Server-generated UUID on root per ImportAssets call. Children get null. RevertImport rolls back by importID. | Feb 19 | API testing |
| **RevertImport = human safety net** | Not part of AI backpressure loop. For semantic issues discovered post-import. | Feb 19 | Extraction pass |
| **GUID mapping free** | ImportAssets response returns full tree with server UUIDs. Post-PoC: enables multi-element reference. | Feb 19 | API testing |
| **Existing-database = foundational constraint** | Production imports must dedup against existing location assets. Address is the primary match key for buildings. Step 2 responsibility. Every import after the first one encounters this. | Feb 20 | Rein meeting |
| **ENUM validation live** | Countries, AssetTypes, Status all validated server-side. Errors return valid_values list for self-correction. | Feb 19 | [Rein Teams chat](~/.claude/hippocampus/project/archibus-fm-assistant/rein-teams-chat-feb19-20.md) |

---

## Source

**Artifacts:**
- [AssetImportDescription](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) — full schema (Fields Assets sheet), hierarchy options, sample data. Yellow = AI-proposed comments, orange = UNDEFINED.
- [asset_types 1.xlsx](https://docs.google.com/spreadsheets/d/1Wc1BL18e5Vaxx7bAzsvxAeWojfvxRrWd/edit) — AssetType enum (updated Feb 14: 23 facility types + Location/Employee/IT categories)
- [asset-status-enum.csv](https://drive.google.com/file/d/1j0GzUSKKaFeG3nj6G8Qp5v7772jg7vpL/view) — AssetStatus enum (26 statuses)
- [cafm-asset-upload-sample.xlsx](https://docs.google.com/spreadsheets/d/1CUaybsk8ZnvvKr3C37xn-md96hRLBKT4/edit) — raw client input (201 rows)
- [Processed Excel Table.xlsx](https://docs.google.com/spreadsheets/d/13Q_P-zbFjbN7VIi4YUWi6bPYDuTxeziK/edit) — data after processing (equipment + generated location assets)
- [Project Index](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/transcript-index-373) — design docs, meeting agendas, data artifacts, transcripts
- Countries Data enum (provided by Rein Feb 11, 249 entries) — country name matching reference
- [API reference 2026-02-18.json](/Users/verdant/Downloads/API reference 2026 02 18.json) — OpenAPI 3.0.4 spec for Bruce BEM AI endpoints (ValidateAssets, ImportAssets, RevertImport)
- [Rein meeting agenda — API reconciliation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-api-reconciliation) — 6 undefined items from Feb 19 extraction pass
- [Rein Teams chat Feb 19-20](~/.claude/hippocampus/project/archibus-fm-assistant/rein-teams-chat-feb19-20.md) — ENUM validation examples, countries update, existing-database proposal

**Session:**
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/1443f1aa-f108-4e27-94ba-d2954cc26dc5.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/6b1217f1-1b02-434e-be24-22dd1a8a1975.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/494332f2-3f40-4c9b-9d96-43e84c3f2441.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/7f5bcc28-c0af-4433-a1fb-2ea60171bdee.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/972b54f8-3fad-4090-8636-562ba06ccc28.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/aee03387-ce21-4f51-9da1-b34b930d7247.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/18b07a8c-a336-47a3-a705-c5ed77fb5da5.jsonl`
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/719f17b2-3360-4099-a3ba-ffe732076e46.jsonl`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

