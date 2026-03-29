---
publish: true
---

# IITR Contract Extension — Commercial Framework
[[client-iitr]]

Design doc for the financial framework conditions (finanzielle Rahmenbedingungen)
governing the direct engagement between Wilsch AI Services OÜ and IITR Datenschutz GmbH.

---

## Problem Statement

Wilsch AI Services needs to formalize commercial terms for a direct engagement with
IITR Datenschutz GmbH. The previous relationship operated through an intermediary
(Roman Rolnik) who is no longer involved. Eckhard Kraska has requested written
"finanzielle Rahmenbedingungen" covering two services: server support and AI development.

The problem is not writing a contract — it's defining the pricing model, scope boundaries,
and engagement structure that will govern the relationship going forward. Decisions made
here set precedent for all future IITR work.

**Preconditions:**
- Eckhard Kraska's email (Feb 26) explicitly requests financial framework conditions
  for commissioning — two items: "exklusive Betreuung unseres Servers" and
  "Weiterentwicklung unserer Datenschutz-KI"
- Zielkorridor proposal (Feb 24) established the bi-weekly fixed-price delivery model
  with IITR's leadership (Eckehard, Sebastian, Marvin)
- DS-Kit Pflichtenheft and extension documents establish the deliverable pricing pattern
  (36h × 80 EUR = 2,880 EUR original; 800 EUR extension)
- Marius is the sole developer with full institutional knowledge of all four IITR AI systems

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Eckhard Kraska receives written financial framework conditions covering both server support and AI development — sufficient for IITR to issue a formal commission |
| **Success** | Marius can draft contract documents from this design doc and Ulrich can confirm or give feedback without needing to re-clarify commercial terms |
| **Done test** | "Can I produce two contract documents (Wartungsvertrag + Werkvertrag framework) from this design doc alone?" — If YES → design complete |

---

## Approach

### 1. Contract Structure: Two Independent Agreements

The engagement consists of two independent contracts, mapping directly to the two items
in Eckhard's email:

| Contract | Type | Covers |
|----------|------|--------|
| **Wartungsvertrag** (Dienstleistungsvertrag) | Service contract | "Dedizierte Betreuung unseres Servers" — ongoing maintenance and support |
| **Werkvertrag-Rahmenvereinbarung** | Deliverable framework | "Weiterentwicklung unserer Datenschutz-KI" — project-based AI development |

**Why independent:** Maintenance should survive if development pauses. IITR may deprioritize
AI development for a quarter while their servers still need running. Independent contracts
protect both revenue streams and give IITR flexibility to adjust each engagement separately.

**Wording:** Replace "exklusive Betreuung" with "dedizierte Betreuung" in contract language.
Eckhard means dedicated sole contact, not non-compete exclusivity. "Dedizierte" avoids any
future misinterpretation.

### 2. Wartungsvertrag — Pricing

Two revenue components:

**Hourly rate (T&M):** 120 EUR/h
- Negotiation anchor: 120 EUR/h (opening position)
- Floor: 100 EUR/h (what IITR paid through Roman — anything below undervalues the work)
- Derivation: Marius's base rate to Roman was 80 EUR/h. Roman added ~25% → IITR paid ~100 EUR.
  The 120 EUR provides 20% negotiation wiggle room above IITR's reference point.

**Bereitschaftspauschale (standby fee):** 300 EUR/month
- Purpose: revenue protection against zero-income months
- Industry standard: German IT service providers charge 300–900 EUR/month for single-server
  SLAs. 300 EUR is at the low-to-conservative end.
- Dual function: covers both response availability AND pre-project scoping (Pflichtenheft
  creation for next projects is absorbed, not billed separately)
- Lead arguments for IITR:
  1. **Institutional knowledge** — Marius is the only developer who knows the system.
     Rebuilding that knowledge costs months, not 300 EUR/month. IITR already experienced
     this when Roman left.
  2. **Answering their own request** — Eckhard asked for "dedizierte Betreuung." The
     standby fee formalizes what both parties already want.
- Predicted objection: "Why pay when nothing breaks?"
- Counter: "You're not paying for hours. You're paying to keep me actively engaged
  with your system."

### 3. Wartungsvertrag — Terms

| Term | Value | Rationale |
|------|-------|-----------|
| **Duration** | 3-month auto-renew | Quarterly review rhythm — natural business checkpoint |
| **Notice period** | 30 days, symmetric | Both parties can exit with 30-day notice before quarter end |
| **Start date** | On signing | Effective immediately — no deferred start |
| **Payment terms** | Net 30 | German B2B standard |
| **VAT** | Reverse charge | Estonian entity (Wilsch AI) → German entity (IITR) = intra-EU B2B |
| **Reaction time** | 24 hours, German business hours (Mon–Fri 8–18 CET) | Solo operator — no RTO commitment |

The auto-renew eliminates administrative burden. No quarterly re-signing needed. The
bi-weekly meetings serve as informal review — if either party is unhappy, it surfaces
in the meetings long before formal cancellation.

**Extended hours (resolved):** The Bereitschaftspauschale covers business hours only
(Mon–Fri 8–18 CET). Weekend and holiday incidents are available at premium rates — +50%
werktags outside business hours (180 EUR/h), +100% Sonn- und Feiertage (240 EUR/h) — but
without a standing weekend SLA commitment. The surcharge structure incentivizes IITR to use
business hours while preserving emergency access. Contract sent March 2 with this structure.

**Undefined:** Betreuung scope boundary — Marius's position is application + middleware
(Docker daemon, OS services, Nginx, SSL, disk, GPU/CPU config), with explicit exclusion of
hardware (Primeline warranty) and network (Hetzner SLA). Awaiting Kraska's agreement in
meeting March 4. See [Meeting Agenda Topic 1](#1-betreuung-scope-boundary-and-liability).

### 4. Werkvertrag — Pricing Model

**Festpreis per project.** Each development project gets its own Pflichtenheft with a
fixed price agreed before work starts. IITR sees the total price, not the hourly rate
behind it.

- Internal estimation rate: ~100 EUR/h (80 EUR base + 25% uplift for direct relationship)
- This rate is never disclosed to IITR — it's the calculation tool, not the contract term
- Billing: on final delivery (no milestone billing at current 2–4K project scale)
- Milestone billing becomes relevant if concurrent projects arise (aggregate unbilled WIP)

**Bi-weekly meetings are delivery rhythm, not pricing events.** Project pricing is agreed
once at project start. The bi-weekly touchpoints are progress reviews and scope adjustments
for the next two weeks — not re-pricing sessions. The contract language should explicitly
distinguish these two levels to prevent Eckhard reading "vereinbart in jedem Meeting" as
a new price negotiation every two weeks.

### 5. Werkvertrag — Engagement Lifecycle

Each project follows a defined lifecycle:

```
SCOPING (Pflichtenheft)
  └── Separate phase — Marius scopes and estimates
  └── Cost absorbed into Bereitschaftspauschale (not billed separately)
      ↓
AGREEMENT
  └── IITR accepts the Festpreis
      ↓
EXECUTION (bi-weekly sprints)
  └── Progress reviewed at each touchpoint
  └── Scope adjustments within agreed project
      ↓
DELIVERY + BILLING
  └── Project complete → invoice sent (net 30)
      ↓
NEXT PROJECT → back to SCOPING
```

**"Weiterentwicklung unserer Datenschutz-KI"** is a framework, not a single project.
It covers all future AI development work across the portfolio (RAG-V1, RAG-V2, DS-Kit,
Avatar). Each project within the framework gets its own Pflichtenheft and Festpreis.
The pipeline is driven by Kraska's priorities, decided in the bi-weekly meetings.

**Current project:** DS-Kit Navigation (2,880 EUR POC + 800 EUR extension). The original
project stalled under the Roman intermediary arrangement — its non-delivery was the catalyst
for the partnership dissolution. Completing this delivery is the first proof point of the
direct relationship. Next project depends on IITR's priorities after DS-Kit is delivered.

---

## Source

- **Transcript:** [Marius × Ulrich — Contract Extension Pricing Strategy (Feb 27)](https://app.fireflies.ai/view/01KJEVQ0Q6XWZNZP9P1FTPB2VW)
- **Email:** [Eckhard Kraska — Finanzielle Rahmenbedingungen (Feb 26)](https://mail.google.com/mail/u/0/#all/19c9a411906d76ee)
- **Artifacts:**
  - [Zielkorridor-Vorschlag](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-zielkorridor-vorschlag)
  - [Zielkorridor Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-zielkorridor-meeting-agenda)
  - [DS-Kit Festpreisangebot](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-rag-estimate)
  - [DS-Kit Extension Pflichtenheft](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/pflichtenheft-dskit-navigation-extension)
- **Issue:** [#954 — Prepare extension contract for signing](https://github.com/DaveX2001/deliverable-tracking/issues/954)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/9f14b021-2a4f-4da9-8925-b30c9eb7d426.jsonl
- **Email:** [Sebastian Kraska — u.a. Staging System, Vergütung (Mar 3)](https://mail.google.com/mail/u/0/#all/19cb5178aea60dd3) — confirms March 4 meeting, contract review
- **Email:** [Sebastian Kraska — Austausch KI-System (Mar 3)](https://mail.google.com/mail/u/0/#all/19cb5170512d9850) — Zoom invite for March 4
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/2c6e2b5f-7d4a-493a-8bc7-d441c20c6090.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

