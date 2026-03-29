---
publish: true
---

# Meeting Agenda: Step 3 — Field Review & Undefined Items
[[client-archibus]]

## Meeting Goal

Validate field-level decisions from the Feb 14 extraction pass. The design doc's field tiers are now defined, but 6 fields remain Undefined and 12+ field descriptions need confirmation.

1. **Resolve 6 Undefined fields** — clarify purpose, data source, or remove from import scope
2. **Validate proposed field descriptions** — 12+ fields in the [Google Sheet](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) have no/inadequate comments. Proposed text is highlighted for review.
3. **Confirm AssetType enum update** — new 23 facility types replace old category, one potential typo

## Pre-Read

- [AssetImportDescription — Fields Assets sheet](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) — highlighted cells have proposed comments for review
- [Chain 1B: Step 3 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design) — updated Field Tiers section
- [Asset Types (old)](https://docs.google.com/spreadsheets/d/1Wc1BL18e5Vaxx7bAzsvxAeWojfvxRrWd/edit) — compare with new 23 facility types below

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Most field descriptions are empty — the AI needs context to map correctly

- 36 import fields in the Fields Assets sheet, 12+ have no comment at all
- AI uses these comments as mapping instructions during Step 2
- Without them, AI relies on field names alone → ambiguous mappings (e.g., "DateActiveService" = installation date? commission date? operational start?)
- Proposed comments highlighted in the [Fields Assets sheet](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) for: DateManufacture, DatePurchased, DateActiveService, DateNextReplacement, WarrantyFrom, WarrantyTo, AreaInteriorM2, AreaTotalM2, AreaRentableM2, MainContactName, MainContactPhone, MainContactEmail, Name, Id, ParentId

**We need confirmation that each proposed description matches what Bruce expects, or corrections where they don't.**

### 2. Three fields exist in the schema but their purpose and data source are unclear

- **AddressExplanation** (row 13): Rein's own comment says "Review if is in use, and what is corresponding DB field?" — example: "Follow the signs to the building when approaching from south"
- **LocationDescription** (row 18): "Additional Description of location, like on top of the hill" — overlaps with Address?
- **ClearanceInstructions** (row 12, renamed from SecurityClearanceStatus): example is "Access by access card. Card at Admin desk safe" — reads like location-level, not equipment-level

**We need a decision per field: keep in AI scope (with a clear description of what data goes here), or remove from the import template for PoC.**

### 3. Address is defined as a 50-character string — unclear what exactly goes in it

- City, State, PostalCode, Country each have their own fields
- Address is likely just street + house number (e.g., "Kotzebue tn 18a")
- Current Excel comment just says "Asset address, if supplied" — too vague for consistent AI mapping
- City and State are free-form strings — if client writes "Tallinn" vs "TALLINN" vs "tallinn", does Bruce care?

**We need:**
- (a) Address = street + house number only?
- (b) House number required or just street OK?
- (c) City/State validate against a list or accept any text?

### 4. Barcode and QrCode lack real-world examples and have unclear scope

- SerialNumber is straightforward (e.g., "45698712365")
- Barcode's example is a Gmail URL (placeholder, not real)
- QrCode's comment is just "Make it longer"
- SerialNumber comment says "If barcode reader in use, only numbers" — does scan go to SerialNumber or Barcode field?

**We need:**
- (a) Real-world Barcode value
- (b) Real-world QrCode value
- (c) When scanned barcode goes to SerialNumber vs Barcode field
- (d) QrCode DB length extension beyond 50 chars

### 5. Two status ENUM fields exist in the import template with no visible distinction

- Row 8 (`AssetStatus`, String FK, 50 chars) — "ENUM Choice from predefined statuses" → maps to 26-value asset-status-enum
- Row 24 (`Status`, String, 100 chars) — also says "ENUM, must match to list of statuses" — no description, no example, no separate ENUM list
- No second list exists in artifacts
- CAFM sample has "Status" column (Active/Inactive) and "Condition" column (Good/Fair/Poor) — unclear which maps where

**We need:**
- What is Status (row 24) for?
- Same ENUM as AssetStatus? Simplified active/inactive flag? Something else?
- If redundant, remove from import scope?

### 6. The updated AssetType facility list has one potential naming issue

- New 23 Facility-Related types provided via chat (Feb 14): Cooling, Heating, Amenity, Decoration, Emergency, Equipment, Fire Safety, Furnishing, Good, HVAC, Lighting, Spare Part, Appliances, SHERQ equipment, Signing, Surveillance, Vertical Transport, Electrical, Other Equipment, Plumbing, Waste Water, Automation, Safety
- One entry reads "Signing" (Labels, Screens) — old list had "Signage"

**We need:**
- (a) "Signing" intentional or should be "Signage"?
- (b) This list fully replaces old Facility-Related category while Employee Personal, Information Management, Location-Related remain unchanged?

### 7. The API error response needs to include the failing node's Id for self-correction

- Current error format (Feb 11) returns field name, value, reason — but not WHICH node failed
- Generic `children` structure means a building JSON may have 50+ nodes
- AI holds full tree in memory, can self-construct paths (Property > Building A > Floor 3 > Room 194) from Id + ParentId chain
- Backend only needs to return failing node's temp Id

**We need: confirmation that error response will include failing node's Id field for AI self-correction.**

### 8. Low-priority items — acknowledged, not blocking

- **Country enum cleanup:** 39 of 249 entries have parenthetical suffixes (e.g., "Korea (Republic of)") — fuzzy matching works around it, clean list would be simpler — blocked on Miguel
- **GUID mapping after import:** does API return temp ID → GUID mapping after insert? Not needed for PoC, may matter for multi-building imports

**These don't need discussion time unless Rein has quick answers.**

## Meeting Format

- **Type:** Field-level review
- **Expectation:** Rein reviews the highlighted cells in the [Fields Assets sheet](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) before the meeting
- **Outcome:** Undefined fields resolved (keep/remove), proposed comments validated, AssetType enum confirmed → design doc and sheet updated

## Related

- **Issue:** [#373 — ARCHIBUS: AI Bulk Data Entry](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Design Doc:** [Chain 1B: Step 3 Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **Fields Assets Sheet:** [AssetImportDescription (Google Drive)](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

