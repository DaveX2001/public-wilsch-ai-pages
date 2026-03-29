# Scoping Estimate: Klimafolgenschutz Website
[[client-abtmayr-reichert]]
---
publish: true
---

Ballpark estimate (Richtpreisangebot) for the Klimafolgenschutz website build. Non-binding indicative pricing within the T&M Rahmenvertrag (€120/hr). All 7 sub-issues scoped via SURFACE/RESOLVE methodology. Estimates assume AI-assisted development (Claude Code).

---

## Estimate Summary

| Sub-Issue | Description | Estimate (hrs) | Status |
|-----------|-------------|---------------|--------|
| #757 | Foundation + CMS + Deploy Pipeline | 5-6 | Buildable |
| #758 | Design System + Branding | 1-2 | Buildable |
| #759 | Static Sections + Legal Pages | 2-3 | Buildable |
| #760 | Projekte Section (CMS-Connected) | 2-3 | Buildable |
| #761 | Mitgliedschaft Section (Forms + Pricing) | 2-4 | Buildable |
| #762 | Stripe Payment Integration | 4-6 | BLOCKED (Vereinsregister) |
| #763 | Zertifizierung Section | 2-3 | DEFERRED (content from Michael) |
| — | Cross-cutting (QA, responsive, revisions) | 3-5 | — |
| **Total** | | **~21-32 hrs** | |
| **At €120/hr** | | **~€2.520 - €3.840** | |

Design work already invested: ~3,5 hrs (alignment value).

---

## Resolved Uncertainties

### #757 Foundation + CMS + Deploy Pipeline (~5-6 hrs)

**Server state (verified Feb 13):** WILSCH-AI-SERVER has Docker v29, Caddy (systemd), PostgreSQL (native), Node v25.3. Disk at 27% after cleanup (318GB free). 27 containers running, all healthy.

**Payload CMS v3:** First deployment. Scaffold + Docker Compose + Caddy site block + Vercel pipeline = heavily AI-assisted (~2 hrs). Collection schema design (projects, memberships, content, legal) requires human data modeling decisions (~2-3 hrs) — AI implements the TypeScript configs once decisions are made.

**Vike (React + Vite SSR/SSG):** First time for team. Learning buffer accounted in frontend sub-issues.

**DNS:** Client dependency (Michael provides Ionos credentials, ~0.5 hr setup).

### #758 Design System + Branding (~1-2 hrs)

tweakcn color config + shadcn/ui component install (trivial — npx commands). CSS variables convention in [React frontend guidelines](https://github.com/veloxforce/velox-global-adrs/commit/ba5e956). Century Gothic substitute (web font, ~0.5 hr). Michael picks from 3 color variants = client dependency. Project-specific component wrappers emerge during page builds (#759-#763), not upfront.

### #759 Static Sections + Legal Pages (~2-3 hrs)

AI generates all sections from design doc spec — David reviews and tweaks visuals. Hero (2 CTAs, Beratung anfragen = mailto), Prozess (5-step visualization), Footer (contact + legal links). Impressum + Datenschutz = e-Recht24 paste + style match. Copywriting from GenSpark prototype + AI rewrite. Newsletter: **OUT of scope**.

### #760 Projekte Section (~2-3 hrs)

AI generates tile components + Payload API integration (~1 hr). Image sourcing: Unsplash + AI via OpenRouter (~0.5 hr). Content seeding from design doc into CMS (~0.5 hr). Summary-only tiles in v1.

### #761 Mitgliedschaft Forms + Pricing (~2-4 hrs)

AI generates both forms (Gemeinde + Einzelperson) from field spec + validation (~1-2 hrs). Pricing display from CMS data (~0.5-1 hr). Email notification on submit via Payload webhook (~0.5-1 hr). 3-4 placeholder municipality tiers (CMS-managed, Michael confirms brackets later).

### #762 Stripe Payment Integration (~4-6 hrs) — BLOCKED

AI follows Stripe docs for 3 checkout integrations (SEPA, Invoicing, Card donations) — but payment logic needs careful human verification (~2-3 hrs). 3 webhook handlers: AI generates boilerplate, David verifies business logic (~1-2 hrs). Spendenbescheinigung: 2 extra fields (~0.5 hr). Test mode first, production cutover when Vereinsregister clears.

### #763 Zertifizierung Section (~2-3 hrs) — DEFERRED

Content undefined — waiting on Michael. Estimated as equivalent to Projekte section (text + visualization, AI-generated). Flagged as uncertain.

### Cross-Cutting (~3-5 hrs)

- Responsive/mobile-first: real work — visual iteration on breakpoints across all sections
- Cookie consent: evaluate at launch — may not be needed if no analytics and Stripe redirects off-site
- Analytics: **OUT of scope** (add later if Michael requests)
- QA/testing: 10% buffer on total
- **Revision rounds:** 2 rounds for whole site, AI applies revisions faster. Additional rounds = T&M.

---

## Rate Strategy

| Role | Rate | Notes |
|------|------|-------|
| Architect (Marius) | €120/hr | Design work, alignment meetings |
| Developer (David) | €120/hr baseline | Negotiation room: developer rate could flex down |

Wiggle room: if Michael pushes back on total, developer rate can be adjusted. Architect rate stays at €120/hr.

---

## Scope Extensions (Not Included)

Michael proposed two extensions (Feb 13 message) — NOT in this estimate:

1. **Interactive needs assessment page** — municipalities check off existing infrastructure (heat protection, digitalization, sensors). Diagnostic questionnaire feeding into membership flow.
2. **"Moorhuhn" fun/engagement page** — gamification element.

Both need spec-design pass before estimating. Separate issue to be created.

---

## Infrastructure Costs (Recurring)

*To be detailed in next session.*

- Vercel: Free tier likely sufficient for initial traffic
- VPS (Payload CMS share): portion of existing WILSCH-AI-SERVER cost
- Domain: klimafolgenschutz.com at Ionos (Michael's existing cost)
- Stripe fees: per-transaction (no monthly fee)

---

## Key Links

| Resource | Link |
|----------|------|
| Design Doc | [Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website) |
| Meeting Agenda (payment) | [Zahlungseinrichtung](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/meeting-agenda-klimafolgenschutz-payment-setup) |
| Framework Contract | [Rahmenvertrag T&M](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/rahmenvertrag-tm-2025-12-16) |
| React Frontend Guidelines | [velox-global-adrs](https://github.com/veloxforce/velox-global-adrs/blob/main/README-REACT-FRONTEND.md) |
| Parent Issue | [#726](https://github.com/DaveX2001/deliverable-tracking/issues/726) |

---

## Source

- Scoping session: `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL__soloforce/5b5fc3d1-06ae-4f97-a8c4-945ade6484b6.jsonl`
- Meeting transcript (Feb 9): [Google Drive](https://drive.google.com/file/d/1PtCYWZKiVPwz3Llykvju89R2suLZIk1W/view)
- Design doc methodology: [hippocampus](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/design-doc-methodology)
- Server cleanup: Disk 93% → 27% (Feb 13 — deleted unused Docker images)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

