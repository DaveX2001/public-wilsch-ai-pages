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
| Tree index generation + validation | 3h | 4h | Qwen 3.5 9B with think:false |
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

