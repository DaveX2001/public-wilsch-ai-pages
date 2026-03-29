---
publish: true
---

# Monthly Planning System — Design Doc
[[coo-position]]

Design doc for a repeatable monthly planning methodology that pre-decides company direction so daily decisions become trivial evaluations — not fresh deliberations. Associated with the COO/President position.

---

## Problem Statement

Day-by-day decision-making produces smart instincts, not strategy. Running three positions simultaneously without a planning framework means daily choices are driven by urgency, not by which work advances the company toward its vision of operating as a shareholder outside the business.

The calendar solved this for time — fixed blocks pre-decide when meetings happen, when maker work happens, when to stop. No equivalent exists for the company itself: which projects, which positions, which contracts, which leads — all decided daily by instinct.

**Preconditions:**
- Multiple active client projects (5+) competing for maker time
- Position-building work (PAs, OMs, methodology) happening alongside project delivery
- Retainer/contract model not yet standardized (#847)
- E-Myth organizational system partially built — org chart, position agreements, and operations manuals at various stages of completion
- Calendar time-boxing already operational (ON/IN the business split)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Decisions are pre-made. When new work arrives, the response is a simple yes/no evaluation against the monthly plan — not a fresh deliberation. Ambiguity in daily decision-making is eliminated. |
| **Success** | Monthly planning session produces a project-position package map. The Shutdown Ritual enforces it nightly ("which 3 issues serve the plan?"). New leads, scope changes, and meetings evaluate against the plan like calendar time-boxes evaluate meeting requests: "does it fit?" |
| **Done test** | Can I write a meeting agenda with open design questions about the monthly planning system? If NO → design is complete |

---

## Approach

### Part 1: Three Organizational Axes

The company operates on three axes. Each has its own tracking system. The monthly plan is the bridge.

| System | Axis | Tracks |
|--------|------|--------|
| **Deliverable-tracking** | Project (GitHub labels) | Client work, delivery |
| **CCI board** | Position (position epics) | AI behavior improvements |
| **Monthly plan** | Package (project + position) | How projects drive position improvement |

Deliverable-tracking answers "what are we delivering?" CCI answers "what are we improving?" The monthly plan answers "why this project improves this position" — the bridge between doing work and building the company.

### Part 2: The Package Model

A project and its associated position are a single unit — the package. Working on Archibus automatically means improving the Developer position. Working on UWI means exercising the full SA → Developer → Dev Lead chain. There is no separate decision about "which position to focus on."

Packages change month by month. This month's Archibus-Developer package may become next month's AVO-Developer package. The position stays, the project rotates. This gives a full month of scope — no pressure to rush because the package holds for 30 days.

The monthly plan lists packages, not projects or positions independently. Deadlines from contracts and retainers drive which package gets attention. Position improvement is a byproduct of project delivery, not a standalone activity — but it is the key value being gained. The position methodology is the scalable product that gets sold. Project delivery funds the research; the research produces the product.

**Outcome-based reporting:** Each package produces two reportable outcomes: (1) what was delivered on the project, and (2) what improved on the position. Associating conversations with issues enables performance review of AI operator sessions — tracing position improvement back to specific project delivery.

One package per maker session. Multi-terminal setup within the session:
- System Engineer terminal (improves AI behavior just-in-time)
- JA terminal (designs)
- Developer terminal (implements)
- Session C (blind verification) available immediately
- One backup terminal for ad-hoc work

**Undefined:** Multi-terminal persona distribution model — how terminals coordinate within a single maker session. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

### Part 3: The Monthly Planning Session

**Cadence:** Last 2-3 days of the current month. Enter the new month knowing what's up — same principle as entering maker time at the top of the hour. Session requires 2-3 hours focused (context loading overhead for reading scope documents at 2.5x speed).

**The walkthrough IS the methodology.** The planning session walks through each active project and asks:

1. **Project inventory:** What projects are active? (GitHub labels)
2. **Friction analysis:** What's the friction in each project?
3. **Position mapping:** Which position improvement would make each project faster — and benefit all other projects?
4. **Package formation:** Project + position = package for the month
5. **Precondition check:** Do I have enough projects to observe friction patterns? If not → take on leads.

This was validated live in the first extraction pass — the walkthrough produced the project-position map that became Part 2.

**What the plan does NOT decide:** Which position to focus on separately. The projects are given (contracts, retainers). The position follows from the project. The plan maps the relationship and makes it visible.

**Stable vs unstable:** Most things that feel unstable are actually undecided. The planning session turns undecided → stable through pre-decisions. Examples:
- "Taking on new leads this month?" → Yes/No (pre-decided)
- "Does this meeting serve the plan?" → Yes/No (evaluates against plan)
- "Which issues tomorrow?" → Shutdown Ritual selects from plan's packages

Only external events (client loss, legal disputes, team exits) are truly unstable — handled by buffer capacity, not re-planning.

### Part 4: Enforcement — Shutdown Ritual Connection

The monthly plan is strategy. The Shutdown Ritual is the enforcement mechanism — the operational trigger that makes the plan real.

**Reframe:** The ritual question evolved from "which package do I work on?" (worker framing) to three CEO questions — one per time block. The CEO does not execute packages directly; the CEO enables workers, pushes deadlines, and manages flow. The package model persists — position improvement remains a byproduct of project work — but the relationship to packages shifted from inside (executing) to above (enabling and directing).

**Trigger:** Post-grooming, not standalone evening habit. Grooming provides the latest board state — what's in review, what's blocked, what David is picking up next. The ritual is the closing 10 minutes of grooming, not a separate habit. One behavioral trigger, not two. Night-before timing gives overnight incubation for strategic decisions (Block 2) while capturing current board state for operational triage (Block 1).

**Three time blocks, each with a fixed responsibility:**

| Block | Responsibility | Ritual Question |
|-------|---------------|-----------------|
| **Block 1 — Enable Worker** | Review David's output + VP/Delivery prep | "What's priority to review?" + "What do I prep so grooming has assignables?" |
| **Block 2 — Own Work** | Milestone-driven execution + lead pipeline | "Which 1-2 milestones do I pick by proximity?" + "Any lead needs action?" |
| **Manager Block** | Manager-labeled issues + fire handling | Board scan of manager items, pick top items |

**Block 1 — Enable Worker (morning).** Two sequential modes. First: review column items, priority sequenced by docket and milestone proximity. Single terminal, single focus, 1-2 hours. Second: VP/Delivery prep on 1-2 epics — unblock sub-issues so grooming has assignable work in the worker column. Multi-terminal: one for the epic (VP/Delivery work), one for the connected position. The package model stays intact — unblocking Archibus exercises the Developer position, unblocking Rekers exercises the full pipeline. Position work is not planned separately; it happens when you work on the package.

**Block 2 — Own Work (later).** Milestone-driven. Scan milestones sorted by due date — closest milestone determines focus. Pick 1-2 milestones for CEO action: client contacts, emails, direct execution, whatever moves the project forward. Existing work (milestones) is prioritized over lead work. Secondary: scan issues with the `lead` label — if a lead needs conversion action (contract drafting, follow-up, proposal), it enters Block 2 alongside milestone work. Most days, the lead scan yields nothing.

**Manager Block (2 hours).** Manager-labeled issues and fires. Open the board filtered by `manager` label, scan, pick top items. Fires — urgent items that don't fit milestone structure — live here, not in maker blocks. The manager block is reactive by nature: context-switching is expected, short tasks are normal.

**Forcing function integration:** Milestones replace meeting cadences as the primary forcing function for Block 2. Each milestone has a due date — proximity determines urgency automatically. The docket still narrows the space (only docket packages have milestones), but within that space, milestone dates drive selection, not instinct. See [Stakes Visibility: A Forcing Function for Task Prioritization](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/stakes-visibility-forcing-function) for the underlying framework.

**Accountability loop:**
1. Monthly plan sets the target (docket packages for the month)
2. Grooming surfaces David's state — what's finishing, what's blocked, what's next
3. Ritual (post-grooming, 10 min) sequences tomorrow: Block 1 reviews + VP/Delivery epics, Block 2 milestones + leads
4. Morning execution follows the sequence — no planning, pure execution
5. If no work serves the plan → the plan needs updating, or new sub-issues need creation

### Part 5: Position Progress & the Router

Each position progresses through a document pipeline visible on the [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router):

PA Draft → PA Complete → OM TOC Draft → OM TOC Complete → OM Sections → Handoff

Handoff = 100%. The cycle is complete, though the position continues evolving post-handoff. The Router already tracks Draft / TBD / Complete per document per position.

The monthly plan uses stages, not percentages: "Developer position: OM complete, handoff in progress" is more useful than "75%." Monthly milestones move positions from one stage to the next — through project work, not through abstract documentation.

Currently, the Developer position is the only one with a full OM (v3) — built over a year of active use. Other positions are earlier in the pipeline (PA drafts, TBD). This asymmetry is expected: positions advance through project work, and the Developer position had the most project hours.

### Part 6: Walkthrough Example (February 2026)

The following project-position map was produced during the first walkthrough. It changes every month — this is an example of the methodology's output, not a permanent state.

| Project | Primary Position | Friction |
|---------|-----------------|----------|
| Archibus | Developer (autonomous path) | Orchestration, role-switching |
| IITR | Developer → Dev Lead | Decomposition success case |
| UWI | Full chain (JA→Dev→Dev Lead) | Handoff speed between roles |
| AVO | JA + Developer (autonomous) | Ralph experiment, local dev |
| Rekers | JA + Developer (autonomous) | Same as AVO, waiting on Thomas |
| Traceline | System Engineer + Developer | Beta friction → improvement loop |

**Cross-cutting friction surfaced through the walkthrough:**
- JA → Developer decomposition (methodology gap)
- Developer → Dev Lead review (tooling gap)
- Non-interactive Developer path (untested)

This is what Part 3's walkthrough produces. Each month's output differs.

**Walkthrough session:** The full reasoning that produced this map is in the first extraction pass — `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/87151f23-d09a-4ed4-a4fe-71368c49c52d.jsonl`

**Undefined:** Team capacity — who can fill which positions, current skill levels, availability. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

**Undefined:** Contract model (#847) — retainer structure that pre-decides projects. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

### Part 7: The Monthly Plan Artifact

The walkthrough (Part 3) produces the analysis. The monthly plan artifact records the decisions. It is a stateless decision record — not a progress tracker. Nothing on this page changes within the month. When something new arrives, you point at the plan and evaluate: does it fit?

**Hypothesis:** If monthly pre-decides direction and daily enforces it, daily judgment shifts from WHAT to work on to HOW to advance — one step per day, traceable to the monthly target. Without this system, daily execution stays incremental: work gets done, but capability doesn't compound toward shareholder status. With it, the same daily effort connects to step-change capability growth — building the company IS doing the work, not a separate activity.

The core behavioral problem is within-day jumping — switching between projects, positions, and contexts multiple times per day. Each context switch costs momentum even with AI handling context bridging. The docket enforces: one package per day, both maker blocks (ON and IN) on that package. All terminals in a maker session — System Engineer, JA, Developer — work on the same package. Day-to-day switching is fine — within-day switching is what kills progress. Two focused maker sessions on a single package produces compounding progress that scattered work across three packages cannot.

**Failure test:** End of March, daily decisions were disconnected from the monthly target — same reactive mode as before. The post-mortem catches wrong direction; the system prevents no direction.

**COO first act:** This is the first position built without prior practical experience. Unlike Developer, JA, or SE — where formalization followed existing behavior — the monthly planning system IS the COO's first operational act. The system precedes the experience. Format may change; the hypothesis stays.

**Financial anchor:** 10K/month agency revenue (controllable — retainers + fixed-price). 5K expenses, 5K pure profit. Traceline success fees as scalable extra. 12-month target: 120K agency revenue. The plan must connect daily work not just to vision but to the financial foundation that makes the vision sustainable.

**Undefined:** Revenue target grounding — why 10K/month, how current pipeline maps to that target, what gap the leads need to fill. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

**Location:** The plan lives on the [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router) as a "Monthly Plans" section — one row per month, each linking to a published page. Current month shows Active; past months show Archived. The Router is already the daily bookmark; the plan becomes part of that daily surface.

**Document structure:**

The plan page contains two sections:

**1. The Docket** — a table of active packages for the month:

| Package (Client → Position) | Owner | Why | Board |
|-----------------------------|-------|-----|-------|
| {Client} → {Position} | {Name} | {Friction or strategic reason from walkthrough} | [→ view]({filtered board URL}) |

Each row answers: what client-position combination, who owns it, why it was selected this month (the reasoning from the walkthrough's friction analysis), and a direct link to the filtered GitHub board view for that project's issues.

"Why" is per package — the reasoning that makes this client-position combination active. It does not change mid-month. It is the decision record from the walkthrough.

**2. Pre-Decisions** — binary choices made once at month start, eliminating mid-month deliberation:

- **New leads:** Yes/No (with constraints if Yes)
- Additional pre-decisions develop empirically — start with leads, add categories as recurring deliberations emerge from practice

**The stateless principle:** Deadlines, milestones, and progress are deliberately excluded. Deadlines live in the operator's head and in contracts — adding them to the plan creates maintenance burden that turns the page into stale shelf-ware. The plan narrows the decision space to 3-4 active packages. Within that small space, instinct combined with known deadlines is sufficient for daily prioritization. The calendar doesn't rank which time block is "most important" — it shows them all. The plan works the same way.

**Three-layer evaluation for new work:**

1. **Vision filter** (permanent): Does this advance the AI-native agency operating model?
2. **Package filter** (monthly): Does this fit one of the docket's active packages?
3. **Calendar filter** (daily): Is there a maker block available?

All three must pass. This is the equivalent of the calendar's time-box evaluation — "does it fit?" — applied to work instead of time.

**Not on the docket = not this month.** No separate "parked" section. Packages either appear on the docket or they don't. If they return, they appear on next month's docket.

**3. Leads** — active sales pipeline items with contract model targets:

| Lead | Contract Target |
|------|----------------|
| {Lead name} | Retainer / Fixed-price / TBD |

Leads are not packages — they don't have positions or owners yet. The only plan-level decision for a lead is which contract model to target. Conversion actions and next steps live in the sales pipeline document, not the monthly plan. When a lead converts, it becomes a docket package in the same or next month's plan.

**Undefined:** Sales pipeline document — revive from [2025 Sales Pipeline GSheet](https://docs.google.com/spreadsheets/d/1zFrb-2B9KufHqOT0fafJoZbNenLNivgN4eYeE5DRMoQ/edit?usp=sharing), link from Router. Manages lead conversion tracking separately from the stateless monthly plan. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

**Undefined:** Month-end evaluation methodology — how to assess whether the month's packages achieved their intended friction reduction. Deferred until after the first month's empirical run (March 2026). → [Meeting Agenda](monthly-planning-system-meeting-agenda)

**Undefined:** Walkthrough-to-docket selection framework — the decision mechanism for how the walkthrough's full package landscape gets cut down to the active docket. Currently a judgment call; may need criteria after the first formal walkthrough. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

### Part 8: Revenue Model

The monthly plan decides WHERE to work. The revenue model decides WHETHER to take on more. Two systems, one constraint: the financial framework sets a target, the monthly plan fills it.

**Two buckets:**

| Bucket | Feeds | Revenue source | Control |
|--------|-------|----------------|---------|
| **Agency** | Level 1 + Level 2 | Retainers, fixed-price projects | High — we set the prices |
| **Principal** | Level 3 | Success-based fees | Lower — depends on client success |

**Three levels within the buckets:**

| Level | Purpose | Monthly target | Bucket | Mechanism |
|-------|---------|---------------|--------|-----------|
| **1. Salary** | Survival + team compensation | €4,000 (€2K Marius + €2K David) | Agency | Bare minimum — covers rent, flights, essentials |
| **2. Nest Egg** | Safety net — 12 months of runway at €3K/month | €3,000 → €36K/year | Agency | If everything fails, 12 months to rebuild |
| **3. Principal** | Passive income — €360K in Nexo at 10% APY → €3K/month | Accumulated over time | Principal | Stretch goal — less control, higher reward |

Level 1 + Level 2 = €7,000 allocation + €1,000 company expenses = **€8,000/month agency revenue target.** This is the number the monthly forecast compares against. Everything above €8K is surplus — accelerates the nest egg, distributes as bonuses, or accumulates toward the principal.

**The design principle:** "Build your company the way that you're comfortable building it and fit into your financial framework." The financial framework is a constraint that makes decisions automatic. Hit the target with what you have. Don't chase more revenue for the sake of earning more — either raise the target with proof that current capacity is sustainable, or maintain it. Taking on more without that proof risks losing what's already working.

**Monthly forecast methodology:**

During the monthly planning session, walk each client and ask: "Revenue this month — yes or no? How much?" Binary. No "maybe." If you can't count on it, it goes in the surplus column — pleasant surprise when it arrives, zero impact on decisions if it doesn't.

The forecast feeds the lead pipeline pre-decision:
- Forecast ≥ €8K → "New leads: no" (default response: "Booked this month, re-evaluate next month")
- Forecast < €8K → "New leads: yes" with constraint (e.g., "one retainer for redundancy")
- Minimum 2 clients for redundancy — single-client dependency is unacceptable risk

No artificial client cap. The revenue target IS the cap. If two clients produce €8K, two is enough. If four are needed, four it is.

**12-month clock:** March 1, 2026 = Month 1 of 12. February 2026 was the design phase. The financial framework targets accumulate over this window — €36K nest egg by February 2027 at €3K/month savings.

### Part 9: Lead Pipeline

Leads are inbound — they arrive through the personal network. There is no marketing, no outbound. The pipeline is a filter framework, not a generation engine.

**When the monthly plan says "new leads: yes":** evaluate every inbound lead against three filters, in order:

1. **Revenue filter:** Does the monthly forecast show a gap below €8K? If no gap exists, the default response is: "Booked this month, re-evaluate next month." No engagement, no evaluation, no energy spent.
2. **Position filter:** Does this client advance a position being built? A client that exercises the JA→Developer handoff is more valuable than one that's just billable hours — the position improvement is the scalable product, not the project delivery.
3. **Selectivity gate:** Does this client have a viable business? Can they prove their revenue or profitability targets? The retainer model includes a success-based component — taking on a client without business viability means the success fee never materializes.

All three must pass. This mirrors the three-layer evaluation for new work (Part 7: vision → package → calendar) but applied specifically to leads entering the pipeline.

**Mid-month lead arrivals:** When a lead appears mid-month and the plan says "no new leads," the response is immediate and templated: "This month we're booked. We may re-evaluate next month." No deliberation, no exception-making. The monthly planning session is the only time revenue decisions are made. During the month, execute — don't reconsider.

**Lead-to-package lifecycle:** A lead is not a package. Leads have a contract model target (retainer or fixed-price) but no position or owner yet. When a lead converts to a client, it enters the next month's walkthrough and becomes a docket package — project + position + owner + why.

**Undefined:** Lead strategy per lead — deeper extraction on each active lead's conversion path, timeline, and retainer vs. fixed-price reasoning. Deferred to next extraction pass. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

**Undefined:** Selectivity gate formalization — whether the "prove your business viability" requirement becomes a formal pipeline stage with criteria, or remains an instinctive filter. → [Meeting Agenda](monthly-planning-system-meeting-agenda)

---

## Source

- **Design Docs:**
  - [Operations Manual Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/operations-manual-router)
  - [Developer Position Beta Scope](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope)
  - [Primary Aim - Life Vision](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)
  - [Organization Chart](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
  - [Strategic Objective](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
  - [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router)
  - [System Engineer PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services)
  - [CCI Board Structure Design](~/.claude/hippocampus/project/soloforce/cci-board-structure-design.md)
- **Issues:**
  - [#884 March capacity plan](https://github.com/DaveX2001/deliverable-tracking/issues/884)
  - [#847 Contract Strategy](https://github.com/DaveX2001/deliverable-tracking/issues/847)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/87151f23-d09a-4ed4-a4fe-71368c49c52d.jsonl`
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/af0f8b80-edc4-4aaa-8b99-b107cf4b48a9.jsonl` (extraction pass 2 — plan artifact format, stateless principle, pre-decisions, evaluation stack)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/2f8d924c-21df-4bb1-b1cb-46f2b645b257.jsonl` (extraction pass 3 — hypothesis, March walkthrough, shutdown ritual redesign, revenue model, forcing functions)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/053c839f-9e9b-4426-aa96-eba196931a4f.jsonl` (extraction pass 4 — revenue model, client walkthrough, lead triage)
- **Session:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/cd49cdfd-e1fd-4dce-933e-a28c9300db07.jsonl` (extraction pass 5 — shutdown ritual CEO evolution, block responsibilities, milestone-driven Block 2, post-grooming trigger)
- **Fireflies:** https://app.fireflies.ai/view/xkn-ucts-pps::01KJ1YM415CRH4KJG7QKX2RHJV (revenue reasoning conversation)
- **Data Artifacts:**
  - [Stakes Visibility: Forcing Function](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/stakes-visibility-forcing-function)
  - [2025 Sales Pipeline](https://docs.google.com/spreadsheets/d/1zFrb-2B9KufHqOT0fafJoZbNenLNivgN4eYeE5DRMoQ/edit?usp=sharing)
  - [Contract Strategy Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/contract-strategy-retainer-model-design)
  - [Archibus Retainer Agreement](hippocampus/project/archibus-fm-assistant/) (6-month renewal, Feb–Aug 2026)
  - [Rekers Order S184773](hippocampus/project/rekers/Order%20S184773%20Rekers%20Betonwerk.pdf) (€5,635 workshop, 50/50 split via UWI)
  - [UWI Supportvertrag](hippocampus/project/wilsch-group/supportvertrag-dezember-2025.md) (€2,500/month + SLA)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

