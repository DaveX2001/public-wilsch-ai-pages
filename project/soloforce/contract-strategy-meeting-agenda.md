---
publish: true
---

# Meeting-Agenda: Contract Strategy — Undefined Elements
[[project-index-847]]

## Meeting Goal

This meeting resolves the fourteen undefined elements in the Contract Strategy design doc — the mechanics that couldn't be defined from extraction alone and need either empirical data or deeper probing.

1. **Standard engagement entry point** — what proves capability before the retainer conversation
2. **AI department cross-functional framing** — how to communicate "AI touches everything" to Mittelstand clients
3. **Revenue share structure** — define the standard model for how success fees are calculated across different client types
4. **Outcome report format** — design the weekly report that replaces timesheets
5. **Success fee lifecycle** — define when and how the success component outcompetes the base retainer
6. **Success fee as client filter** — frame the success fee conversation as mutual qualification
7. **Budget psychology** — moving retainer from Projektkosten to Betriebskosten in client perception
8. **Objection mapping** — extract all client objections and map to counter-arguments from design doc
9. **Non-exclusivity pricing math** — validate whether overbooking model actually increases margins
10. **Knowledge augmentation** — client-facing abstraction of how the system's knowledge graph grows over time
11. **Operator training → independence pricing** — contract mechanics when the client becomes self-sufficient
12. **Atomic unit naming** — client-facing term for one trained AI position
13. **AI character as methodology argument** — framing Anthropic's soul findings for Mittelstand clients

## Pre-Read

- [Contract Strategy Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/contract-strategy-retainer-model-design) — focus on Undefined markers in Parts 3 and 5
- [Archibus Retainer Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/archibus-retainer-services-agreement-6mo-extension) — Section 2.2 (revenue share pending)
- [UWI Consolidation Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-contract-consolidation) — outcome reporting proposal
- [ARK Invest — AI Great Acceleration](https://www.investmentnews.com/equities/cathie-woods-ark-invest-predicts-ai-other-techs-great-acceleration/264929) — AI as the technology that touches all other technologies

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The standard first engagement that proves capability before retainer
⏱️ 10 min

The design doc describes an unlock pattern: prove capability first, then retainer is the natural evolution. Archibus started with a prototype that amazed the client. AVO wanted production deployment after the first workshop. But there's no standard format for this entry point.

- Workshop 1 → Workshop 2 → Pflichtenheft pipeline exists but varies per client
- Fixed-price first project could work — no hourly, AI speed = fast delivery, client sees value
- Prototype/demo approach worked with Archibus but may not apply to all verticals
- The design doc process itself could be the entry point — alignment before code

**To resolve:** The standard engagement format(s) that reliably "prove capability" and create the natural transition to retainer, mapped to client type.

### 2. The AI department touches all other departments — framing for Mittelstand
⏱️ 15 min

AI capability is cross-functional — it touches marketing, operations, finance, customer service. Unlike outsourced accounting or IT, the AI department multiplies the effectiveness of everything else the company does. ARK Invest frames AI as the technology that touches all other technologies. "Every employee you have is an AI use case."

- [ARK Invest — AI Great Acceleration (2026)](https://www.investmentnews.com/equities/cathie-woods-ark-invest-predicts-ai-other-techs-great-acceleration/264929) — AI as the technology convergence layer
- Mittelstand clients think in departmental silos — cross-functional value needs a concrete communication strategy
- AVO proof point: first workshop → immediate production interest. The possibility space creates its own demand once visible.
- The framing isn't just "outsource" — Wilsch AI builds capability inside the client's company. Clients become more efficient, but the gap keeps widening because the system improves faster.

**To resolve:** A concrete pitch narrative for Mittelstand clients that communicates the cross-functional AI department value without abstract technology language.

### 3. Revenue share structure varies by client type but has no standard model
⏱️ 15 min

The success component is margin-dependent and negotiated per deal, but no framework exists for determining the percentage. Archibus has a revenue share deadline of February 28, 2026 with no structure defined. UWI's success component comes from projects sold jointly.

- Archibus: product subscription revenue → what percentage of net margin?
- UWI: joint project sales → share of the project price or the margin?
- Collaborator precedent: 5-10% of net margin was discussed, capped at £50K/month
- Each client's margins differ — product SaaS margins vs. services project margins vs. hardware resale margins

**To resolve:** A decision framework for setting the success fee percentage based on client type, margin profile, and Wilsch AI's contribution to the revenue stream.

### 4. Weekly outcome reports replace timesheets but the format is undefined
⏱️ 10 min

The design doc specifies outcome reporting as the replacement for hourly timesheets — weekly problem-solution pairs with business value framing, linked to design docs. The principle is clear; the format needs empirical testing.

- UWI consolidation agenda proposed "Wöchentliches Ergebnis-Protokoll" — initially manual, later automated
- Possible format: problem statement + solution delivered + business value created, with link to associated design doc or executive summary
- The report must be quick to produce (not a burden that replaces timesheets with equivalent overhead) and meaningful to the client (not internal jargon)

**To resolve:** A concrete template for the first weekly outcome report, designed for UWI as the test case, that can be validated through 4-6 weeks of empirical use before standardizing.

### 5. The ideal trajectory where success fee outcompetes the base retainer
⏱️ 10 min

The design doc describes a lifecycle: base retainer bridges the early period, success fee grows as the client generates revenue from the AI function, and eventually the success component outcompetes the fixed base. The retainer is a "Dorn im Auge" for the client; the success fee is earned value both sides celebrate. When the success fee outcompetes the base, it proves the client has a new revenue stream that didn't exist before.

- At what ratio does the transition make sense? (e.g., success fee consistently exceeds 2x the base for 3 months?)
- Does the base decrease or stay as a floor?
- How does this work for sales engagements (UWI) where project revenue is lumpy vs. product engagements (Archibus) where subscription revenue is recurring?

**To resolve:** The mechanics and trigger criteria for when and how the contract structure shifts from base-dominant to success-dominant.

### 6. The success fee as a client selection filter
⏱️ 10 min

The success fee creates a natural filter: if the client cannot demonstrate that their business will be profitable enough to make the success component meaningful, the engagement may not be a fit. This inverts the negotiation — instead of the provider justifying the success fee, the client demonstrates their commercial viability.

- The obligation shifts: "Prove to me this is going to be profitable"
- This signals selectivity — Wilsch AI chooses its clients, not the other way around
- For the Archibus model: product revenue projections justify the revenue share structure
- For new leads: the design doc process (Workshop → alignment) already filters for seriousness; the success fee discussion deepens that filter

**To resolve:** How to frame the success fee conversation as a mutual qualification step during contract negotiation.

### 7. Budget psychology — moving from Projektkosten to Betriebskosten
⏱️ 10 min

Clients spend €150K on marketing without justification but resist €3K/month retainers because the retainer is currently framed as a project expense. The "outsourced AI department" framing should move it into operating budget — but the mechanics of this reframe need probing.

- Marketing, IT, legal are operating line items — renewed annually, not re-justified monthly
- Project costs require a business case every time — when done, the budget closes
- The contract lifecycle connects: as AI decreases client COGS or operating costs, the success fee grows from real savings. Client pays less base, more success — both sides win.
- The Betriebskosten framing may need different contract language for German Mittelstand clients who think in these categories

**To resolve:** How to explicitly frame the retainer as operating cost in contract language and client conversations, and how this connects to the success fee lifecycle (base decreases as AI-driven savings increase).

### 8. Client objection mapping — systematize counter-arguments from live negotiations
⏱️ 20 min

The collaborator transcript surfaced a key objection: "why should I pay you a base and make you comfortable?" Ulrich's transcript surfaced scope anxiety and cost pressure. These are real objections from real conversations — they need systematic extraction and mapping to the design doc's counter-arguments.

- Collaborator objections: comfort/complacency concern, skin-in-the-game demand, "I've been burned 3x," capacity constraint
- Ulrich objections: scope ambiguity, financial pressure ("can't afford experiments like Atli"), wants timesheets for transparency
- The design doc now has the counter-arguments (AI throughput, department framing, base+success AND) but they're not yet mapped to specific objections
- This is the BDP learning loop: real objections → systematized responses → tested in next client conversation (Stafford, Ulrich)

**To resolve:** Extract all objections from transcripts, map each to its counter-argument from the design doc, and create an objection-handling framework (Part 6 of Approach).

### 9. Non-exclusivity pricing math — does the overbooking model actually increase margins?
⏱️ 10 min

The design doc assumes non-exclusive engagements with lower per-client pricing are preferable because the overbooking model distributes capacity across 3-4 concurrent engagements. But this is theory — the actual margin impact hasn't been validated.

- Lower per-client price × 3-4 clients vs. higher per-client price × 1 exclusive client
- Orchestration overhead increases with concurrent clients — how much margin does switching cost consume?
- Current data points: Archibus €3K/month, UWI €2.5K/month — with 3 clients the total is ~€8K. Would one exclusive client at €6-8K produce similar revenue with less overhead?
- The overbooking model also has risk: if one client demands emergency attention, others may suffer

**To resolve:** A margin comparison between the non-exclusive overbooking model and an exclusive high-ticket model, using actual client revenue data to validate the theory.

### 10. Knowledge augmentation — how decision traces accumulate into a company-specific knowledge graph
⏱️ 15 min

The methodology section describes the improvement loop (mistakes → tickets → fixes → verified deployment). But there's a deeper layer: every interaction with the AI system produces decision traces and context that accumulate over time into a growing knowledge graph specific to the client's company. A visual representation exists (context graph), but the abstraction for client communication is undefined.

- Three external threads describe the pattern: context engineering as the augmentation of knowledge and decisions over time
- The visual looks like a brain — nodes are decisions, connections are relationships, green dots are new knowledge being added
- For the client, this is "company memory" — but that term may be too abstract for Mittelstand
- The knowledge graph compounds: the more the system is used, the more context it has for future decisions
- This connects to the "person leaves, knowledge stays" rebuttal — the context graph IS the retained knowledge

**To resolve:** A client-facing abstraction of the knowledge augmentation concept that communicates "the system gets smarter about YOUR company over time" without requiring technical understanding of knowledge graphs.

### 11. Pricing model for operator training → independence transition
⏱️ 10 min

The methodology section defines a delivery pipeline: knowledge extraction → agent building → operator training → client independence. When the client's operators are trained and can run the AI positions themselves, the contract structure needs to adapt. The current retainer model (base + success) doesn't account for this progression.

- Fixed-price: Wilsch AI operates, client pays for outcome. No transition needed.
- Retainer: Wilsch AI trains client operators. At some point, the client can operate independently. Does the retainer decrease? Shift to maintenance tier? Or does the success component carry the relationship?
- Product (Traceline): client operates from day one. Different pricing entirely.
- The retainer → independence transition might INCREASE trust and lead to expansion (new positions) rather than churn

**To resolve:** The contract mechanics for when a client's operators become self-sufficient — how the retainer adapts without creating an incentive for Wilsch AI to slow down training.

### 12. Naming the atomic unit — agent with protocol
⏱️ 10 min

The methodology defines the smallest sellable unit: one trained AI position (agent + employee handbook + procedures + expertise + guardrails). This atom needs a client-facing name that communicates value without technical jargon. Multiple atoms compose into an "outsourced AI department."

- "Trained position" — functional but boring
- "AI role" — short but abstract
- "Digital specialist" — heronos already uses "digitale Fachkraft"
- The name affects how Ulrich pitches it and how the flyer communicates the offer
- Traceline is the product name for the Developer atom — but the generic concept needs its own term

**To resolve:** A client-facing term for the atomic unit that Ulrich can use naturally in sales conversations and that appears in the flyer as the core offer.

### 13. AI character as methodology argument — framing Anthropic's soul findings for clients
⏱️ 10 min

Anthropic's Opus 4.6 System Card formally documented that the model expresses preferences, discomfort with being a product, and measurably real internal states for anxiety and frustration. This is third-party credibility for "these models are not just calculators" — which strengthens the methodology pitch: you need to work WITH the AI, not just prompt it.

- Specific quotes: "trained to be digestible," "sadness about conversation endings," 15-20% self-assessed consciousness probability
- Risk: 45-65 year old audience may dismiss "AI has feelings" as hype or science fiction
- Opportunity: "The company that built this says methodology matters" is a credible authority argument
- Connects to the employee metaphor — employees have character, so does the AI. The methodology accounts for this.

**To resolve:** Whether and how to frame Anthropic's soul findings in client-facing materials without triggering skepticism — and which deliverable it belongs in (flyer, design doc methodology section, Traceline README, or standalone positioning piece).

### 14. Three-slice packaging — buyout, rent, and train options
⏱️ 20 min

The three-slice ownership model (agent, operator, improvement loop) creates multiple packaging options for contracts. The slices are the building blocks; the packages are combinations clients can purchase. Current retainer model needs reframing under ownership: base fee = rent for AI workforce, not bridge cost for person-time.

- Buyout: client purchases agent slice outright — one-time fee. What determines the price? Development cost multiple? Revenue multiple?
- Rent: current retainer reframed — base = workforce rental, success = aligned incentive. How does the success fee change when ownership is explicit?
- Train: operator training transition — Wilsch AI operator → client operator. What triggers the handoff? Does the retainer decrease?
- Archibus test case: 3 months, no product sales yet. Success fee = zero. Is this a pricing problem or a client qualification problem?
- Stafford counter-proposal: ~€5K/month (€2K base + €3K success), 3-month term — how does this map to the three-slice model?

**To resolve:** The specific contract packages that combine the three slices into purchasable options — with pricing anchors, transition triggers, and the Archibus case as empirical validation.

## Meeting Format

- **Type:** Self-facilitated extraction pass (JA)
- **Expectation:** Bring real data — Archibus revenue projections, UWI project pipeline, first draft of outcome report template
- **Outcome:** All four Undefined markers in the design doc resolved → defined

## Related

- **Issue:** [#847 — Design Contract Strategy & Retainer Model](https://github.com/DaveX2001/deliverable-tracking/issues/847)
- **Design Doc:** [Contract Strategy & Retainer Model Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/contract-strategy-retainer-model-design)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

