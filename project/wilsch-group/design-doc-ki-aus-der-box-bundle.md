---
publish: true
---

# KI aus der Box — DGX Spark Bundle
[[client-wilsch-group]]

Design doc for the DGX Spark bundle product section on the FSO seminar website — hardware, software expertise, and setup services as a packaged commercial proposition.

---

## Problem Statement

The FSO seminar website (ki-performance page) ends with a generic CTA — "Vergleichstest anfragen" — that offers no concrete product, no price, and no commitment path. Seminar attendees see benchmark results (DGX Spark 200x faster than Power 10) but have no actionable next step beyond "contact us."

This design doc defines the DGX Spark bundle — "KI aus der Box" — as a packaged product with hardware, software expertise, and setup services at a concrete price point. The bundle section replaces or augments the current "Nächster Schritt" footer on the KI-Performance page.

This is NOT about the KI-Performance page content (benchmarks, hardware comparison, analogies) — that already exists. This is about the commercial proposition at the end: what the customer buys, what it costs, and what they get.

**Preconditions:**
- FSO seminar is 2026-03-27 — the bundle section must be live before the event
- DGX Spark hardware is available at ~€5,000 (Richtpreis, volatile — IBM raised Spyre component prices 55% in March)
- Two DGX Sparks are operational with vLLM, benchmarks completed (Rekers pipeline: 1,319 pages/hr)
- KI-Performance page exists at /ki-performance with sections: Hero → ComparisonTable → Benchmark → WarumSoSchnell → Kaufentscheidung → PageFooter
- Existing CTA infrastructure: "Jetzt anrufen" + "Gespräch vereinbaren" on homepage, "Vergleichstest anfragen" on KI-Performance page

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A concrete, priced product offering on the FSO website that gives seminar attendees a clear next step — from "interesting benchmarks" to "I want this" |
| **Success** | Attendees see the bundle, understand what they get (hardware + software + setup), see the price (~€10K), and pick up the phone or book a Gespräch. Ulrich can point at it during the seminar and say: "KI aus der Box — 5.000 Euro Hardware, 5 Tage Einrichtung, läuft." |
| **Done test** | Can Ulrich walk a Geschäftsführer through the bundle section in 30 seconds and get a "tell me more"? If YES → the section works. |

---

## Approach

### Part 1 — Bundle Structure

The bundle follows Ulrich's 40-year IBM sales pattern: Hardware + Betriebssoftware + Dienstleistung. Three layers, one price.

| Layer | What the customer gets | Price |
|-------|----------------------|-------|
| **1 — Hardware** | NVIDIA DGX Spark (GB10 Blackwell, 128 GB RAM, 2 TB NVMe). Runs models up to 200B parameters locally. Fits on a desk (150×150×50mm, 1.2 kg). | ~€5.000 (Richtpreis) |
| **2 — Software Expertise** | Production serving engine (vLLM), chat interface (Open WebUI / LibreChat), data connectors (MCP, Vector DB, DB Toolkits), authentication — configured for the customer's environment. | Included in setup |
| **3 — Einrichtung** | 5 Manntage: infrastructure setup, platform + connectors, validation with real data, user training + handover. | ~€5.000 |

**Bundle total: ~€10.000** — versus €250.000+ for IBM Spyre + Power 11.

**Framing:** Not "a GPU box" — it's your local platform for making data accessible. The DGX Spark is the sovereign engine; MCP + the software stack is what connects it to your SQL, Confluence, PIM, and file servers. Cloud option available — mentioned but not the lead.

**Product name:** "KI aus der Box" — triple meaning: physically a box, works out of the box, and the surprise factor ("DAS ist die KI-Maschine?").

### Part 2 — Software Expertise (Layer 2)

The selling point is not the specific tool — it's that we've already figured it out across 4+ client projects. "Sie profitieren davon, dass Sie sich nicht selbständig mit diesen Neuerungen herumplagen."

**Serving Engine:** vLLM via eugr/spark-vllm-docker. Production-grade, model-agnostic — swap any model in one command. Nightly builds, tested before publish. Two advantages over the pre-installed Ollama: (1) 1.4x faster per request while running a 7.5x bigger model, and (2) continuous batching — 8 users can query simultaneously while Ollama queues them one at a time. Same hardware, the difference is software expertise. Addresses the staleness fear: new model drops tomorrow, one command to update.

**Clustering (add-on):** "Was ist, wenn das Modell nicht auf eine Box passt?" — two DGX Sparks linked via ConnectX-7 → 256 GB unified memory, runs models up to 397B parameters (Qwen3.5-397B confirmed). +€5K for the second unit. We know how to set it up — launch-cluster.sh handles the orchestration.

**Chat Interface:** Both leading open-source ChatGPT-like chat interfaces are included:

| Capability | Included |
|-----------|----------|
| ChatGPT-like UI | ✓ |
| SSO / OIDC | ✓ |
| Role-based access (Admin/User/Groups) | ✓ |
| Model-level permissions per group | ✓ |
| Speech-to-Text / Text-to-Speech | ✓ |
| File upload + RAG | ✓ |
| MCP tool calling | ✓ |
| Code interpreter | ✓ |
| Agent marketplace | ✓ |
| MIT-licensable (rebrandable) | ✓ |

We know both platforms and pick the right one for the customer's environment. Already proven across 4+ client projects.

**Data Connectors — the Dolmetscher layer:** Four connector types, each for a different data problem:

<img src="https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/ki-aus-der-box-telefonzentrale.png" width="100%" alt="Telefonzentrale — KI routet Anfragen an die richtige Datenquelle" />

| Type | When to use | Example |
|------|------------|--------|
| MCP | Structured data with APIs | SQL, Confluence, SharePoint, email |
| Vector DB | Unstructured semantic search | PDFs, file servers, product docs |
| DB Toolkits (MindsDB) | Legacy databases, many connectors OOB | AS/400, Access via ODBC |
| Skills | Company knowledge as structured instructions | Product catalog logic, naming conventions, internal processes — the AI knows your company |

**Analogy — Dolmetscher:**

Ulrich's one-liner: *"Ihre Daten sprechen sechs Sprachen — wir liefern den Dolmetscher, der alle versteht."*

Extended: SQL spricht Deutsch, Confluence spricht Englisch, Ihre PDFs sprechen Bildsprache, die AS/400 spricht Plattdeutsch. Bisher mussten Sie selbst übersetzen — in jedem System einzeln suchen. Die KI ist der Dolmetscher. Wir liefern die Wörterbücher mit — und wissen, welches wo hingehört.

**Berechtigungskonzept:** Three auth layers, all mapping to patterns enterprises already use:
1. Chat UI groups — Open WebUI RBAC (Admin/User/Pending, group-based model access)
2. MCP DB permissions — restricted database user, same auth the customer already has
3. Vector metadata filtering — document sensitivity tags, C-level content filtered by role

**Licensing:** LibreChat = MIT (fully rebrandable). Open WebUI = BSD + branding restriction (≤50 users exempt, enterprise license for larger).

### Part 3 — Einrichtung (Layer 3)

5 Manntage, configurable. Day 1 and 5 are fixed; days 2-4 are the "build your own" zone.

| Tag | Category | Output |
|-----|----------|--------|
| 1 | **Infrastructure** | Box online, Tailscale remote access, vLLM serving, first model pulled |
| 2-3 | **Platform + Connectors** | Chat UI deployed, SSO/auth configured, base data sources wired (2-3 connectors) |
| 4 | **Validation** | Real customer data, end-to-end use case tested internally, then demoed to customer |
| 5 | **Einweisung + Handover** | Admin training (model management, user admin), user training (how to chat), handover documentation |

**Base vs. Add-ons:**
- **Base bundle:** DGX Spark + vLLM + Chat UI + 2 Manntage (infra + Einweisung) = "Private ChatGPT, lokal"
- **Recommended:** Full 5 Manntage with connectors + auth + validation = ~€10K total
- **Add-ons:** Additional connectors beyond base (extra Manntage per source), second DGX Spark for clustering (+€5K), ongoing Betreuungsvertrag (separate)

**Ongoing maintenance:** "Permanente Aktualisierung verfügbar" — model updates, new connectors, performance tuning. Scoped per customer in Workshop 1 (free). Not priced on the website — signals availability, kills the staleness objection.

**Reference case:** Ein Kunde aus der Fertigungsindustrie — 294 Seiten Dokumentenverarbeitung: 5 Tage auf Power 10, 13 Minuten auf DGX Spark. Faktor 200.

### Part 4 — Website Integration

**Position:** Replaces or augments the current "Nächster Schritt" section (PageFooter component) on /ki-performance. The bundle section sits after Kaufentscheidung — the natural next question after "which hardware?" is "what does it cost to get started?"

**Irresistible offer framing ($100M Offers pattern):**
- Price anchor with alternatives: IBM Spyre + Power 11 (€250K+), 300 ChatGPT accounts (€XX.000/Jahr), then the bundle (~€10K once)
- Value stack: all three layers visible (Hardware + Software + Setup)
- Price disclaimer: "Aktueller Richtpreis, kann sich ändern"
- Urgency: seminar-specific framing

**Scarcity:** "Nur 3 Pakete verfügbar" — or similar limited availability framing. Real constraint: Marius's setup capacity is finite (5 Manntage per customer).

**Entry point:** Workshop 1 (free, 1-1.5 hours) is the natural next step after seeing the bundle. Customer calls → free workshop → bundle scoped to their environment. Already the standard sales process — the bundle gives it a concrete anchor.

**CTA:** Links to existing infrastructure — "Jetzt anrufen" + "Gespräch vereinbaren." No new flow needed.

**Copy/headlines:** Deferred to David — direction is "KI aus der Box" as headline, value stack as body, price anchor as persuasion layer. Specific German copy to be written during implementation.

**Visual:** Telefonzentrale illustration for the data connector section (generated, stored at ki-aus-der-box-telefonzentrale.png). Additional visuals at David's discretion.

**Technical:** React component in KiPerformance.jsx, positioned between Kaufentscheidung and PageFooter (or replacing PageFooter). Existing design system: DM Sans font, #0A84FF blue accent, gradient CTAs.

---

## Source

- **Transcript (FSO bundle discussion):** [2026-03-25](https://drive.google.com/file/d/1Ct_aOzc6px9j3TSBy7xrteoja-FPb6E0/view) — lines 1780-2320, bundle definition with Ulrich
- **Transcript (Schäferbarthold WS01):** [2026-03-24 Fireflies](https://app.fireflies.ai/view/01KMGA10PQ07RZ3S7DGXPDQR26) — Poggemöller requirements, MCP/Vector explanation, cloud vs on-prem debate
- **Email (Poggemöller requirements):** [2026-03-23](https://mail.google.com/mail/u/0/#all/19d19a3dec2135c4) — 7-point requirements list
- **Hardware strategy:** [Design doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/hardware-strategie-lokale-ki-inferenz) — DGX Spark specs, pricing, Phase 1/2
- **DGX Spark infrastructure:** [Design doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-dgx-spark-infrastructure) — serving engine ladder, vLLM, phased deployment
- **vLLM container:** [eugr/spark-vllm-docker](https://github.com/eugr/spark-vllm-docker) — README, clustering, 397B recipe
- **vLLM vs Ollama benchmark:** hippocampus/project/rekers/vllm-vs-ollama-benchmark.md
- **FSO analogies meeting:** [2026-03-25 agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/meeting-agenda-fso-test-run-2026-03-25)
- **Website repo:** /Users/verdant/Documents/projects/non-billable/WILSCH-AI-INTERNAL__wilsch-ai-site (KiPerformance.jsx:800-866)
- **Issue:** [#1280](https://github.com/DaveX2001/deliverable-tracking/issues/1280)
- **Generated visuals:** /tmp/analogy-{01-dolmetscher,02-reiseadapter,03-telefonzentrale}.png
- **Session:** b982f95a-c532-43a6-9f1b-1247d112a47b

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

