---
publish: true
---

# Criteria Evidence Template — REKERS Projects
[[client-rekers]]

Reusable template for producing per-project criteria evidence documents. Based on [37369 evidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-37369), formalized through Part 2 extraction pass (12 uncertainties resolved).

---

## Template Structure

```markdown
# Criteria Evidence — Project {ID}: {Name}
[[client-rekers]]

Evidence for design doc Part 2. Per-criterion data availability with source files and extracted values.

Project: {ID} | Date: {date} | Value: €{value}

---

## Data Examined

| Layer | Count | Details |
|-------|-------|---------|
| L0: Folder files | {n} | {n_eml} EML, {n_pdf} PDF, {n_zip} ZIP |
| L2: EML attachments | {n} | {n_pdf} PDF, {n_xlsx} XLSX, {n_other} other |
| _extracted (ZIP) | {n} | {types} |

Multiplier: {x}x (L0 vs L0+L2)

---

## Quick View

| Criterion | Status | Value | Source Level |
|-----------|--------|-------|-------------|
| Bauort | ✅ CSV | {value} | L1 |
| Kundenreferenz | ✅ CSV | {value} | L1 |
| Kalkulatorenwissen | ✅ CSV | {value} | L2 |
| Gebäudetyp | ✅/❌ | {value or "Not found"} | L1/L2 |
| Höhe | ✅/❌ | {value or "Not found"} | L1/L2 |
| Kran | ✅/❌ | {value or "Not found"} | L1/L2 |
| Dachlasten | ✅/❌ | {value or "Not found"} | L1/L2 |
| Baustoff | ✅/❌ | {value or "Not found"} | L1/L2 |
| Dachbegrünung | ✅/❌ | {value or "Not found"} | L1/L2 |

**{X}/9 evidenced** ({n} structured + {n} extracted). {n} absent.

---

## Structured Criteria (Anfragen.csv)

### Bauort
- **Value:** {location}
- **Source:** Anfragen.csv, Row {n} → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Value:** {customer_id}
- **Source:** Anfragen.csv, Row {n} → `KUNDE`

### Kalkulatorenwissen
- **Value:** {kalkulator_id}
- **Source:** Anfragen.csv, Row {n} → `KALKULATOR`

---

## Extracted Criteria (Project Files)

### {Criterion} ✅
- **Value:** {extracted value}
- **Source:** `{subfolder}/` → {file type}: `{filename}`
- **Passage:** {quoted text or description of visual content}
- **Note:** {any extraction observation — e.g., "only visible as image labels"}

### {Criterion} ❌
- **Value:** Not found
- **Source:** {what was searched}
- **Note:** {why not found — true negative vs undocumented}

---

## Observation

{Free-text for unique per-project findings. Examples:}
{- "All PDFs embedded in EMLs — 0 standalone. Extraction required."}
{- "Kranliste.xlsx found — direct Kran criterion evidence."}
{- "WeTransfer ZIPs corrupted (HTML, not actual archives)."}
{- "DWG files present but not processed (out of scope)."}

---

## Source

- **Data location:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/{ID}/`
- **Design doc:** [Feasibility Report, Part 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** {session_id}
```

---

## Extraction Awareness (for sub-agents)

When producing evidence documents, agents MUST know:

1. **EMLs are containers** — extract attachments to disk (Python `email` library). 660 PDFs are hidden inside EMLs across the 14 projects.
2. **ZIPs are recursive containers** — extract wherever found (on disk or inside EML attachments), then apply all rules to contents. Most L0 ZIPs already extracted to `_extracted/` dirs.
3. **PDFs: read visually** — use Read tool (renders as images natively). Do NOT text-extract. 5x more criteria found via visual read.
4. **XLSX: openpyxl via bash** — Read tool doesn't handle XLSX well.
5. **EML body text: read as text** — incoming correspondence from architects/engineers CAN contain criteria (building specs, structural changes).
6. **All file types are equal criteria sources** — no hierarchy. Technique varies by type.
7. **L0/L2 data model** — surface files (L0) are 2.2x–3.2x less than actual documents after unfolding EML containers.
8. **L1/L2 source tagging** — nice-to-have. Tag each criterion with source level (L1 = customer/architect, L2 = REKERS). Split is roughly 50/50, not "mostly L2."

---

## Source

- **Based on:** [Criteria Evidence — 37369](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-37369)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-REKERS--poc/772b96f8-d646-428c-bde6-9eeddf0418b5.jsonl` (Part 2 extraction pass)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

