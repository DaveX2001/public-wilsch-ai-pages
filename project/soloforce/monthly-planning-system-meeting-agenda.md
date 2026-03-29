---
publish: true
---

# Monthly Planning System — Meeting Agenda
[[coo-position]]

## Meeting Goal

Resolve remaining undefined elements of the Monthly Planning System. Extraction pass 3 defined: testable hypothesis, March docket (5 packages, 3 leads), shutdown ritual redesign (package-level selection, two tracks), and revenue model (10K/month agency target). Remaining items require deeper probing or empirical evidence from the first month's run.

1. **Revenue & pipeline** — revenue target grounding, sales pipeline revival, pre-decisions
2. **Operational gaps** — ~~fire handling~~, ~~forcing function integration~~, Traceline section, multi-terminal model
3. **Strategy updates** — SO update, position transition methodology, month-end evaluation

## Pre-Read

- [Monthly Planning System Design Doc](monthly-planning-system-design)
- [March Monthly Plan](monthly-plan-2026-02-23-to-2026-03-31) — the active docket
- [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router) — position progress dashboard
- [Contract Strategy #847](https://github.com/DaveX2001/deliverable-tracking/issues/847) — retainer model context
- [Stakes Visibility Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/stakes-visibility-forcing-function) — forcing function drivers
- [2025 Sales Pipeline](https://docs.google.com/spreadsheets/d/1zFrb-2B9KufHqOT0fafJoZbNenLNivgN4eYeE5DRMoQ/edit?usp=sharing) — lead tracking baseline

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Revenue target grounding
⏱️ 15 min

The March plan targets 10K/month agency revenue (retainers + fixed-price). Current recurring: ~6K (Archibus 3K + UWI 3K). Gap: ~4K. The 10K figure emerged from financial reasoning: 5K expenses, 5K profit → 60K/year. But "10K because it sounds good" is not grounded.

- Goal: 360K principal at ~10% or 10K/month earning capability as safety net
- Traceline success fees are the scalable extra, not the controllable base
- Current pipeline: Stafford (retainer), Rekers (fixed-price), Christoph (TBD)
- IITR and Rohdex have unclear contract models (legal dispute, SLA respectively)
- Fireflies transcript contains the revenue reasoning conversation

**To resolve:** Whether 10K/month is the right target based on actual expense structure and how the current pipeline maps to that target.

### 2. Contract model as project pre-decider
⏱️ 15 min

Retainers eliminate the "which projects" decision — contracts pre-decide everything downstream. Hourly billing is breaking because multi-project work makes single-dimension time tracking impossible. The retainer model from #847 is the mechanism that turns "which projects" from a monthly decision into a contractual given.

- Archibus is closest to the desired contract model (extended 6 months)
- Abtmayr-Reichert contract type was a mistake
- Stafford comp model (base + success fee) is being negotiated
- #847 owns this design space — this topic connects monthly planning to the contract work, not duplicates it

**To resolve:** How the contract model from #847 feeds into the monthly planning walkthrough as a pre-decided input.

### 3. Multi-terminal persona distribution
⏱️ 15 min

The package model runs one package per maker session with multiple Claude Code terminals: SE + JA + Developer + backup. The just-in-time benefit is that SE improvements feed directly into the same session's developer work — Session C verification becomes immediately available.

- How terminals coordinate within a single maker session
- Whether SE observations from one terminal can influence the Developer terminal in real-time
- Practical constraints: context windows, session isolation, attention splitting

**To resolve:** Operational model for multi-terminal maker sessions — how the human orchestrates 3-4 concurrent Claude Code personas.

### 4. Monthly success criteria
⏱️ 10 min

The monthly plan needs a "done" definition. Without milestones or deadlines on the stateless plan artifact, month-end assessment is qualitative — checking each package's "why" column against observed friction reduction. Whether this qualitative approach works or needs quantitative metrics is unknown until March runs.

- Plan artifact is deliberately stateless — no progress tracking on the page
- "Why" per package encodes direction: "orchestration friction" → did it reduce?
- Position stage advancement and project milestones are candidates but untested
- Deferred to after first month's empirical run (March 2026)

**To resolve:** Month-end evaluation methodology — what assessment looks like after running the docket model for one month.

### 5. Strategic Objective update
⏱️ 10 min

The SO (written Feb 3-4) describes a dev shop with a proprietary method. Current thinking has evolved: positions are products, the org structure itself is the vision product, the company is an AI research lab for organizational strategy. The three-product hierarchy (client deliverables → position methods → org system for acquisitions) is confirmed but not yet written into the SO.

- SO references: "What we sell: finished AI projects"
- Evolved: "What we sell: position methodologies. What we build: org system for boomer businesses."
- "Every employee I have is an AI use case" — the thesis in one sentence

**To resolve:** SO update scope — which sections change and how the three-product hierarchy integrates.

### 6. Position transition methodology gaps
⏱️ 15 min

The cross-cutting friction from the walkthrough: JA → Developer decomposition is a methodology gap, and Developer → Dev Lead review is a tooling gap. David completed IITR decomposition successfully — why he doesn't struggle while Marius does is an open question.

- JA → Developer: no documented process for turning design docs into developer issues
- Developer → Dev Lead: Review Queue designed in the ILR but not built
- David's success: potential evidence that the methodology works for non-experts
- Rohdex + evidence chain system: future agent/triage angle (deep undefined)

**To resolve:** Whether these gaps need their own design docs or can be resolved through the next project walkthrough.

### 7. Walkthrough-to-docket selection criteria
⏱️ 10 min

The walkthrough (Part 3) produces a full landscape of project-position packages. The docket (Part 7) contains only the active subset. The selection mechanism — how the full landscape gets cut to the active docket — is currently a judgment call during the planning session.

- First walkthrough (February) was done live in extraction — selection happened conversationally
- No explicit criteria for what makes a package "active" vs "not this month"
- Deadlines, contract status, and position priority all play a role but aren't formalized
- May resolve naturally through practice — the cut might be obvious once you see the landscape

**To resolve:** Whether the walkthrough-to-docket cut needs explicit selection criteria or remains a judgment call informed by the walkthrough data.

### 8. Sales pipeline revival
⏱️ 10 min

The monthly plan's leads section contains contract model targets but no conversion tracking. Conversion actions and next steps need a separate document. The [2025 Sales Pipeline GSheet](https://docs.google.com/spreadsheets/d/1zFrb-2B9KufHqOT0fafJoZbNenLNivgN4eYeE5DRMoQ/edit?usp=sharing) exists but is unused.

- 10 open leads on the board, only 3 on the March plan
- Lead conversion tracking is stateful — doesn't belong on the stateless monthly plan
- The GSheet format may need updating for the current contract model (retainer vs fixed-price)
- Sales pipeline document should link from the Router

**To resolve:** Revived sales pipeline document format and how it feeds into the monthly planning walkthrough.

### 9. Traceline as internal product
⏱️ 15 min

Traceline was deferred from the March walkthrough as an undefined marker. It's unique: internal product, not a client project. The position mapping is System Engineer + Developer, but the relationship to the agency packages is unclear.

- Traceline beta was targeting Feb 23 launch
- Success fees from retainers connect to Traceline (the scalable revenue stream)
- Junior Architect will have its own name and may be a Traceline product
- How internal product work competes with or complements agency maker time is undefined

**To resolve:** Traceline's place on the monthly plan — separate section, its own package type, or background activity that doesn't compete for maker blocks.

### 10. Pre-decisions extraction
⏱️ 10 min

Pre-decisions were removed from the first March plan because the revenue target isn't grounded yet. The design doc defines pre-decisions as binary choices made at month start. Beyond "new leads: yes/no," which recurring deliberations should become pre-decisions?

- Pre-decisions develop empirically from recurring mid-month deliberations
- The first month is the evidence-gathering period
- Potential categories: hiring, conferences, internal project time allocation
- **Partially resolved (pass 4):** Revenue target grounded at €8K/month. "New leads" pre-decision now on the March plan with constraint. Remaining: which additional pre-decision categories emerge from practice.

**To resolve:** Which pre-decision categories to add after the first month's evidence.

### 11. Lead strategy per lead
⏱️ 20 min

The lead triage (extraction pass 4) classified 11 leads by contract target (retainer/fixed-price) but did not extract the conversion strategy per lead — what meeting is next, what's the pitch, what makes each one a good or bad fit for the retainer model.

- 3 hot leads: Stafford (retainer), Manuel Tessun (fixed-price), Jens RPG (via UWI)
- Stafford decided next week; Tessun starts next week
- Jens RPG could fold into UWI consolidation retainer discussion
- 8 remaining leads at various stages from backlog to dead
- Contract Strategy design doc (#847) is the product being sold through the lead pipeline
- The goal isn't just replicating Archibus — it's having one more rock-solid revenue source AND using the process to work out the retainer model

**To resolve:** Per-lead conversion path, timeline, and which leads justify retainer vs. fixed-price pursuit based on the three-filter framework (revenue gap, position fit, business viability).

### 12. Selectivity gate formalization
⏱️ 10 min

Current client selection includes an instinctive filter: "Can they prove to me they actually have a business?" This was articulated in the revenue reasoning conversation but is not formalized as a pipeline stage with criteria.

- Archibus: proven (6-month extension, success fee negotiation underway)
- Estonian client meeting end-of-month: must prove profitability projections
- The retainer model includes success-based components that depend on client viability
- Referrals (e.g., Christoph Neumaier) tend to be higher quality but still need business viability assessment

**To resolve:** Whether the selectivity gate becomes a formal stage (with documented criteria) or remains an instinctive evaluation during the planning walkthrough.

## Meeting Format

- **Type:** Extraction pass (JA session)
- **Expectation:** Pre-read the design doc. Come with updated project status and team availability.
- **Outcome:** Resolved undefined markers → design doc updated

## Related

- **Issue:** [#884 March capacity plan](https://github.com/DaveX2001/deliverable-tracking/issues/884)
- **Design Doc:** [Monthly Planning System Design](monthly-planning-system-design)
- **Contract Strategy:** [#847](https://github.com/DaveX2001/deliverable-tracking/issues/847)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

