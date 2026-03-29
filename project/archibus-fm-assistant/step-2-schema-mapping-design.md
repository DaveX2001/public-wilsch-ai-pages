---
publish: true
---

# Step 2: Schema Mapping - Design Document
[[client-archibus]]

## Problem Statement

Client column schemas never match Bruce BEM's expected schema. Traditional tools can't bridge the semantic gap between "Asset Name" and "Name", or "Equipment Description" and "Name". AI can infer these mappings.

**TBD (pending clarification):**
- Cell value transformations — do values need mapping? (pending schema constraint clarity)
- Input format variations — one sheet? Multiple? Where are headers?

---

## Success Definition

**Goal:** Each AI-filled Bruce BEM field has a confirmed semantic mapping to a client column.

**Output:** A mapping table pairing Bruce BEM fields → client columns. For example: Bruce's `Name` ← client's "Asset Name", Bruce's `SerialNumber` ← client's "Serial No."

Once confirmed, this mapping tells Step 3 exactly where each piece of client data should go.

**Success criteria:**
- Every AI-filled Bruce BEM field is either mapped to a client column OR marked as "no source available"
- Implementer has reviewed and confirmed the mapping

**Review modes:**
- **Batch review** — high confidence mappings confirmed together
- **One-by-one review** — uncertain mappings reviewed individually

**Authority:** Implementer has final say on all mappings.

---

## High-Level Overview

**Input:**
- Client Excel with unknown column schema (e.g., "Asset Name", "Equipment Description", "Serial No.")

**Process:**
1. Parse client column headers
2. For each Bruce BEM field (AI-filled ones):
   - Find semantically matching client column
   - Assign confidence level
3. Present mapping to implementer
4. Implementer confirms or corrects

**Output:**
- **Mapping table** — For each Bruce BEM field, the confirmed client column that provides its data
  - Example: `Bruce.Name ← Client."Asset Name"`
  - Example: `Bruce.SerialNumber ← Client."Serial No."`
  - If no match exists: marked as "no source"

**Cell values:** TBD (pending schema constraints)

---

## Example Output

**CAFM Sample:**

| Bruce BEM Field | ← | Client Column | Status |
|-----------------|---|---------------|--------|
| Name | ← | Asset Name | Confirmed |
| SerialNumber | ← | Serial No. | Confirmed |
| AssetStatus | ← | Status | Confirmed |
| Address | ← | Location | Confirmed |
| WarrantyTo | ← | Warranty Expiry | Confirmed |
| DatePurchased | ← | Purchase Date | Confirmed |
| OwnerId | ← | — | API-filled |

**FMM Sample:**

| Bruce BEM Field | ← | Client Column | Status |
|-----------------|---|---------------|--------|
| Name | ← | Equipment Description | Confirmed |
| SerialNumber | ← | Serial Number | Confirmed |
| WarrantyTo | ← | Date Warranty Expires | Confirmed |
| DateActiveService | ← | In-Service Date | Confirmed |
| OwnerId | ← | — | API-filled |

---

## Next Step

Before building:
- Clarify cell value transformations (Rein meeting)
- Clarify input format expectations (one sheet vs multiple)
- Define API-filled vs AI-filled field split

These discussion topics are captured in the Rein meeting agenda.

---

## Source

- Chain 1B: AI Bulk Data Entry Design Document (parent)
- CAFM Asset Upload Sample (2026-01-28)
- FMM Asset Data Sample (2026-01-28)
- Asset Import Schema (Rein's template)
- Rubber duck session (2026-02-03)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

