---
publish: true
---

# REKERS KI-gestütztes Angebotssystem — Feasibility Report Design Doc
[[client-rekers]]

V006 deliverable: feasibility assessment with Go/No-Go recommendation and POC implementation plan for AI-powered project-level similarity matching over 30 years of REKERS engineering history.

---

## Problem Statement

REKERS Beton GmbH has 30 years of engineering project history distributed across individuals and disparate data systems (IBM i database, Linux file systems). When a new quotation request arrives, finding relevant historical reference projects requires extensive personal knowledge and manual search — a process that depends on a few key personnel and takes days per request.

The problem is NOT building a full AI quotation system. The problem IS determining whether AI-powered project-level similarity matching is feasible given REKERS' actual data, and defining what a Proof of Concept would look like.

**Preconditions:**

- Workshop 2 completed (Jan 27-28, 2026) — scope and approach defined
- REKERS committed to delivering test data (10 projects as CSV + file folders) — 14 delivered
- Project-level matching adopted over component-level matching (complexity reduction)
- V006 contract covers this feasibility assessment (1.5 days analysis work)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Deliver V006 feasibility report with honest Go/No-Go recommendation and POC plan |
| **Success** | Report answers: (1) Is the data quality sufficient? (2) Can project-level matching work? (3) What would a POC cost and involve? Optional: demonstrate feasibility with indexed/chunked sample data |
| **Done test** | "Can I write a meeting agenda with open design questions?" → If NO → design is complete |

---

## Approach

### Part 1: Data Assessment — Does the data exist?

**Three-level model** (our synthesis, based on REKERS database entities Anfragen/Angebote/Kommissionen):

| Level | Entity | Rule | Data Available |
|-------|--------|------|----------------|
| **Level 1** | Anfragen (requests) | Customer's world — what they want | CSV row + files (EMLs, PDFs, plans) |
| **Level 2** | Angebote (offers) | REKERS's response — how they price it | Detailed cost breakdowns, component specs |
| **Level 3** | Kommissionen (commissions) | Execution — what was built | NOT delivered — deferred to POC (live system access required) |

**What we have:** REKERS delivered test data on 2026-01-28 (14 projects, all historical). All files live in the same folder per project — levels are logical, physical storage is mixed.

*Example: Anfrage 35764 — Neubau Werkhalle van Eckendonk, Gescher (including but not limited to):*

**Level 1 — Customer input** (what arrives):
```
35764/10593/  Angebotsaufforderung Stahlbau.eml     ← architect's initial request
35764/11239/  van Eckendonk_Planunterlagen.eml       ← plans from architect
              van Eckendonk_Projektunterlagen.eml    ← project specs
              van Eckendonk_Stützen.eml              ← column specifications
              Baugenehmigung.pdf                     ← building permit (external)
              Bodengutachten Schleicher.pdf           ← soil survey (external)
              BA-1000d_Grundrisse+Schnitt.dwg        ← floor plans (architect)
```

**Level 2 — REKERS response** (what REKERS creates):
```
35764/11239/  Angebot Nr. 28273.20.pdf               ← quotation document
              Angebot Nr. 28273.30.pdf               ← quotation document
              BV. Neubau Werkhalle van Eckendonk.eml ← REKERS internal emails
```

**Angebote ohne Material.csv** (Level 2 structured data, 4,307 rows, 141 cols): Quotation line items with cost breakdowns per component — weights, crane costs, material costs, crew sizes. Surprise delivery (not discussed in transcript). Assessed: contains no criteria-relevant data beyond Anfragen.csv (KRANKOSTEN = universal installation crane cost for precast, not building crane; material weights = REKERS product, not building material type). → Meeting agenda: why was this included?

**Level 3 — Execution** (what was built): Not delivered — deferred to POC.

**Across all 14 projects:** Dates 2019–2025, €1.2M–€15.3M. File inventory has two layers:

| Layer | Total | Key types |
|-------|-------|-----------|
| **L0: Visible** (folder files) | 565 | 504 EML, 44 PDF, 17 ZIP |
| **L2: Hidden** (EML attachments) | 1,322 | 660 PDF, 26 XLSX, 12 DWG, 435 images |

The ~740 files previously reported counted L0 + pre-extracted ZIPs. EML attachments (L2) were uncounted — yet contain the majority of criteria-rich PDFs. Every project is 2.2x–3.2x richer than the file system shows. ZIPs are containers at any level (in folders or inside EML attachments) — extract wherever found, then all rules apply to contents.

**Folder structure** (Anfrage ID = project, subfolders = customer links, verified 1:1 match with CSV):

```
REKERS Test Data/
├── Anfragen.csv                (14 Anfragen × AnfrageKunde × Angebot = 111 rows)
├── Angebote ohne Material.csv  (4,307 quotation line items)
├── Wilsch.zip                  (original delivery, 2.1GB)
│
├── 35764/ — Neubau Werkhalle van Eckendonk (2019, €491K)
│   ├── 10593/ (2 files)
│   └── 11239/ (23 files)
├── 36185/ — Lagergebäude GGM Gastro (2019, €3.9M)
│   ├── 11167/ (34 files)
│   ├── 13169/ (2 files)
│   ├── 15846/ (7 files)
│   └── 19866/ (0 files)
├── 37369/ — Kühl- und Lagerhalle Schütte (2020, €2.0M)
│   └── 12687/ (19 files)
├── 38043/ — Neubau Nokera Werk Stegelitz (2021, €15.3M)
│   └── 13533/ (91 files)
├── 38882/ — VGP Park Erfurt 3 (2022, €3.5M)
│   ├── 14578/ (25 files)
│   └── 15026/ (15 files)
├── 39381/ — VGP Park Magdeburg Halle D (2022, €11.8M)
│   └── 15214/ (48 files)
├── 40138/ — Hillwood (2023, €1.7M)
│   └── 16182/ (29 files)
├── 40593/ — Neubau Logistikzentrum (2023, €6.1M)
│   └── 16743/ (35 files)
├── 40758/ — Pfenning Neubau Halle (2024, €4.7M)
│   ├── 16946/ (51 files)
│   ├── 17013/ (3 files)
│   ├── 17146/ (3 files)
│   └── 18555/ (0 files)
├── 40856/ — Neubau Logistikhalle (2024, €1.8M)
│   └── 17085/ (17 files)
├── 41634/ — VGP Park Leipzig Halle C (2024, €5.6M)
│   └── 18080/ (22 files)
├── 41740/ — VGP Park Bernau Halle B (2024, €2.3M)
│   └── 18213/ (24 files)
├── 41793/ — Lager- und Logistikhalle Roy Böhlke (2025, €2.1M)
│   └── 18282/ (18 files)
└── 41807/ — NB Logistikhalle Spedition Neukirchen (2025, €1.3M)
    └── 18297/ (18 files)
```

**What we don't have:**

- **Level 3 — Kommissionen**: Deferred to POC — extraction from IBM i is complex, requires live system access.
- **Evaluierungsliste** from Herr Sasse: Not delivered (→ Part 4 blocker).
- **New request examples**: All 14 projects are historical. No example of what a new request looks like at arrival time — the trigger/input for the matching system is undefined (→ meeting agenda).

**Observation — absent ≠ no:** For criteria extracted from files, absence of information doesn't mean the criterion value is "no" — it means "undocumented." This applies to all extraction criteria. Whether any criteria are must-haves vs nice-to-haves requires both client input (meeting agenda) and empirical testing (POC).

**Detailed inventory:** [Data Assessment Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)

**Transcript:** [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) + [Wed Follow-up](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4)

### Part 2: File Processing Feasibility — Can we extract it?

Prerequisite for Part 3: 6 of 9 matching criteria require information extraction from project files (see Part 3 criteria mapping).

**File types and extraction approach:**

| Type | L0 | L2 (in EMLs) | How to read | Notes |
|------|----|-------------|-------------|-------|
| EML | 504 | — | Body: text parsing. Attachments: extract to disk first. | Containers — not files. 660 PDFs + 26 XLSX + 12 DWG hidden inside. |
| PDF | 44 | 660 | Read tool (renders as images natively). NOT text extraction. | 5x more criteria found via visual read vs text parse. |
| ZIP | 17 | 8 | Extract wherever found (on disk or inside EMLs), then apply all rules to contents. | Pre-extracted to `_extracted/` dirs for L0 ZIPs. |
| XLSX | 2 | 26 | openpyxl via bash. | Includes Kranliste.xlsx (crane specs), Abfallbilanz.xlsx (DGNB). |
| DWG | 5 | 12 | ❌ Out of scope — binary CAD, needs specialized tooling. | |
| DXF | 3 | — | ⚠️ Out of scope — parseable but complex. | |

**All file types are equal criteria sources** — no hierarchy. Extraction technique varies by type, but email body text, XLSX cells, and PDF pages all contribute to criteria discovery equally.

**Criteria extraction evidence** (all 14 projects — complete findings):

| Project | L0 | L2 | Extracted | Notes |
|---------|----|----|-----------|-------|
| [35764 — Werkhalle van Eckendonk](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-35764) | 25 | 65 | 5/6 | Kran 2×ABUS 40t/10t; Dachbegrünung ❌ |
| [36185 — Lagergebäude GGM Gastro](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-36185) | 67 | 217 | 4/6 | Kran ❌, Dachbegrünung ❌ |
| [37369 — Kühl/Lagerhalle Schütte](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-37369) | 19 | 74 | 4/6 | 40m Satteldachbinder; Kran ❌, Dachbegrünung ❌ |
| [38043 — Nokera Werk](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-38043) | 104 | 556 | 5/6 | Fertigteilwerk; Kranbahnrahmen 3/4 Hallen; Dachbegrünung ❌ |
| [38882 — VGP Park Erfurt 3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-38882) | 42 | 47 | 4/6 | Kran ❌; Dachbegrünung explizit ausgeschlossen |
| [39381 — VGP Park Magdeburg D](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-39381) | 50 | 96 | 5/6 | 20t Kran Sonderbereich; Dachbegrünung ❌ |
| [40138 — Hillwood](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-40138) | 6 | 80 | 3/6 | Holz-Fischbauchbinder; Kran ❌, Dachlasten ❌, Dachbegrünung ❌ |
| [40593 — Logistikzentrum](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-40593) | 39 | 91 | 3/6 | 4 Hallen + Mezzanine; Kran ❌, Dachlasten ❌, Dachbegrünung ❌ |
| [40758 — Pfenning Neubau Halle](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-40758) | 69 | 282 | 5/6 | DGNB Gold; Gründach 1,200 kN/m²; Kran ❌ |
| [40856 — Neubau Logistikhalle](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-40856) | 19 | 63 | 4/6 | Kran ❌, Dachbegrünung ❌ |
| [41634 — VGP Park Leipzig Halle C](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-41634) | 23 | 142 | 4/6 | Kran ❌; Dachbegrünung explizit ausgeschlossen |
| [41740 — VGP Park Bernau B](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-41740) | 24 | ~152 | **6/6** | Einziges 9/9 Projekt; 2×10t Kran + Dachbegrünung 1,00 kN/m² |
| [41793 — Lager/Logistikhalle Böhlke](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-41793) | 27 | 75 | 5/6 | 2×20t Koppelkran; Dachbegrünung explizit ausgeschlossen |
| [41807 — NB Logistikhalle Neukirchen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-41807) | 23 | 48 | 4/6 | F90 Brandschutz; Kran ❌, Dachbegrünung ❌ |

L0 = visible folder files, L2 = extracted EML attachments. Average multiplier: 4.7× (range 3.0–18.5×). Gebäudetyp, Höhe, Baustoff found in all 14 projects. Dachlasten found in 12/14. Kran documented in 5/14. Dachbegrünung documented in 2/14 (explicit exclusion recorded in 3 more).

**Key pattern:** EMLs are containers, not files. The 504 EMLs hold 1,322 attachments (660 PDFs, 26 XLSX, 12 DWG). 4/14 projects have zero standalone PDFs — all criteria-rich data is inside EML attachments. Reading PDFs visually (image rendering) yields 5x more criteria than text extraction. This pattern is structural to how REKERS stores project data.

**Key observation — L1/L2 source split:** Contrary to initial assessment, criteria are split roughly 50/50 between Level 1 (customer/architect documents: Gebäudetyp, Höhe) and Level 2 (REKERS quotations: Dachlasten, Baustoff). Not "mostly Level 2." 3 CSV criteria (Bauort, Kundenreferenz, Kalkulatorenwissen) are 2 L1 + 1 L2. Implications for new request matching depend on what data arrives at request time (→ meeting agenda, Thomas → Wittag).

**Transcripts:**
- [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (file format discussion, data source for criteria)
- [Thomas-Marius Sync 2026-02-11](https://drive.google.com/file/d/11Ttr1zcWoVSZccolGwJkCsWLZ2A-3G2g/view) (Part 2 is core deliverable, criteria extraction evidence for all 14 projects, deadline alignment)

### Part 3: Similarity Matching Approach — Can we match it?

Project-level matching: given a new Anfrage (Level 1 only), find the most similar historical **projects** from the reference set. Historical projects have Level 1 + Level 2 + files — all available data contributes to similarity. The matched projects' Angebote (Level 2) provide the pricing reference value.

**Asymmetry constraint:** New requests have limited data (Level 1 only). Historical projects have full lifecycle data. Matching must handle sparse input against rich references. **What a new request contains at arrival time is undefined** — this is a meeting agenda item (→ Part 4, → meeting agenda).

**Three modes of acquiring criteria:**

| Mode | Criteria | How |
|------|----------|-----|
| **Structured** (CSV) | Bauort, Kundenreferenz, Kalkulatorenwissen | Direct column comparison — always available |
| **Extracted** (documents) | Gebäudetyp, Höhe, Kran, Baustoff, Dachbegrünung | Text mining PDFs/EMLs — inconsistent availability |
| **Derived** (norms) | Dachlasten | Location → wind/snow load norms (DIN EN 1991) |

**Criteria-to-data mapping** (verified against actual CSV columns):

| Criterion | Source | Always available? |
|-----------|--------|-------------------|
| Bauort | CSV: `BAUVORHABEN_ORT, PLZ, NATION` | ✅ Yes |
| Kundenreferenz | CSV: `KUNDE` column | ✅ Yes |
| Kalkulatorenwissen | CSV: `KALKULATOR` column (ID only — who, not what they know) | ⚠️ ID available, knowledge requires extraction |
| Gebäudetyp | File extraction (PDFs, EMLs) | ⚠️ Requires extraction |
| Höhe | File extraction (specs, cross-sections) | ⚠️ Requires extraction |
| Kran | File extraction + Kranliste.xlsx | ⚠️ Requires extraction |
| Dachlasten | File extraction + derivable from location norms | ⚠️ Requires extraction |
| Baustoff | File extraction (specifications) | ⚠️ Requires extraction |
| Dachbegrünung | File extraction | ⚠️ Requires extraction |

3 criteria guaranteed in CSV. 6 require document extraction (see Part 2 for extraction evidence).

**Verified similarity criteria** (from Wed Follow-up transcript + handwritten notes, confirmed against each other):

1. **Gebäudetyp** — building type (e.g., Hallengebäude, Bürogebäude)
2. **Höhe** — height ("height is decisive for column calculations")
3. **Kran, nicht Kran** — crane or no crane (boolean)
4. **Dachlasten** — roof loads
5. **Bauort mit gleichen Dachlasten** — construction location combined with matching roof loads
6. **Kundenreferenz** — customer reference (complex: same Bauherr may have multiple GUs)
7. **Kalkulatorenwissen** — calculator expertise ("future-oriented" — not currently available)
8. **Baustoff** — building material (precast vs cast-in-place, wood roof structures)
9. **Dachbegrünung, nicht Dachbegrünung** — roof greening or not ("decisive for calculations")

**Rejected:** Photovoltaik ("not really relevant anymore")

**Not in transcript:** Achsraster, Zwischenebene (these were in the previous AI session's interpretation but are NOT verified)

**Dynamic weighting** [later]: "Wenn ich kalkuliere, ist die Dachbegrünung maßgeblich, wenn ich die Stütze kalkuliere, ist ja Höhe maßgeblich." Explicitly discussed — first prove matching works at all, then optimize weighting. Could be added later via chat interface.

**Ranking approach:** Partial matching — not all criteria will match. AI ranks by how many criteria match (3 of 5 > 1 of 5). Three-tier thresholds (80%/70%/60%), 60% cutoff. Top-K results (configurable, e.g., top 10 or 15).

**Three business outcomes from matching:**
- **High match (~100%):** "Angebot morgen bis 10 Uhr" — quote by tomorrow morning
- **Partial match:** Baukastensystem — combine parts from different reference projects
- **No match:** "Komplett neu entwickeln, dauert vier Wochen" — build from scratch

**UNDEFINED — similarity computation approach:** How to combine structured + extracted + derived criteria into one similarity score. Needs deep dive (→ next session).

**Background — abandoned approach:** Workshop 2 initially explored component-level matching (individual beams, columns matched by dimensions + loads). This was abandoned due to complexity of cross-location load normalization (wind, snow, earthquake). Project-level matching pre-filters the search space before any component analysis.

**Transcripts:** [Tue AM](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (component-level parameters, yellow fields) + [Tue PM](https://app.fireflies.ai/view/01KFZR74V0N0P1S589KBKZCBYH) (search/return parameters, variance tolerances 3-10%) + [Wed Follow-up](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4) (verified criteria list, ranking approach)

### Part 4: Validation Design — Can we validate it?

**Validation process** (agreed in Workshop 2):

1. REKERS provides 10 reference projects (Anfragen + Angebote + files) — ✅ delivered (14 actually)
2. Similarity criteria defined with Herr Sasse — ✅ done (9 criteria from Workshop 2)
3. REKERS provides **Evaluierungsliste** (test queries + expected matches) — ❌ not delivered
4. Marius provides CSV template for Evaluierungsliste — ❌ not created (→ meeting agenda deliverable)
5. AI processes test queries → returns ranked list
6. Compare AI ranking vs expected ranking

**Two-phase validation:**
- **Phase 1 (binary):** "Hier ist die Anfrage, das muss das Ergebnis sein." Given a test query, does the system return the expected project? Pass/fail per query. Goal: prove the AI can find correct matches at all.
- **Phase 2 (ranking quality):** "Jetzt musst du mir drei mit Ranking 80, 70, 60 Prozent ausliefern." Given a test query, does the system return matches in the expected order with meaningful scores? Top-K ranked list, 0-100% scores, 60% cutoff below which results aren't useful. Goal: prove the AI can distinguish between strong and weak matches.

**Evaluierungsliste template** (must-have columns):

| Column | Type | Description |
|--------|------|-------------|
| `Test_Anfrage_ID` | ID/text | The query being tested |
| `Expected_Match_1` | ID | Best expected match (Anfrage ID) |
| `Red_Herring` | Boolean | TRUE = "nothing found" is the correct answer |
| `Match_Reason` | Text | Which criteria drive the similarity (or why no match) |

**Red herrings** [TOUCHED — needs deep dive]: Test queries where the correct answer is "nothing found." The system must be able to say "tut mir leid, nichts gefunden" instead of forcing irrelevant matches. "Ist schlecht programmiert, wenn die KI nicht sagen kann, tut mir leid, nichts gefunden." How to design and select red herrings needs further exploration.

**Key assumptions:**
- Leave-one-out valid (use 9 as reference, query the 10th)
- Results = Top-K ranked list with 0-100% scores, 60% cutoff
- AI must handle "nothing found" case
- REKERS should provide conscious test queries (not timestamp reconstruction)

**Open question (→ meeting agenda):** What does a new request actually contain at arrival time? Determines which criteria are available for matching input.

**Client dependency:** Evaluierungsliste content from REKERS (→ meeting agenda).

**Transcript:** [Wed Follow-up — Priorisierung & UI](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4) (ranking thresholds, parameter weighting, test case design) + [Wed AM](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (Evaluierungstabelle concept, Herr Sasse's role)

### Part 5: Infrastructure Assessment — Where does it run?

Target: IBM Power 10 (~20 TOPS per chip). Alternative: hybrid approach (external GPU training, local Power 10 inference). Only relevant after Go/No-Go decision — does not affect feasibility assessment itself.

**Transcripts:** [Wed AM](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (Power 10 performance, ~20 TOPS) + [Tue AM](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (hybrid GPU/Power approach)

### Part 6: POC Scope Definition — What does the POC look like?

**Known:** 10 historical projects, project-level matching only. Component-level matching deferred. Batch processing model (overnight AI, morning review).

**Unknown:** Contract terms, effort estimate, timeline — derived from feasibility assessment results. Next step after Go: create POC contract based on findings.

**Transcript:** [Wed AM](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (10-project scope, project-level first, batch processing model)

### Part 7: Local Model Feasibility — Can Power 10 produce Kriteriennachweis?

The Kriteriennachweis documents (Part 2) were produced using the current model via an automated playbook. REKERS asked: can this be done locally on Power 10?

**What we're testing:** Whether a local vision-language model on IBM Power 10 can extract the same 9 criteria from the same project PDFs and produce comparable Kriteriennachweis output. Not a chat system — batch processing with tolerance for longer runtimes.

**Model selection — priority stack:**

| Priority | Model | Architecture | Size (Q4_K_M) | Rationale |
|----------|-------|-------------|---------------|-----------|
| Primary | [Qwen3.5-9B](https://ollama.com/library/qwen3.5) | Native early-fusion multimodal | ~6 GB | Latest (Mar 2, 2026). Jointly trained on text + image tokens — outperforms Qwen3-VL on visual understanding benchmarks. |
| Fallback 1 | [Qwen3-VL 8B](https://ollama.com/library/qwen3-vl) | Encoder-based vision | ~6 GB | Stable (Oct 2025). Proven Ollama integration, GGUF published. |
| Fallback 2 | [Qwen2.5-VL 7B](https://ollama.com/library/qwen2.5-vl) | Encoder-based vision | ~5 GB | Most proven. Used as baseline in prior sessions. |

Multimodal is required — the 5x criteria extraction advantage from visual PDF reading (Part 2) depends on it. All candidates fit Power 10's 123 GB RAM comfortably. The primary model (Qwen3.5) uses early-fusion training where text and image tokens are learned jointly, eliminating the quality drop seen in bolt-on vision encoders at smaller model sizes. Fallback order: try primary, fall to next if ppc64le build fails or vision quality is insufficient.

**Architecture — script-driven pipeline:** Python orchestrates the full extraction workflow; the model handles criteria extraction only. The Ralph playbook's logic (file patterns, priority ordering, stop conditions) is deterministic — encoding it in a script is more reliable than asking a 9B model to reason about orchestration.

| Step | Owner | What happens |
|------|-------|-------------|
| File inventory | Script | Glob project directory, categorize by type (PDF/EML/XLSX) |
| EML extraction | Script | Parse email body text, extract attachments to disk |
| XLSX parsing | Script | openpyxl reads Kranliste, Leistungsverzeichnis |
| PDF rendering | Script | Render pages to images at configurable DPI (default: 110) |
| Priority ordering | Script | Apply Ralph playbook file patterns (Angebot*, *Grundriss*, *Kran*) |
| Criteria extraction | **Model** | Receive page image or text, return structured criteria per the 9-criterion schema |
| Result assembly | Script | Merge per-file extractions into Kriteriennachweis markdown |

Fresh context per extraction call — no accumulated state between files. The model sees one input (page image, email text, or parsed data) and returns criteria found. Context is cleared between calls, keeping prompts simple and model load predictable.

**Measured performance** (qwen3-vl:8b on Power 10, March 2026):

Server: AlmaLinux 10 (ppc64le), POWER10 5 cores × 8 SMT = 40 threads, 123 GB RAM, 2.9 GHz. Ollama v0.13.5 (locally built container). Backend: `libggml-cpu-power10.so` with VSX/MMA active, FlashAttention enabled.

| Config | Resolution | Image tokens/page | Prompt eval | Gen | Per page | Throughput |
|--------|-----------|-------------------|-------------|-----|----------|------------|
| 20T × 1 parallel | 72 dpi | 525 | 24.3 tok/s | 13.9 tok/s | 63s | 1.0 pg/min |
| 20T × 1 parallel | 110 dpi | 1,154 | 21.6 tok/s | 12.2 tok/s | 172s | 0.35 pg/min |
| 20T × 1 parallel | 150 dpi | 2,045 | 18.6 tok/s | 10.4 tok/s | 386s | 0.16 pg/min |
| **20T × 2 parallel** | **72 dpi** | **525** | **24.3 tok/s** | **13.9 tok/s** | **67s / 2 pages** | **~1.8 pg/min** |
| 40T × 1 parallel | 72 dpi | 525 | 29.3 tok/s | 6.5 tok/s | 58s | 1.0 pg/min |
| 40T × 1 parallel | 150 dpi | 2,040 | 22.7 tok/s | 5.6 tok/s | 377s | 0.16 pg/min |

**Optimal config: 20 threads × 2 parallel requests at 110 dpi.** More threads improve prompt eval but degrade token generation due to SMT contention. Parallel requests nearly double throughput without increasing CPU above 50%.

**Project-level time estimates** (110 dpi, 20T × 2parallel):

| Scenario | Pages/project | Per project | 14 projects |
|----------|--------------|-------------|-------------|
| All deduped pages | ~275 avg | ~6.5 hrs | ~91 hrs (~4 days) |

File inventory (sampled from 2 projects): projects contain 61–91 PDFs each, but ~50% are EML attachment duplicates (`_1`, `_2` suffixes). Large documents dominate page counts — Vorfertigungspläne (93–99 pages) and Leistungsverzeichnisse (99 pages) inflate totals. Deduped priority page count averages ~275 per project. The 50-page estimate from the initial projection significantly underestimated actual data volume.

**Threading:** llama.cpp defaults to 1 thread on Power architecture (GitHub [#9623](https://github.com/ggml-org/llama.cpp/issues/9623)) — marked stale upstream, no fix merged. Workaround: set `OLLAMA_NUM_THREADS` explicitly. Default (1 thread) gives ~10x worse performance than configured (10+ threads). This applies to all llama.cpp-based runtimes (Ollama, llama.cpp, llama-cpp-python).

**Strategic context:** This benchmark is an internal investment trigger for Thomas and Ulrich. The performance table makes the case visible: current speed processes 14 projects overnight, optimization brings it to under two hours. Feeds into the IBM Power AI Inference Optimization Initiative ([#713](https://github.com/DaveX2001/deliverable-tracking/issues/713)) and the broader product line question: Power 10 as deployment target for operations below a complexity threshold, M3/M5 Ultra for operations above it.

**Quality benchmark — A/B comparison:**

Gold standard: the existing 14 Kriteriennachweis documents produced by the frontier model via the Ralph playbook. The local model processes the same projects from the same files.

| Metric | Method | Threshold |
|--------|--------|-----------|
| Criteria hit rate | Count criteria found (local) vs found (frontier), per project | ≤20% degradation acceptable (e.g., 4/5 where frontier found 5/5) |
| Value correctness | Spot-check extracted values against source documents | Manual review on a sample — not automated |
| Sample size | Minimum 1 project, target 3+ | More projects = higher confidence, but each takes ~6.5 hrs |

Runtime is a measured metric, not a pass/fail threshold. The validation measures processing time per project and reports it. Ulrich determines production viability from the measured results — runtime tolerance is not a validation blocker.

**Extraction design — matching the Ralph playbook scope:**

The Ralph playbook processes all file types: PDFs (visual reading), EML body text, and XLSX files (Kranliste, Leistungsverzeichnis). The local model validation uses the same scope for a fair A/B comparison.

| Source type | Script preparation | Model input | Prompt |
|------------|-------------------|-------------|--------|
| PDF pages | Render to image at 110 dpi (configurable) | Page image | "Extract criteria from this document page" + 9-criterion schema with hints |
| EML body | Parse text from email | Body text | "Extract criteria from this email" + schema |
| XLSX | openpyxl → structured text | Parsed cell data | "Extract criteria from this data" + schema |

Processing: all priority files per project, no early stopping. This is a benchmark — processing time itself is a metric. The script feeds files in Ralph playbook priority order (Angebote → Plans → Specialized) but does not skip files when criteria are found. Deduplication of `_1`/`_2` EML attachment duplicates is handled at the script level before model invocation.

**POC Validation — Happy-Case Design (Project 35764)**

The general architecture above describes the full production pipeline. Before building it, a POC validates the core hypothesis: can a 9B vision model extract the same criteria that the frontier model found via the Ralph playbook? The POC isolates extraction quality from file selection — file selection is a separate engineering problem solved later.

**Ralph → Script transformation:** The Ralph playbook uses a frontier model to both orchestrate (decide which files to read, in what order, when to stop) AND extract (read documents, identify criteria, write evidence). The POC script replaces orchestration with deterministic Python; the 9B model handles only extraction — receiving one page image per call and returning structured criteria.

**Validation target:** Project 35764 (Neubau Werkhalle van Eckendonk). Gold standard: [Kriteriennachweis 35764](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/criteria-evidence-35764) — 8/9 criteria found by the frontier model. Chosen for its rich documentation and manageable file count.

**Three-lane architecture:**

*Lane 1 — Pre-work (done once, before script runs):*

| Step | What | Output |
|------|------|--------|
| EML extraction | Extract attachments from EMLs to disk | `_extracted/` dirs (already done for all 14 projects) |
| Hash dedup | md5 hash all PDFs, keep one per unique hash | 113 files → 66 unique (43 PDFs + 20 EMLs + 3 XLSX) |
| CSV context | Feed Anfragen.csv header + project row as prompt context | ~630 chars — model receives structured data alongside page images |

Dedup uses content hashing only — no filename-based suffix stripping. Hash comparison is both simpler and more accurate: suffix-named files (e.g., `Lageplan_1.pdf`) sometimes contain genuinely different content from their base version. Hash dedup keeps these; suffix dedup would incorrectly remove them. Validated on 35764: suffix+hash yields 63 files, hash-only yields 66 — the 3 extras have confirmed different content.

*Lane 2 — Orchestration (Python script):*

| Step | What | Detail |
|------|------|--------|
| O1: Read manifest | Load JSON manifest | `{"pdfs": [...], "emls": [...]}` — file paths grouped by type, human-reviewable |
| O2: Render PDFs | pdftoppm at 72 dpi | One PNG per page. Start at 72 dpi (~525 image tokens/page), escalate to 110 dpi only if extraction quality drops |
| O3: EMLs | Skipped for POC | All criteria in 35764 came from PDFs. EMLs are forwarding correspondence — criteria-rich data is in the PDF attachments already in `_extracted/` |
| O4: Call Ollama | POST /api/generate per page | JSON mode (`format: "json"`), full prompt per call (stateless), CSV row as context |
| O5: Persist JSONL | Append each result incrementally | Crash-safe at ~4.2 hr runtime. Each line: `{"file": "path", "page": N, "response": {...}, "timing": {...}}` |

No aggregation or report assembly in the script. The JSONL output is the deliverable — analysis and comparison against the gold standard happen externally (via Claude or manual review).

*Lane 3 — AI (model's job per call):*

Input: one page image (72 dpi PNG, base64) + prompt (CSV context + 6-criterion schema in German). Output: JSON per the extraction schema. The model extracts criteria or returns an empty array for noise pages.

Per-page JSON schema:

```json
{
  "criteria": [
    {
      "name": "Höhe",
      "value": "14,35 m (First); OK-Stütze +13,35 m",
      "passage": "Alle vier Ansichten (Westen, Norden, Osten, Süden) zeigen konsistente Höhenangaben: Geländeoberkante ±0,00, Hallenfirst bei +14,35, Bürobereich bei +7,25. Der Schnitt in BA-1000d bestätigt: 14,35 Gesamthöhe, +12,34 Unterkante Binder, +0,30 Sockelelement OK."
    }
  ]
}
```

- `name`: one of 6 extraction criteria (Gebäudetyp, Höhe, Kran, Dachlasten, Baustoff, Dachbegrünung)
- `value`: extracted value in original language/units
- `passage`: supporting text from the page
- Empty `criteria: []` for noise pages (Fugenschnur specs, invoices, contracts)

The 3 CSV criteria (Bauort, Kundenreferenz, Kalkulatorenwissen) are excluded from extraction — already known from Anfragen.csv. The CSV row is included as prompt context only.

**Project 35764 — validated file inventory:**

| Metric | Value |
|--------|-------|
| Total files (all types) | 113 |
| After hash dedup (PDFs only) | 43 unique PDFs |
| Total pages | 239 |
| Criteria-relevant PDFs (Ralph-pattern match) | ~20 |
| Noise PDFs (no pattern match) | ~23 |

No noise filtering — the model processes all 43 PDFs (239 pages). Noise handling is part of the test: the model must return empty arrays for pages containing sealant specs, invoices, or contract documents. File selection intelligence is a later engineering problem.

**Runtime projections (Project 35764, Power 10):**

| DPI | Per page | 239 pages | With 2× parallel |
|-----|----------|-----------|-------------------|
| 72 dpi | 63s | ~4.2 hrs | ~2.2 hrs |
| 110 dpi | 172s | ~11.4 hrs | ~5.7 hrs |

POC starts at 72 dpi. If extraction quality is insufficient (criteria hit rate drops >20% vs gold standard), escalate to 110 dpi and re-run.

**Machines:** Power 10 (current: 5 cores × 8 SMT = 40 threads, 123 GB RAM), second Power 10 (specs TBD — potentially more cores/threads, pending [#866](https://github.com/DaveX2001/deliverable-tracking/issues/866)), Mac Studio (128 GB RAM, Apple Silicon — benchmarks pending), MacBook Pro (128 GB RAM — fallback). Power 10 benchmarks exist; Mac Studio and second Power 10 benchmarks to be run after script is operational.

**Validated on Power 10** (March 2026 — SSH investigation on `wph-ki-tst`):

| Item | Status | Evidence |
|------|--------|----------|
| Ollama on ppc64le | ✅ Running | Locally built container v0.13.5, `libggml-cpu-power10.so` loaded |
| Vision API | ✅ Works | qwen3-vl:8b correctly reads German Angebot PDFs, identifies "REKERS Betonwerk GmbH" |
| PDF rendering | ✅ Works | pdftoppm (poppler) renders at 72/110/150 dpi, all resolutions produce valid image tokens |
| Threading workaround | ✅ Applied | `OLLAMA_NUM_THREADS=20` confirmed via 50% CPU utilization (20/40 threads) |
| Parallel inference | ✅ Works | `OLLAMA_NUM_PARALLEL=2` nearly doubles throughput (2 pages in 67s vs 1 in 63s) |
| German text extraction | ✅ Partial | Model reads document headers, project names, section titles correctly. Full criteria extraction not yet benchmarked. |

**Inference runtime stack:**

| Tier | Runtime | Use case |
|------|---------|----------|
| Primary | Ollama (already installed on Power 10) | Server + HTTP API, vision model support, simplest setup |
| Fallback | llama.cpp direct (build from source with `-mcpu=power10 -O3`) | More control over threading, MMA optimization |
| Integration | llama-cpp-python (embed in script) | No separate server process, tighter Python integration |

All llama.cpp-based runtimes share the same underlying binary and the same threading issue workaround. The Ollama container already includes the Power10-optimized backend (`libggml-cpu-power10.so`), but building from source with explicit `-mcpu=power10` flags may unlock additional MMA performance.

**Undefined — Libel kernel optimization engagement:** [#713](https://github.com/DaveX2001/deliverable-tracking/issues/713) (IBM Power AI Inference Optimization Initiative) runs parallel to this validation. Libel's kernel-level optimization targets the 10x speedup from current to optimized performance. Engage ASAP — does not block #952 validation but determines production viability. → Meeting agenda (Thomas/Ulrich coordination).

**POC Run 1 Results (Project 35764, March 2026)**

Two extraction runs completed on Power 10 (20 threads, 72 dpi, think:false) using the three-lane architecture. Both models processed 135 of 240 manifest pages — 105 pages were missing from Power 10 (21 PDFs: 4 fixable via symlink, 17 never uploaded).

| Metric | qwen3.5:4b | qwen3.5:9b |
|--------|-----------|-----------|
| Pages processed | 135 | 135 |
| Criteria extracted | 287 | 273 |
| Pages with real criteria | 96 (71%) | 71 (53%) |
| Errors | 0 | 0 |
| Avg generation speed | 6.5 tok/s | 5.7 tok/s |
| Total wall time | 3.2 hrs | 3.9 hrs |
| Criteria hit rate vs gold | 5/6 (0% degradation) | 5/6 (0% degradation) |

Both models pass the ≤20% degradation threshold defined in the quality benchmark. All 5 positive extraction criteria found; Dachbegrünung correctly identified as absent. The 4B model is more aggressive (attempts criteria on 71% of pages vs 53% for 9B) but noisier. CSV criteria (Bauort, Kundenreferenz, Kalkulatorenwissen) extracted deterministically from Anfragen.csv.

**Extraction quality — per-criterion assessment:**

The hit rate metric (5/6) measures whether a criterion was found at all. A deeper analysis of extraction quality reveals per-criterion signal-to-noise ratios (SNR): of all non-null values extracted, what percentage are correct or useful?

| Criterion | 4B SNR | 9B SNR | Gold value recoverable? |
|-----------|--------|--------|------------------------|
| Gebäudetyp | 96% | 99% | Yes — "Werkhalle" dominant signal (39×/56×), Bürotrakt mentioned separately |
| Höhe | 32% | 12% | Partial — +14,35 m found (9B), +13,35 m found (4B), but Hakenhöhe 8,70 m never extracted |
| Kran | 38% | 30% | Yes — ZLK 40t/10t, 50t total, optional 20t all present across pages |
| Dachlasten | 43% | 64% | Yes — exact gold values (0,45/0,15/0,20 kN/m²; WLZ2; SLZ2) found 3× per model |
| Baustoff | 93% | 98% | Mostly — Spannbeton, Portlandzement CEM I found; B500(A) only in passage text |
| Dachbegrünung | n/a | n/a | Correctly negative — both models return absence |

Gebäudetyp, Dachlasten, and Baustoff have strong signal (>90% SNR or exact gold values found). Höhe and Kran are noisy — the correct values exist across multiple pages but are buried among irrelevant numbers (crane data sheet dimensions, floor loads, span lengths). No single extraction entry contains the complete answer for any criterion except Dachlasten.

The models have complementary strengths: 4B uniquely finds the optional 20t cranes and Bürotrakt +7,25 m height. 9B uniquely finds the ABUS manufacturer name and ST1660/1860 Spannstahl specification. A combined extraction (using both models' output jointly) would recover more of the gold standard than either model alone.

**Aggregation implication:** A naive majority-vote over per-page extractions recovers only 2/6 criteria (Dachlasten, Dachbegrünung). The other 4 require semantic merging — an LLM-based aggregation pass that combines fragments across pages. Aggregation is outside the scope of this extraction POC (#1079) but is a prerequisite for producing Kriteriennachweis-quality output.

**Performance analysis — timing breakdown and scaling behavior:**

Aggregate timing for the 4B run (135 pages, 3.2 hrs) splits into three roughly equal parts:

| Component | % of wall time | What it is |
|-----------|---------------|------------|
| Prompt eval | 37% | Vision encoder processing the page image |
| Generation | 31% | Model writing the JSON criteria response |
| Ollama internal | 32% | ~30s fixed cost per request inside Ollama, not inference |

No single component dominates. The 32% overhead is entirely inside Ollama — HTTP and PDF rendering contribute zero measurable time. Each light page incurs a consistent 30s internal cost (29.3–32.3s, near-zero variance) that is neither prompt eval nor generation. Heavy pages show only 1.6s of this overhead. Root cause unconfirmed — likely vision pipeline initialization per API call on ppc64le. Investigation pending ([#1079 comment](https://github.com/DaveX2001/deliverable-tracking/issues/1079#issuecomment-4029578788)).

**Super-linear scaling on vision tokens:** Page images tokenize to between 778 and 2,300 prompt tokens depending on visual complexity. Processing time scales super-linearly with token count:

| Token bucket | Pages | Avg wall time | ms/token | % of total time |
|-------------|-------|--------------|----------|----------------|
| <800 (text docs, simple pages) | 123 (91%) | 75s | 25 | 80% |
| 800–1800 (plans, drawings) | 8 (6%) | 133s | 81 | 9% |
| >1800 (large technical drawings) | 4 (3%) | 329s | 126 | 11% |

A 2.9× increase in tokens causes a 14.9× increase in processing time — a 5.1× amplification factor, consistent with quadratic attention cost in the vision encoder. The 12 expensive pages (technical drawings with rich visual detail) consume 20% of total runtime despite being only 9% of pages.

**Page-type bottleneck split:** The two page types have fundamentally different bottlenecks:

- **Fast pages** (91%): Ollama-internal-overhead-bound — 30s fixed cost per request dominates the 75s wall time. Parallel processing (NUM_PARALLEL=2 or two instances) pipelines this overhead.
- **Expensive pages** (9%): prompt-eval-bound — 82% of per-page time is vision encoding. Image size reduction (lower DPI, cropping) would have outsized impact due to super-linear scaling.

**Primary speed lever: parallelism.** Because 80% of total runtime comes from the overhead-bound fast pages, parallel processing (filling Ollama's internal idle time between inference stages) is the highest-impact optimization. Making the 12 expensive pages cheaper saves ~13%; doubling throughput via parallelism saves ~50%.

**Run 2 design — head-to-head parallelism comparison:**

Run 1 established that extraction quality is sufficient (0% degradation) and identified parallelism as the primary speed lever. Run 2 tests two approaches to achieving parallel throughput on the same 135 pages:

| Config | Model | Parallelism | Threads | Expected throughput |
|--------|-------|-------------|---------|-------------------|
| **A** | qwen3-vl:8b | NUM_PARALLEL=2 (native) | 20 | ~3-4× (14.8 tok/s + pipeline) |
| **B** | qwen3.5:4b | Two Ollama instances (ports 11434/11435) | 10 + 10 | ~2× (concurrent requests) |

Config A is preferred: qwen3-vl's encoder-based vision architecture supports NUM_PARALLEL=2 natively (validated in pre-POC benchmarks at 1.8 pg/min). Its 14.8 tok/s generation speed is 2.3× faster than qwen3.5:4b (6.5 tok/s). Config B is the fallback if qwen3-vl's extraction quality is materially worse than qwen3.5.

**Prompt tweaks (applied to both configs):**

1. **Kill "nicht angegeben" padding:** Add instruction "Gib NUR Kriterien zurück, die du tatsächlich gefunden hast. Keine 'nicht angegeben' Einträge." Run 1 showed 35% of 4B's criteria output was padding — wasted generation tokens.
2. **Tighten Dachbegrünung:** Add "Dachbegrünung: Nur wenn explizit im Text erwähnt. Grüne Farben in Zeichnungen sind kein Hinweis." Prevents false positives from visual interpretation of drawing colors.
3. **Require numbers with context:** Replace "Extrahierter Wert mit Einheit" with "Wert muss eine konkrete Zahl mit Einheit enthalten. Beschreibender Kontext ist erwünscht." Reduces vague descriptions while preserving the rich descriptive style of the gold standard.

**Controlled variables:** Same 135 pages, same manifest, same DPI (72), same extraction schema. Only model and parallelism config change. Output: JSONL per config, timing comparison, criteria quality comparison.

The winning config becomes the standard for benchmarking on the remaining 3 systems (Power 10 Ostermann, Mac Ultra, Nvidia 4xxxx IITR) — each producing a row in the Systemvergleich comparison table.

**Systemvergleich — commercial artifact framing ([#1081](https://github.com/DaveX2001/deliverable-tracking/issues/1081)):**

The POC benchmark feeds into a Wilsch-branded system comparison document (Systemvergleich) for Thomas and Ulrich. Template: Ulrich's existing hardware comparison format (POW004/POW005 series — side-by-side specs, bar charts, percentage improvements). Reference: [Ticket #19884 Systemvergleich](https://mail.google.com/mail/u/0/#all/19cd6474c8b275d6).

**Comparison matrix (4 systems × multiple models):**

| System | Status | Access |
|--------|--------|--------|
| IBM Power WPH | ✅ Run 1 complete (4B + 9B) | SSH via VPN |
| IBM Power Ostermann | Pending | Available |
| Mac Ultra (Mac Studio) | Pending ([#1080](https://github.com/DaveX2001/deliverable-tracking/issues/1080)) | Local |
| Nvidia 4xxxx IITR | Pending | Available |

Same Ollama runtime on all systems. Same extraction script and prompt. The variable is hardware + model combination. Each benchmark produces one row per model.

**Per-row metrics:** System specs (CPU, RAM), model used, wall time per project, prompt eval speed (tok/s), generation speed (tok/s), criteria hit rate (X/6).

**Undefined — value-level quality presentation:** The hit rate metric (X/6) is clear for technical stakeholders. How to present per-criterion signal quality (SNR, value correctness) to Thomas/Ulrich in the Systemvergleich format is unresolved. The extraction data supports a detailed breakdown, but the commercial document needs a simpler representation. → Meeting agenda (Thomas/Ulrich alignment on quality metric presentation).

---

## Source

**Transcripts:**

- [Tue AM — Kalkulation & Metadatenextraktion](https://app.fireflies.ai/view/01KFZ9MSE9WZ179TAYYC30C6JC) (abandoned component-level approach)
- [Tue PM — Lastmodellierung & Suchparameter](https://app.fireflies.ai/view/01KFZR74V0N0P1S589KBKZCBYH) (pivot discussion)
- [Wed AM — Datenexport & Ranking-System](https://app.fireflies.ai/view/01KG1V9Y791YHSAVP7GJABEJAA) (adopted project-level approach — most critical)
- [Wed Follow-up — Priorisierung & UI](https://app.fireflies.ai/view/01KG25S4S0MVB8Q8F3CEXG60N4) (validation design direction)

**Artifacts:**

- [V006 Offer (contract basis)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/v006-offer-wph)
- [KBB Workshop 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/KBB_2026_01_27-28_Workshop2)
- [Gelbe Felder Definition](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/gelbe-felder-definition)

**Issue:** [#629 — REKERS: KI-gestütztes Angebotssystem - Workshop 2](https://github.com/DaveX2001/deliverable-tracking/issues/629)

**Sessions:**

- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects/fc9f193e-2d54-4ea9-bcc1-5dfd2dd35f2d.jsonl` (initial design doc creation)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/00e6a51f-cd0d-432f-b5f4-f2a41dd07a4d.jsonl` (data deep dive, criteria mapping, validation design)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/cb7381c0-65f8-42cd-bf0a-a6a09bbfe357.jsonl` (Part 1 extraction pass — 16 uncertainties resolved)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-REKERS--poc/772b96f8-d646-428c-bde6-9eeddf0418b5.jsonl` (Part 2 extraction pass — 12 uncertainties resolved, evidence template formalized)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-REKERS--poc/a661e34f-b89f-48bf-bc6c-bd590536fae4.jsonl` (Part 7 extraction pass — local model feasibility, Power 10 benchmarks)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-REKERS--poc/5459d299-6d80-466e-aca9-bedb19f41f8f.jsonl` (Part 7 extraction pass 2 — model selection, architecture, Power 10 SSH benchmarks, file inventory)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-REKERS--poc/50ea279f-b40e-4985-80cb-26f328aa43f5.jsonl` (Part 7 extraction pass 3 — POC validation script design, three-lane architecture, 35764 file inventory + hash dedup)
- `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-REKERS--poc/f0df4a4a-830c-490c-8124-b7ea4a878756.jsonl` (Run 1 retrospective + Run 2 design — extraction quality analysis, super-linear scaling discovery, 30s Ollama overhead finding, Systemvergleich framing)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

