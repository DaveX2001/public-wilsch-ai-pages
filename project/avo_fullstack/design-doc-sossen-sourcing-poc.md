---
publish: true
---

# Design Doc: Soßen Sourcing POC — Recipe Similarity Algorithm
[[client-avo]]

Design for the recipe similarity POC: compare 62 test recipes, find Top 5 most similar per query, validate against known duplicate pairs.

---

## Problem Statement

AVO-Werke has ~8000 sauce recipes accumulated over years. Duplicates and near-duplicates are invisible — no system compares recipes systematically. Manual comparison takes 2+ person-days just to display the data. Redundant recipes mean redundant production runs, inventory, and QA.

**Core proxy:** The underlying question is whether two recipes taste similar enough to consolidate. Taste cannot be measured algorithmically — ingredient composition (which ingredients, in what proportions) serves as the computable proxy. All similarity criteria are different lenses on this same proxy.

**Preconditions:**
- 62 R-prefix test recipes available for POC development (from 197 total entries in `alle.jsonl`)
- Two comparison criteria agreed: ingredient overlap, percentage similarity
- Process-based similarity (manufacturing timing) is out of scope — deferred by agreement

**NOT the problem:**
- Production optimization / batch sizing (post-POC)
- Preventive duplicate detection from product requests (post-POC)
- Process-based similarity (deferred — data not in ERP, agreed in WS2-Session3a + Jan 15 meeting)
- Category-level matching (future enhancement, blocked on Stammdaten CSV)

---

## Success Definition

**Goal:** For 62 test recipes: query any recipe → receive Top 5 most similar recipes with similarity scores. POC validates or refutes the hypothesis that ingredient overlap + proportion similarity is a proxy for taste similarity.

**Success:**
1. Behrens's 5 known duplicate pairs each appear in their partner's Top 5 results
2. Two criteria (overlap ratio, percentage similarity) scored per pair — weights calibrated from known pairs
3. Output: ranked list per query (recipe ID + score). Ingredient drill-down is next iteration
4. The evaluation chain itself is demonstrated as reusable — adding a future criterion follows the same validate-against-pairs workflow

**Done test:** Query each recipe from Behrens's 5 known pairs → partner appears in Top 5. If yes for all 5 → baseline validated. Otherwise → adjust weights and iterate. A negative finding (no weight combination achieves this) is also a valid POC result — it means the current criteria don't capture taste similarity.

**Blocked by:** 5 known duplicate pairs from Herr Behrens ([meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/meeting-agenda-behrens-validation)).

---

## Approach

### Part 1: Data Loading

Load `alle.jsonl` → filter to **R-prefix entries only** (62 final products) → each recipe becomes a map: `{material_id: percentage}`. Materials are already exploded (flattened across all Vormischung levels), summing to exactly 100% (within floating-point precision, verified).

**Input:** `alle.jsonl` (197 total entries, now in `data/` directory)

**Output:** 62 recipe maps (`R`-prefix), each `{material_id: percentage}` — this is the frame of reference for all similarity scoring.

**Data format:** Raw JSONL entries contain `materials[]` array with `{article_number, percentage}` objects — trivially transformable to flat `{material_id: percentage}` maps.

**Entry type breakdown (from data analysis):**

| Prefix | Count | Used in POC? |
|--------|-------|-------------|
| R | 62 | Yes — final products |
| V | 54 | No — Vormischungen (pre-mixes, Phase 2) |
| RV | 18 | No — Rezept Vormischung (premix recipes, Phase 2) |
| RZ | 9 | No — Rezept Zwischenmischung (intermediate mixes, Phase 2) |
| (none) | 47 | No — raw materials / intermediates |
| B | 6 | Unknown — verify with Mattis |

**Why only R-prefix?** Each R entry's `materials[]` array is already fully exploded — all Vormischungen (V), premix recipes (RV), and intermediate mixes (RZ) have been resolved down to their base raw material IDs with final percentages summing to 100%. The non-R entries are already contained within the R entries. The `structure[]` field on R entries records which Vormischungen were exploded (traceability only, not used for similarity).

> **⚠️ Undefined:** B-prefix classification unresolved — may change pool size. → [Mattis agenda, Topic 1](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/meeting-agenda-mattis-data-confirmation)

**What we ignore:** The `structure` field (Vormischung traceability — deferred to Phase 2 per WS2-Session1 agreement).

**Basisvariante filter:**
- [WS2-S3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) agreed: only Variant 0 (base recipe) enters comparison
- **Definition:** Same recipe can have multiple article numbers for different packaging sizes or customer-specific labels — all sharing identical ingredients and percentages
- Filtering to Variant 0 keeps one entry per actual recipe, removing ~80% packaging/customer duplicates
- Data analysis confirms no variant duplicates among 62 R-prefix entries (confirm with Mattis)

> **⚠️ Undefined:** Basisvariante filtering status in test data unconfirmed. → [Mattis agenda, Topic 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/meeting-agenda-mattis-data-confirmation)

**Material ID consistency:**
- Material IDs are globally consistent — same ID = same ingredient across all recipes
- 134 unique materials, 84% shared across multiple recipes
- Suffix variations (e.g., 00004 vs 00004B) = different quality grades of same base substance
- POC treats suffix variants as **distinct** — both exist in pool but do NOT match each other in Jaccard/Bray-Curtis
- Per [WS2-S1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs): "dürfen nicht automatisch zusammengeführt werden"
- Confirm treatment with client

> **⚠️ Undefined:** Suffix variant treatment (distinct vs grouped) unconfirmed. → [Mattis agenda, Topic 3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/meeting-agenda-mattis-data-confirmation)

**Source:**
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (44:47) — "Auflösung der Rezepturen auf Zutatsebene ohne Vormischungen" (resolve to ingredient level without premixes)
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (25:26) — "Vormischungen sollten separat verglichen werden" (premixes compared separately = Phase 2)
- [WS2-Session3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — Basisvariante (Variant 0) only, filters ~80% duplicates
- [Jan 29 transcript](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873) — Mattis confirms exploded materials structure, summing to 1.0
- Data analysis (2026-02-09): 197 entries, 62 R-prefix, 54 V-prefix, 134 unique raw materials
- Data verification (2026-02-11): all R-prefix entries sum to 1.0, no variant duplicates detected, 134 unique material IDs globally consistent, 84% shared across recipes

### Part 2: Similarity Scoring

For each recipe pair, compute two scores. Each is a proxy lens on the core question: "do these recipes taste similar enough to consolidate?"

**Criterion 1 — Overlap (Jaccard similarity):** "Do they share the same ingredients?"

Binary set comparison: `|A ∩ B| / |A ∪ B|` where A and B are sets of material IDs. Score 0–1, where 1.0 = identical ingredient lists. Label: **Überlappung**.

**Criterion 2 — Proportion (Bray-Curtis similarity):** "For shared ingredients, how close are the amounts?"

For shared materials, measure proportional distance: similarity = `1 - sum|a_i - b_i| / sum(a_i + b_i)`. Score 0–1, where 1.0 = identical proportions. Naturally weights high-percentage ingredients more than trace ingredients. Label: **Anteil**.

**Combined weighting:** Empirical — start equal weights (50/50), let Behrens's validation pairs reveal which criterion matters most. Transcripts emphasize "quantity-based similarity" ([WS2-S3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw)), suggesting Proportion may ultimately dominate — hypothesis to test, not pre-commit.

**Design context:** Algorithmic approach chosen over vector/embedding because similarity is quantity-based, not semantic — client needs explainable, per-criterion scores, not opaque numbers.

**Proxy Architecture (future enhancement — all blocked on Stammdaten CSV):**

Beyond the two POC criteria, the Stammdaten CSV defines additional proxy dimensions for taste similarity. The [Tabellenstruktur XLSX](https://docs.google.com/spreadsheets/d/1GEXqdkFxCtA4xSUjFpZTBUBPk6O_Jx8G) (WS2, 2025-11-08) specifies the exact schema — three separate axes, each a different lens on comparability:

**Axis 1 — Einkaufsgruppen (column 6, 761 categories):** Compositional substitutability. Materials in the same purchasing group are assumed functionally equivalent — if Recipe A uses 00004 and Recipe B uses 00004B, and both share an Einkaufsgruppe, the recipes are substitutable despite different IDs. Two sub-approaches to test empirically:

**(a) Count-based:** Of unshared materials, what fraction have a partner in the same Rohstoffkategorie? Tells you differences are "soft" (substitutable) vs "hard" (genuinely different).

**(b) Algorithm-based:** Group materials by category, sum percentages within each group, re-run Jaccard + Bray-Curtis on category-aggregated data.

**Axis 2 — Certification Flags (columns 7–13, boolean 0/1 per material):** Regulatory and dietary compatibility. Seven flags in the Tabellenstruktur:

| Flag | Column | Type |
|------|--------|------|
| Bio | Bio-Klassifizierung | Organic |
| Halal | KZ. HALAL | Islamic dietary |
| Naturland | Kz. Naturland | Stricter organic |
| Kosher | Kz. Kosher | Jewish dietary |
| Vegan | Kz Vegan | No animal products |
| Vegetarisch | KZ. Vegetarisch | No meat products |
| TK | KZ. TK | Frozen handling |

VLOG (non-GMO) is derived from article text, not a dedicated flag — different extraction mechanism.

**Axis 3 — Country Exclusions (not yet in schema):** Pre-filters, not scoring criteria. Recipes must NOT contain materials from specific countries (Non-China, Non-Russland, Non-Ukraine per [von Schultz email, Oct 30 2025](Rohstoffkriterien zur Vergleichbarkeit.pdf)). Tabellenstruktur notes "still needs discussion" — unclear if material numbers have 1:n relationship to suppliers/countries.

**Assumption:** Suffix variants (e.g., 00004 vs 00004B) likely share Einkaufsgruppe — B-suffix likely indicates Bio-certified variant (data pattern: wrapper articles contain 100% of B-suffix counterpart). To be verified once Stammdaten CSV available. None are POC scope.

**Proxy coverage (vs [Tabellenstruktur](https://docs.google.com/spreadsheets/d/1GEXqdkFxCtA4xSUjFpZTBUBPk6O_Jx8G)):**

| Axis | What it measures | Columns | Status |
|------|-----------------|---------|--------|
| Composition | Ingredient overlap + proportions | 1–3 | ✅ Have (alle.jsonl) |
| Einkaufsgruppen | Ingredient substitutability | 6 | ❌ Needs Stammdaten CSV |
| Certifications | Bio, Halal, Naturland, Kosher, Vegan, Vegetarisch, TK | 7–13 | ❌ Needs Stammdaten CSV |
| LIMS Categories | Artikelgruppe + Rohstoffkategorie | 14–15 | ❌ Needs Stammdaten CSV |
| Country Exclusions | Non-China, Non-Russland, Non-Ukraine | — | ❌ Not in schema yet |

> **⚠️ Undefined:** 1 of 5 axes delivered. Stammdaten CSV blocks the remaining four. → [Mattis agenda, Topics 4 + 5](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/meeting-agenda-mattis-data-confirmation)

**Source:**
- [Pflichtenheft §3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — FR-02 (Material_ID matching), FR-02a (overlap ratio), FR-04 (percentage similarity), FR-08 (category matching)
- [WS2-S1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (49:53) — "Ähnlichkeitsschwellen flexibel: 2%, 5%, 10%", 3% fluctuation acceptable
- [WS2-S1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (12:45) — "nicht nur auf Namen, sondern auf die tatsächliche Zusammensetzung" (actual composition, not names)
- [WS2-S3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — algorithmic > vector ("keine semantische, sondern mengenbasierte Ähnlichkeit"), 761 Einkaufsgruppen, data quality poor
- [WS2-S3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I) — 98% similarity target, user-adjustable tolerance sliders

### Part 3: Ranking + Output

Rank all 62 R-prefix recipes by combined similarity score for a given query. Return **Top 5** (configurable N) with per-criterion scores using client-facing labels.

**POC output format** (plain scores — ingredient drill-down is next iteration):

```
Query: R261800

Rang  Rezept    Überlappung  Anteil  Gesamt
#1    R261700   85%      97%         91%
#2    R482000   62%      78%         70%
#3    R954700   58%      82%         70%
#4    R445100   51%      65%         58%
#5    R887200   48%      71%         60%
```

**Top N:** Default 5, configurable. Workshop discussed "five to ten" (WS2-S3a), but Top 5 is the stated POC criterion.

**Decided:** Plain scores for POC. Full vision (post-POC): Übersicht-style Excel with ingredients, quantity differences, percentage matches, and color-coded deviation markers per WS2-S3b. The POC validates the algorithm; the Excel output is a presentation layer added after validation passes.

**Source:**
- [Pflichtenheft §9](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — Top 5 output, three criteria separately
- [WS2-Session3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — "fünf bis zehn ähnlichsten Rezepturen" (five to ten)
- [WS2-Session3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I) — Excel output with color-coded deviations (full vision)
- Übersicht.xlsx — AVO's existing recipe comparison format (from AVO Seafile)

### Part 4: Validation (Blocked on Behrens pairs)

**POC scope:** Validate the algorithm against Behrens's 5 known pairs. This is algorithm validation ("does it find expected pairs?"), not discovery ("find new duplicates"). New discoveries during POC are observed but not formally validated — discovery becomes the production goal.

**Similarity type:** Near-duplicates with flexible thresholds (2%, 5%, 10% per WS2-Session1), not exact matches. Quantity-based, not semantic. The specific similarity band that matters will emerge empirically from the data and Behrens's pairs.

**Core hypothesis:** Overlap (Jaccard) + Proportion (Bray-Curtis) is a sufficient proxy for taste similarity.

**Back-pressure mechanism:** Behrens's 5 known pairs function as the POC's automated feedback loop. Each algorithm iteration (weight change, threshold adjustment) can be validated against these pairs without human-in-the-loop review. The evaluation chain is stable — proxies change (currently Overlap + Proportion, later Category, future TBD), the validation loop stays the same.

**Evaluation table (Behrens fills in):**

| Rezeptur_A | Ähnlichste (Pflicht) | Weitere (optional) | Anmerkung |
|------------|---------------------|-------------------|-----------|
| R...       | R...                | R..., R...        | Freitext  |

- **Ähnlichste:** Primary similar recipe (required). Weitere optional if Behrens knows additional similar recipes.
- **Anmerkung:** Free-form text — Behrens writes whatever reasoning he has. No structured confidence levels.
- **Living document:** Not static ground truth. After algorithm runs, revisit results with Behrens. His updated reasoning when seeing algorithm output informs which proxy to explore next.

**XLSX artifact (2 sheets):**
1. **Rezeptpool:** 62 R-prefix recipes listed by ID + ingredient count — Behrens selects pairs from this
2. **Bewertungstabelle:** Evaluation table template with example data showing the format

→ [XLSX-Vorlage (Google Drive)](https://docs.google.com/spreadsheets/d/1yQyUYYF_cPZRsjUIE9ZUv-AHl2-74k4N)

**Pair selection:** Only constraint — pairs must come from the 62 R-prefix pool. No diversity requirement. If pairs are homogeneous (all obvious duplicates), that's back-pressure signal: algorithm passes trivially → harder test cases needed in next iteration.

**Success criterion:** K1 partner appears in query recipe's Top 5 (configurable N). Test bidirectionally — query A→check B in Top 5 AND query B→check A in Top 5 — as sanity check (both criteria are mathematically symmetric, so results should match).

**Calibration workflow:** Run algorithm → sweep weight combinations → find which combo best ranks Behrens's pairs → his free-text reasoning provides post-hoc explanatory context for why the winning weights capture taste. Search strategy is an implementation detail.

**Outcomes:**

The POC is a bounded experiment: test two proxies, deliver a verdict, done. We expect Overlap + Proportion to produce meaningful results — they are established similarity measures applied to compositional data. If the results show these two proxies aren't sufficient, the POC delivers a clear direction for which additional proxies to explore — proxies already discussed in Workshop 2 (Einkaufsgruppen, certification flags) that need to be designed and scoped as a follow-up phase in discussion with the client.

- **Hypothesis confirmed:** A weight combination exists where all 5 K1 partners appear in their query's Top 5. POC result = the algorithm + winning weights.
- **Hypothesis weakened:** Best combo achieves 3–4/5. Investigate failing pairs — may indicate edge cases or missing criterion (e.g., Category needed sooner than expected).
- **Hypothesis refuted:** No weight combination achieves majority match. Overlap + Proportion alone don't capture taste similarity — more proxies needed. POC result = analysis of which proxy axis to explore next (informed by Behrens's reasoning), taken into a follow-up design discussion with the client.

All three outcomes are valid POC results. A negative finding is still a finding.

> **⚠️ Undefined:** The implications of "hypothesis refuted" need further discussion — a negative result doesn't mean failure, it means exploring additional proxies for taste. See [Feb 9 transcript](https://drive.google.com/file/d/1qZFdhyr9D_VWjH5YqqdcghFMAfU9i0ZS/view) (end of conversation). → [Behrens meeting agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/meeting-agenda-behrens-validation)

**Two Behrens interactions:**
1. **Interaction 1 (meeting):** Build shared understanding of the validation process. Present XLSX as visual stimulus. Agree on format together. Behrens fills initial pairs.
2. **Interaction 2 (async email):** Send results XLSX with multiple result sets (internally from different weight combos). Behrens sees ranked lists with per-criterion scores (Überlappung, Anteil, Gesamt), annotates which results match his intuition. False positives emerge organically — Behrens flags unexpected entries in Top 5.

**Blocked by:** 5 known duplicate pairs from Herr Behrens (meeting agenda item).

> **⚠️ Undefined:** Evaluation table format + pair selection pending Behrens meeting. → [Behrens agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/meeting-agenda-behrens-validation)

**Source:**
- [Pflichtenheft §9](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing) — evaluation methodology, success criteria
- [Nov 8 transcript](https://app.fireflies.ai/view/01K9J1PK1YHP4HXC5VD0NJGWT2) — 3→5 pairs negotiation (Thomas pushed for 5, customer compliance concern)
- [Jan 29 transcript](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873) — evaluation table action item for Behrens
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (49:53) — flexible thresholds 2%, 5%, 10%
- [WS2-Session3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw) — "automatisierte Qualitätskontrollen finden Dubletten" (system expected to discover unknown duplicates)
- [WS2-Session1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs) (54:09) — "lebendes System" with user feedback loop

---

## Source

- **Transcripts:** [WS2-S1](https://docs.google.com/document/d/1KRF-IktLEuM8wlyGnXjg-yfK3KoQR-zqwzDlHyqqXGs), [WS2-S3a](https://docs.google.com/document/d/1Y6dz78yEu1E1LG4-YFqruj5gxSsJqil8vvU6tbOu_cw), [WS2-S3b](https://docs.google.com/document/d/1NhjxZnugaiWLg415HbC7nB_ybDPOCaCbfM0zF3L_n0I), [Jan 15 Fireflies](https://app.fireflies.ai/view/01KEXRBAF5495NKTGZ5N6FHFVE), [Jan 29 Fireflies](https://app.fireflies.ai/view/01KG3DKZHD0EVFK4620C86B873)
- **Pflichtenheft:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/pflichtenheft-ki-projekt-sossen-sourcing)
- **Agreed Schema:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/agreed-json-schema)
- **Data artifacts:** [`data/`](https://github.com/MariusWilsch/AVO__poc/tree/main/data) — all recipe data, workshop documents, evaluation templates
- **Session (Part 1 deepening):** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO--poc/451ac158-4b2b-4501-8dca-bd9645888e7d.jsonl`
- **Session (Parts 2–4 deepening):** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO--poc/ed3ed7d2-a582-4987-b15b-d59a0f7763de.jsonl`
- **Session (Part 1 extraction + Part 2 simplification):** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO--poc/953e038a-ae96-4265-a20b-b7d9d1763d87.jsonl`
- **Session (Part 4 extraction):** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO__poc/67641e8a-a998-4c2c-b7b6-67f7191bc2f4.jsonl`
- **Session (Extraction pass — all parts, proxy architecture):** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-AVO__poc/883714f4-7ce5-43be-8a46-a238f9feb2e8.jsonl`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

