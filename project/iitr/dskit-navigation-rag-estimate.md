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

- Chapter-referenced answers (from PageIndex tree path)
- Stellmacher/Kraska qualitative review with iteration cycles within budget

---

## Festpreis (Zielkorridor)

| Work Package | Min | Max | Notes |
|--------------|-----|-----|-------|
| Infrastructure (PageIndex + Ollama + OpenWebUI + test harness w/ LLM judge) | 6h | 8h | Test harness includes working evaluation judge |
| Data preparation (PDF, templates ZIP, web chapters, CSV) | 4h | 6h | Anwenderleitfaden PDF on server; web chapters re-extracted cleanly |
| Tree index generation + validation | 3h | 4h | Qwen 3.5 9B with think:false |
| Evaluation + iteration (>90% target) | 10h | 12h | Purely RAG iteration — harness built in Pkg 1 |
| Deployment + documentation | 1h | 2h | Staging IS production |
| **Total** | **24h** | **32h** | |

**Zielkorridor: EUR 2.880 – EUR 3.840** (24–32h × EUR 120/h)

---

## Out of Scope

- Dach KI / umbrella system integration
- Avatar system
- Keycloak authentication
- Images/screenshots in responses


---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

