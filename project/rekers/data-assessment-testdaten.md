# REKERS Testdaten — Data Assessment Report
[[client-rekers]]

---
publish: true
---

V006 feasibility assessment: comprehensive analysis of test data delivered by REKERS on 2026-01-28 (Michael Rings, Ticket #19637). Evaluates CSV structure, file folder contents, data quality, and gaps against Workshop 2 specifications.

---

## Executive Summary

The delivered test data differs significantly from Workshop 2 specifications. Instead of 3 separate CSV tables (Anfragen, Kunden, Protokolle), the delivery contains 2 CSVs with denormalized/merged structures and 14 numbered folders containing email correspondence and PDF documents.

**Key Findings:**
- **Anfragen.csv**: Denormalized structure merging 3 entity types (111 data rows, 30 columns)
- **Angebote ohne Material.csv**: Quotation dataset (4,307 rows, 141 columns, 247MB — file size inflated by HTML content in VORTEXT column)
- **14 Numbered Folders**: 566 total files (mostly EML emails + PDF documents)
- **Missing Tables**: Kunden and Protokolle tables not delivered as separate files
- **Perfect ID Matching**: Folder structure maps directly to Anfragen.csv hierarchy

**Source:** [Email from Michael Rings](https://mail.google.com/mail/u/0/#all/19c051171933d8e1) (MRings@rekers-beton.de), 2026-01-28, Ticket #19637

**Data Location:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/`

---

## 1. CSV Analysis

### 1.1 Anfragen.csv

**File Statistics:**
- **Size:** 26KB
- **Rows:** 112 total (1 header + 111 data rows)
- **Columns:** 30
- **Delimiter:** Comma
- **Encoding:** UTF-8
- **Data Quality Issues:** Multiline text fields (BEMERKUNG column contains line breaks)

**Structure — Three Denormalized Entity Groups:**

The CSV contains a denormalized join of three entity types, identified by repeating "ID" columns at positions 1, 14, and 22:

**Group 1: Anfragen (Project Requests) — Columns 1-13**

| Column | Sample Value | Type | Description |
|--------|--------------|------|-------------|
| ID | 35764 | Integer | Request ID (matches folder names) |
| FIRMA | 1 | Integer | Company ID |
| BAUVORHABEN_NAME | "Neubau Werkhalle van Eckendonk" | String | Project name |
| BAUVORHABEN_STRASSE | "Marie-Curie-Straße" | String | Project street |
| BAUVORHABEN_ORT | "Gescher" | String | Project city |
| BAUVORHABEN_PLZ | "48712" | String | Postal code |
| BAUVORHABEN_NATION | "D" | String | Country code |
| ANFRAGEDATUM | "2019-07-19" | Date | Request date |
| GEFORDERTES_ABGABEDATUM | "2019-07-31" | Date | Required submission date |
| KALKULATOR | 1051 | Integer | Calculator/estimator ID |
| ANGEBOTSSUMME | 491260.52 | Decimal | Total quotation amount |
| STATUS | "E" | String | Status code |
| PRIORITAET | "" | String | Priority (often empty) |

---

**Group 2: AnfrageKunde (Request-Customer Link) — Columns 14-21**

| Column | Sample Value | Type | Description |
|--------|--------------|------|-------------|
| ID | 10593 | Integer | Request-Customer link ID (matches subfolder names) |
| ANFRAGE | 35764 | Integer | Foreign key to Anfragen.ID |
| KUNDE | 108636 | Integer | Customer ID |
| ANFRAGEDATUM | "2019-07-19" | Date | Request date |
| ABGABEDATUM_GEFORDERT | "2019-07-31" | Date | Required submission date |
| ANGEBOTSSUMME | 344002.7 | Decimal | Quotation amount |
| STATUS | "E" | String | Status code |
| ABSAGEGRUND | 0 | Integer | Rejection reason code |

---

**Group 3: Angebot (Quotations) — Columns 22-30**

| Column | Sample Value | Type | Description |
|--------|--------------|------|-------------|
| ID | 8182 | Integer | Quotation ID |
| ANFRAGEKUNDE | 10593 | Integer | Foreign key to AnfrageKunde.ID |
| ABGABEDATUM | "2019-08-13" | Date | Submission date |
| ANGEBOT | 28273 | Integer | Quotation number |
| ANGEBOT_NACHTRAG | 0 | Integer | Quotation supplement number |
| ANGEBOTSSUMME | 344002.7 | Decimal | Quotation amount |
| BEMERKUNG | "" | String | Notes (multiline text) |
| STATUS | "" | String | Status code |
| ANGEBOT_X | 39807 | Integer | Unknown reference ID |

**Data Quality Observations:**
- **Null Handling:** Empty strings ("") used instead of NULL
- **Text Encoding:** UTF-8 with German umlauts correctly encoded
- **Multiline Data:** BEMERKUNG field contains multiline text with bullet points and formatting
- **Denormalization:** Multiple rows per Anfragen ID (one-to-many relationships flattened)

---

### 1.2 Angebote ohne Material.csv

**File Statistics:**
- **Size:** 247MB (258,631,171 bytes)
- **Rows:** 4,307 data rows (file has ~1.77M raw lines due to multiline HTML in VORTEXT column)
- **Columns:** 141
- **Delimiter:** Comma
- **Encoding:** UTF-8

**Structure — Three Denormalized Entity Groups:**

Similar to Anfragen.csv, this file contains denormalized joins with repeating "ID" columns at positions 1, 44, and 51.

**Group 1: Angebot Header (Columns 1-43)**

| Column | Description | Type |
|--------|-------------|------|
| 1. ID | Quotation ID | Integer |
| 2. FIRMA | Company ID | Integer |
| 3. ANGEBOT | Quotation number | Integer |
| 4. NACHTRAG | Supplement number | Integer |
| 7. ANGEBOTSDATUM | Quotation date | Date |
| 8. BEZEICHNUNG | Description | String |
| 9. SUCHBEGRIFFE | Search terms | String |
| 10. ANFRAGE | Request ID (FK to Anfragen) | Integer |
| 12. BEARBEITER | Processor ID | Integer |
| 13-19. WERK, KOSTENSTELLE_* | Cost centers | Integer |
| 20-23. GEMEINKOSTEN_* | Overhead costs | Decimal |
| 24. GEWINN | Profit margin % | Decimal |
| 25. FRACHT | Freight cost | Decimal |
| 26-30. STUNDENSATZ_* | Hourly rates | Decimal |
| 31. VORTEXT | Preamble text | String (HTML) |
| 32-33. MATERIALNACHLASS, ANGEBOTSNACHLASS | Discounts | Decimal |
| 34. ANGEBOTSSUMME | Quotation total | Decimal |
| 35-42. BETRIEBSBEREICH, ENTFERNUNG, etc. | Operational parameters | Various |
| 43. STATUS | Status code | String |

---

**Group 2: Angebot Gliederung (Quotation Structure) — Columns 44-53**

| Column | Description |
|--------|-------------|
| 44. ID | Structure item ID |
| 45. ANGEBOT | Quotation number |
| 46. PARENT_ID | Parent structure ID |
| 47. ORDNUNGSZAHL | Sequence number |
| 48. BEZEICHNUNG | Description |
| 49. SORTIERFOLGE | Sort order |

---

**Group 3: Quotation Line Items (Columns 51-141):** Additional detail columns for quotation line items (91 more columns) including costs, materials, labor hours, weights, and calculated sums.

**Data Quality Observations:**
- **HTML Content:** VORTEXT column contains HTML-formatted preamble text with inline styles
- **Hierarchical Structure:** PARENT_ID suggests tree structure for quotation items
- **Comprehensive Costing:** Extensive cost breakdown columns (90+ calculation fields)
- **Dataset Size:** 4,307 rows of quotation line items (247MB due to HTML bloat in VORTEXT)

---

## 2. Folder Analysis

### 2.1 Folder Structure Overview

**14 Top-Level Folders:** Each named with Anfragen ID
**32 Subfolders:** Each named with AnfrageKunde ID
**~740 Total Files:** Across all folders (566 original + ~174 extracted from ZIPs)

### 2.2 Folder-by-Folder Breakdown

> **Note:** Counts below are pre-ZIP-extraction. See section 2.3 for updated totals after extracting 17 ZIP archives.

| Folder ID | Size | Total Files | PDF | EML | Other | Subfolders |
|-----------|------|-------------|-----|-----|-------|------------|
| 35764 | 135M | 30 | 3 | 22 | 5 | 10593, 11239 |
| 36185 | 293M | 67 | 3 | 62 | 2 | 11167, 13169, 15846, 19866 |
| 37369 | 37M | 19 | 0 | 19 | 0 | 12687 |
| 38043 | 619M | 104 | 0 | 100 | 4 | 13533 |
| 38882 | 197M | 41 | 0 | 40 | 1 | 14578, 15026 |
| 39381 | 200M | 50 | 1 | 49 | 0 | 15214 |
| 40138 | 245M | 31 | 3 | 27 | 1 | 16182 |
| 40593 | 99M | 39 | 5 | 34 | 0 | 16743 |
| 40758 | 351M | 68 | 18 | 49 | 1 | 16946, 17013, 17146, 18555 |
| 40856 | 123M | 19 | 2 | 16 | 1 | 17085 |
| 41634 | 103M | 23 | 1 | 21 | 1 | 18080 |
| 41740 | 207M | 25 | 0 | 24 | 1 | 18213 |
| 41793 | 127M | 27 | 6 | 20 | 1 | 18282 |
| 41807 | 139M | 23 | 2 | 21 | 0 | 18297 |
| **TOTAL** | **2.88GB** | **566** | **44** | **504** | **18** | **32 subfolders** |

### 2.3 File Type Distribution (after ZIP extraction)

| Format | Count | Notes |
|--------|-------|-------|
| EML | 482 | Email correspondence |
| PDF | 210 | Technical documents, quotations, permits (most were inside ZIPs) |
| DWG | 5 | AutoCAD drawings — floor plans, elevations, site plans |
| DXF | 3 | CAD exchange — building specifications (wall, deck, facade) |
| XLSX | 2 | Including **Kranliste.xlsx** (crane list — relevant to Kran similarity criterion) |
| ZIP | 21 | Original archives (3 corrupted) |
| IFC | 0 | Still absent — no Building Information Modeling files |

**Key finding:** 17 ZIP files were extracted, revealing 210 PDFs, 5 DWG, 3 DXF, and 2 XLSX files that were previously hidden. The `Kranliste.xlsx` (crane list, 2.3MB) in project 38043 contains structured crane data directly relevant to the Workshop 2 similarity criterion "Kran".

**Remaining absence:** IFC files (3D building models) were not found anywhere in the dataset.

---

## 3. Cross-Reference Analysis

### 3.1 Folder ID Mapping

**Perfect 1:1 Match Between:**
- **Top-level folder names** ↔ **Anfragen.csv Column 1 (ID)**
- **Subfolder names** ↔ **Anfragen.csv Column 14 (AnfrageKunde ID)**

```
Folder IDs (sorted):
35764, 36185, 37369, 38043, 38882, 39381, 40138, 40593, 40758, 40856, 41634, 41740, 41793, 41807

Anfragen.csv Column 1 IDs (sorted):
35764, 36185, 37369, 38043, 38882, 39381, 40138, 40593, 40758, 40856, 41634, 41740, 41793, 41807

Match: 100% ✓
```

### 3.2 Relationship Schema

```
Anfragen (Project Requests)
├── ID: 35764 (Folder: 35764/)
    ├── AnfrageKunde (Request-Customer Links)
        ├── ID: 10593 (Subfolder: 35764/10593/)
        │   └── Files: EML emails, PDFs related to this request-customer pair
        ├── ID: 11239 (Subfolder: 35764/11239/)
            └── Files: EML emails, PDFs, ZIP archives
```

---

## 4. Data Quality Summary

### 4.1 Present vs. Expected (Verified Against Transcript)

> **Source:** Wed AM transcript (Datenexport & Ranking-System, Fireflies ID: 01KG1V9Y791YHSAVP7GJABEJAA)

| Item | Transcript Evidence | Actually Delivered | Status |
|------|--------------------|--------------------|--------|
| Anfragen Table | Explicitly agreed as CSV | Delivered (denormalized with Kunden) | **MATCH** |
| Kunden Table | Ambiguous — two options discussed (separate vs. flattened into Anfragen) | Flattened into Anfragen.csv (KUNDE ID column, no details) | **AMBIGUOUS** |
| Protokolle Table | Discussed as potential 3rd table but delivery NOT confirmed ("are you checking that?") | Not delivered | **NEVER AGREED** |
| File Folders per Anfragen_ID | Agreed | Delivered (14 folders vs 10 agreed) | **EXCEEDED** |
| Angebote ohne Material CSV | Not mentioned in transcript | Delivered (4,307 rows) | **SURPRISE** |
| File Types (IFC, DWG, PDF, EML) | All discussed as expected formats | DWG/PDF/EML present, IFC absent | **PARTIAL** |

### 4.2 Absent Data

**Kunden Details (Customers):**
- Customer IDs present (e.g., 108636, 529) but no customer details (name, address, contact)
- Transcript shows ambiguity: Marius proposed separate table OR flattened into Anfragen — REKERS delivered flattened
- **Impact:** Cannot resolve customer names or demographics without separate Kunden export

**Protokolle (Meeting Protocols):**
- Transcript shows this was left as "we'll check" — not a committed deliverable
- 482 EML email files may serve as unstructured substitutes
- **Impact:** No structured meeting/protocol data (but this was never firmly agreed)

### 4.3 Concerns

1. **Multiline CSV parsing** — BEMERKUNG field spans multiple lines
2. **HTML embedded in CSV** — VORTEXT column in 247MB Angebote file
3. **No IFC files** — 3D building models absent even after ZIP extraction. DWG (5), DXF (3), and XLSX (2) were found inside ZIPs.
4. **14 folders vs 10 expected** — More data than specified

---

## Source

- **Email:** Michael Rings (MRings@rekers-beton.de), 2026-01-28, Ticket #19637
- **Issue:** [#629 — REKERS: KI-gestütztes Angebotssystem](https://github.com/DaveX2001/deliverable-tracking/issues/629)
- **Design Doc:** [Feasibility Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Analysis Tool:** Claude Code (Sonnet 4.5), 2026-02-09

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

