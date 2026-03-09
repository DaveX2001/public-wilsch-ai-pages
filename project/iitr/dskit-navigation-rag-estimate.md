---
publish: true
---

# DS-Kit Navigation — Zielkorridor (Werkvertrag)
[[client-iitr]]

## Status

**DRAFT** — Hours pressure-tested (session 2026-03-09)

---

## Scope

Rebuild the DS-Kit Navigation system using PageIndex (vectorless, tree-based retrieval) on IITR's on-premise infrastructure. Full technical design: [Design Doc](dskit-navigation-design.md)

---

## Acceptance

- ≥26/29 (>90%) correct answers on Quelle-mapped test set
- Chapter-referenced answers (from PageIndex tree path)
- Stellmacher/Kraska qualitative sample review

---

## Festpreis (Zielkorridor)

| Work Package | Min | Max | Notes |
|--------------|-----|-----|-------|
| Infrastructure (PageIndex + Ollama + OpenWebUI + test harness w/ LLM judge) | 6h | 8h | Test harness includes working evaluation judge |
| Data preparation (PDF, templates ZIP, web chapters, CSV) | 4h | 6h | Anwenderleitfaden PDF on server; web chapters re-extracted cleanly |
| Tree index generation + validation | 3h | 4h | Dual-model: 30B-A3B for tree gen, 9B for runtime |
| Evaluation + iteration (>90% target) | 8h | 12h | Purely RAG iteration — harness built in Pkg 1 |
| Deployment + documentation | 1h | 2h | Staging IS production |
| **Total** | **22h** | **32h** | |

**Zielkorridor: EUR 2.880 – EUR 3.840** (24–32h × EUR 120/h)

### Validated Assumptions

- **PageIndex runs fully local** — 2 patches needed (tiktoken fallback + Ollama env vars)
- **All 29 test questions sourced** — Navigationssystem FAQ (19), Anwenderleitfaden PDF (3), DS-Kit Oberfläche chapters (7)
- **Data on server** — PDF available, web chapters need clean re-extraction, templates ZIP downloadable from portal
- **Dual-model strategy** — Qwen 3.5 30B-A3B (MoE, ~3B active) for tree generation quality, 9B for runtime speed (6-8s/query)

---

## Out of Scope

- Dach KI / umbrella system integration
- Avatar system
- Keycloak authentication
- Images/screenshots in responses

---

## Design Doc — Extraction Pass Handoff

The [Design Doc](dskit-navigation-design.md) was written for the previous Typesense approach. The following sections need extraction passes before implementation can begin:

| Section | What needs updating | Priority |
|---------|-------------------|----------|
| **Approach** (3 workstreams) | Replace Typesense pipeline with PageIndex tree generation workflow | High |
| **Current Deployment** | Update container list — PageIndex replaces Typesense + TEI stack | High |
| **Success Definition** | Confirm 28/29 target still holds with PageIndex retrieval | Medium |
| **Source — Data Files** | ~~Verify all source data accessible~~ ✅ Verified 2026-03-09 — all 29 Quelle mapped | Done |
| **Undefined: dual-answer trigger** | Resolve "when to include navigation guidance" — PageIndex tree path may solve this naturally | Low |

**Approach:** Each section gets its own extraction pass (SCOPE → SURFACE → RESOLVE → UPDATE → ASSESS). Start with Approach — it defines the implementation path.
