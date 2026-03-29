---
publish: true
---

# Step 3: Field Tier Mapping
[[client-archibus]]

Maps all 36 AssetImportDescription fields to their responsibility tier (Bruce-filled, AI-filled, Skip, TBD) based on the Feb 11 Rein/Marius alignment meeting.

---

## Field Tiers

| # | Field | Type | Length | Tier | Notes |
|---|-------|------|--------|------|-------|
| 1 | Id | Guid | — | **Bruce** | PK assigned by database automatically |
| 2 | ParentId | Guid | — | **Bruce** | Rein extracts from JSON hierarchy and assigns |
| 3 | Name | String | 128 | **AI** (required) | Must be in JSON. Insert fails without it. |
| 4 | AssetType | String FK | 50 | **AI** (required, enum) | Must match predefined types. See [asset_types](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset_types%201.xlsx) |
| 5 | OtherCode | String | 50 | **AI** | Copy client's original asset ID here for traceability and future integrations |
| 6 | StatusDetail | String | 2000 | **AI** (overflow) | DB name misleading — UI shows "Asset Detail Description." Use as catch-all for unmappable fields (e.g., manufacturer). Pipe-separated. |
| 7 | AssignedPortfolioEmployee | Guid | — | **Skip** | Too complex — requires employee list lookup. Skipped for AI scope. |
| 8 | AssetStatus | String FK | 50 | **AI** (enum) | Must match predefined statuses. See [asset-status-enum](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset-status-enum.csv) |
| 9 | OwnerId | Guid FK | — | **Bruce** | MemberID of inserting user. System assigns. |
| 10 | OspId | Guid FK | — | **Bruce** | Not possible for AI to fill. Excluded. |
| 11 | Address | String | 50 | **AI** (if provided) | Pass through from source data |
| 12 | SecurityClearanceStatus | String FK | 50 | **TBD** | Rein reviewing — may rename to ClearanceInstruction |
| 13 | AddressExplanation | String | 255 | **TBD** | Rein reviewing — one of AddressExplanation or LocationDescription will be removed |
| 14 | City | String | 50 | **AI** (if provided) | Pass through from source data |
| 15 | State | String | 50 | **AI** (if provided) | Pass through from source data |
| 16 | PostalCode | String | 50 | **AI** (if provided) | Pass through from source data |
| 17 | Country | String FK | 50 | **AI** (enum, fuzzy match) | Match client input to country NAME (not code2). Exact string match required. See countries table. |
| 18 | LocationDescription | String | 2000 | **TBD** | Rein reviewing — one of AddressExplanation or LocationDescription will be removed |
| 19 | CadastralReference | String | 120 | **AI** (if provided) | Official register code. Description + example from Rein pending. |
| 20 | SerialNumber | String | 50 | **AI** (if provided) | Pass through from source data |
| 21 | Barcode | String | 50 | **AI** (if provided) | Numeric string. Barcode reader output. |
| 22 | QrCode | String | 50→TBD | **AI** (if provided) | Alphanumeric string. 50 chars insufficient — Rein extending DB field. |
| 23 | ModelSpecific | String | 128 | **AI** | Maps to equipment model. No dedicated "model" field exists in schema. |
| 24 | Status | String | 100 | **AI** (enum) | Predefined statuses |
| 25 | DateManufacture | DateTime | — | **AI** (if provided) | |
| 26 | DatePurchased | DateTime | — | **AI** (if provided) | |
| 27 | DateActiveService | DateTime | — | **AI** (if provided) | |
| 28 | DateNextReplacement | DateTime | — | **AI** (if provided) | |
| 29 | WarrantyFrom | DateTime | — | **AI** (if provided) | |
| 30 | WarrantyTo | DateTime | — | **AI** (if provided) | |
| 31 | AreaInteriorM2 | double | — | **AI** (if provided) | Building/floor-level field |
| 32 | AreaTotalM2 | double | — | **AI** (if provided) | Building/floor-level field |
| 33 | AreaRentableM2 | double | — | **AI** (if provided) | Building/floor-level field |
| 34 | MainContactName | String | — | **AI** (if provided) | |
| 35 | MainContactPhone | String | — | **AI** (if provided) | |
| 36 | MainContactEmail | String | — | **AI** (if provided) | |

## Summary

| Tier | Count | Fields |
|------|-------|--------|
| **Bruce** | 4 | Id, ParentId, OwnerId, OspId |
| **AI (required)** | 2 | Name, AssetType |
| **AI (enum)** | 3 | AssetStatus, Country, Status |
| **AI (special mapping)** | 3 | OtherCode (←client ID), StatusDetail (overflow), ModelSpecific (←model) |
| **AI (if provided)** | 18 | Address, City, State, PostalCode, CadastralReference, SerialNumber, Barcode, QrCode, 6× dates, 3× areas, 3× contacts |
| **Skip** | 2 | AssignedPortfolioEmployee, SecurityClearanceStatus |
| **TBD (Rein)** | 4 | SecurityClearanceStatus, AddressExplanation, LocationDescription, QrCode length |

## Missing Value Rule

Empty fields: **omit from JSON body** (preferred — shorter JSON). API treats missing as null via .NET model binding. Pending Rein's test confirmation.

## Source

- [Feb 11 — Rein/Marius meeting](https://app.fireflies.ai/view/01KH67KMEA533C0VVJWH7FE63D)
- [AssetImportDescription.xlsx → Fields Assets sheet](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/AssetImportDescription.xlsx)
- Session: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-ARCHIBUS--archibus-fm-assistant/494332f2-3f40-4c9b-9d96-43e84c3f2441.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

