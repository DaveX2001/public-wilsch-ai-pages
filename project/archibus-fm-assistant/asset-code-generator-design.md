---
publish: true
---

# Asset Code Generator - Design Doc
[[client-archibus]]

## Problem Statement

**Repetitive manual work generating asset codes from raw Excel data.**

Partners receive client Excel files with raw asset data (building, floor, room, equipment type). For each asset, they must manually construct an Equipment Code following the pattern: `{Building}-{Floor}-{Room}-{Type}-{Sequence}`. This involves:

1. Transforming building codes (strip prefixes)
2. Truncating room codes
3. Mapping equipment types to abbreviations
4. Tracking sequence numbers

This is tedious, error-prone work repeated for every FM implementation.

---

## Success Definition

**A tool that generates Equipment Codes from raw Excel data, reducing manual effort and errors.**

| Element | Definition |
|---------|------------|
| Goal | Generate Equipment Codes for all rows with sufficient data |
| Success | Excel file with Equipment Code column populated correctly |
| Failure mode | Actionable - tool flags rows it cannot process with clear reason |

---

## Target Audience

**Primary: FM Implementation Partners**
- Repeatable pain (every implementation)
- Currently manual, time-consuming work
- Direct beneficiaries of automation

**Secondary: Clients**
- Faster onboarding
- Consistent asset naming

---

## High-Level Overview

```
INPUT                    STEPS                         OUTPUT
─────                    ─────                         ──────
Excel file          →    1. Read columns               →    Excel file with
with raw data            2. Transform each segment          Equipment Codes
                         3. Track sequences                 filled in
                         4. Generate codes
```

**Equipment Code Pattern:** `{Building}-{Floor}-{Room}-{Type}-{Sequence}`

**Example:** `DUP-GF-LRBA-ACO-0001`

| Segment | Source Column | Transformation | Example |
|---------|---------------|----------------|---------|
| Building | Column E | Strip prefix (A-DUP → DUP) | A-DUP → DUP |
| Floor | Column G | Use as-is | GF → GF |
| Room | Column F | First 4 chars | LRBAL → LRBA |
| Type | Column D | Abbreviate | AIR-CON → ACO |
| Sequence | Generated | Continuous counter | 0001, 0002... |

---

## Key Uncertainties

### Uncertainty #1: Schema Consistency

**Core question:** Can we expect a consistent input schema, or will columns vary?

**What "schema" means:**

| Aspect | Question | Example |
|--------|----------|---------|
| Column index | Will Building Code always be in Column E? | Or could it be Column C in another file? |
| Header name | Will the header always say "Building Code"? | Or could it be "Site", "Block", "Location"? |

**Why this matters:** If every client file looks different, the tool needs to handle that variation. If files are mostly consistent, it's simpler.

**Question:** How similar are client Excel files? Same columns in same places, or does every file look different?

---

### Uncertainty #2: Data Availability & Value Consistency

**Two dimensions:**

| Dimension | Question |
|-----------|----------|
| Presence | Are all required fields populated? |
| Format | Do values follow a consistent pattern? |

**Required fields:** Building Code, Floor Code, Room Code, Equipment Standard

**Format examples:**

| Field | Consistent | Inconsistent |
|-------|------------|--------------|
| Building Code | Always like `A-DUP`, `V01`, `F704` | Could be "Main Building", "Block A", "HQ" |
| Floor Code | Always like `GF`, `1F`, `7F` | Could be "Ground", "First", "Level 1" |
| Room Code | Always like `LRBAL`, `KIT`, `BED1` | Could be "Living Room", "Kitchen", "Bedroom" |
| Equipment Standard | Always like `AIR-CON`, `ELEC-LGHT` | Could be "Air Conditioner", "Light Fixture" |

**Questions:**
1. Do clients use short codes like `KIT` or write out full words like "Kitchen"?
2. How common are rows missing one of the four segments (Building, Floor, Room, Type)?

---

### Uncertainty #3: Transformation Rules

**Core question:** Can we agree on one set of rules, or will each client need different rules?

**Example transformations (from the sample file):**

| Segment | Example | What happened |
|---------|---------|---------------|
| Building | `A-DUP` → `DUP` | Stripped the `A-` prefix |
| Floor | `GF` → `GF` | Used as-is |
| Room | `LRBAL` → `LRBA` | Truncated to 4 characters |
| Type | `AIR-CON` → `ACO` | Abbreviated |
| Sequence | → `0001` | Added counter |

**Question:** Are these rules standard across all clients, or does each client have their own conventions?

---

### Uncertainty #4: Output Format

**Core question:** What should the tool produce and how?

**Examples (including but not limited to):**
- Update the original Excel file directly
- Create a new file, keep original untouched
- Show preview before saving

**Question:** What output format and workflow makes sense for partners?

---

### Uncertainty #5: Abbreviations

**Core question:** Can we agree on one set of abbreviations, or will each client have different ones?

**Example from sample file:**
- `AIR-CON` → `ACO`

**Question:** Is there a standard abbreviation table, or does each client define their own?

---

## Example Output

Sample Excel updated with generated Equipment Codes:

```
Equipment Code           Equipment Standard  Building  Room   Floor
────────────────────────────────────────────────────────────────────
DUP-GF-LRBA-ACO-0001    AIR-CON            A-DUP     LRBAL  GF
DUP-GF-KIT-ACO-0002     AIR-CON            A-DUP     KIT    GF
DUP-1F-BED1-ACO-0003    AIR-CON            A-DUP     BED1   1F
V01-GF-BED1-ACO-0005    AIR-CON            V01       BED1   GF
DUP-GF-LRBA-ELE-0009    ELEC-LGHT          A-DUP     LRBAL  GF
```

Full example file: [Sample Asset Data - Equipment Code Generator Example](https://docs.google.com/spreadsheets/d/1_OOUyGDXXf_OHfg4hqpc1AO_8PoNQXqu/edit)

---

## Next Step

Before building, one core question: **How standardized is the data we'll receive?**

- If data is consistent → straightforward tool
- If data varies wildly → need more design work first

---

## Source

- Mujahid <> Marius meeting (2026-01-22)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

