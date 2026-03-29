---
publish: true
---

# Vulture — Design Doc
[[client-stafford]]

Standalone design doc for the Vulture engine — EasyBooks' content production and distribution system. Separated from the platform-level EasyBooks design doc because Vulture's scope, contradictions, and delivery model require independent treatment.

---

## Problem Statement

Stafford declared Vulture his highest priority — an AI system that produces marketing content and distributes it across social media to drive traffic to EasyBooks. The urgency is real: no content is being produced, no channels are being built, and every week without Vulture is a week without systematic user acquisition.

The problem is not whether Vulture should exist. The problem is that Vulture is undefined.

Stafford's technical documents describe a sophisticated growth automation engine — self-evolving funnels, cultural signal detection, automated A/B testing, niche scoring. His spoken words describe something different: "producing me content, marketing content" for "200 channels" through "mass market cultivation." These are not the same system. His own March Technical Plan places Vulture at build position 5 of 6 ("only once governance is stable") and notes that Plug analytics is a blocking dependency ("Without this: Vulture learns incorrectly") — yet he asks for Vulture before Plug exists.

The contradiction pattern repeats across five areas: priority vs build order, Vulture-first vs Plug dependency, scope reversal without new information, definition gap between specs and conversation, and ownership confusion between Marius's deliverable and Stafford's planned hire.

Without resolving these contradictions, any build commitment — by any developer — risks building the wrong thing.

**Preconditions:**
- EasyBooks has an existing app on TestFlight with a basic Plug-to-Shakespeare flow
- Stafford has marketing urgency but no content production infrastructure
- Technical specs were AI-generated from Stafford's broad vision — they describe automation, not his near-term need
- The input/output triple (what content, what format, which platforms) is undefined
- Stafford is interviewing a full-time hire who would also build AI and manage Vulture

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Produce a proposal for Stafford that positions Marius as the orchestrator and designer of EasyBooks' AI systems — not a developer guessing at undefined requirements. |
| **Success** | Stafford gets a clear yes/no decision: a retainer that pays for extraction, design, and developer orchestration. The contradictions in his own documents are the evidence that this role is necessary — not something to resolve for free. |
| **Done test** | "Can Marius send Stafford a proposal today that he feels confident about, win or lose?" → YES after this extraction pass. |

---

## Approach

### Part 1: What Vulture Is

Vulture is an AI content factory that produces social media marketing material at scale to drive people to EasyBooks.

**Pipeline:**
```
NICHES → CONTENT → DISTRIBUTE → MEASURE → ITERATE
```

1. **Niches** — audience segments. "Mothers preserving family stories." "Founders writing origin stories." "People who never had a yearbook." Each niche gets its own content stream.
2. **Content** — AI generates platform-ready material per niche. Hooks, captions, video scripts. Each piece has an emotional trigger and routes to a Plug landing page.
3. **Distribute** — content goes out across platforms. Initially human-posted (Stafford's hire reviews and publishes). Eventually automated with approval gates.
4. **Measure** — which content drives traffic to Plug? Which niches convert?
5. **Iterate** — feed performance data back. Generate more of what works.

**What Vulture is NOT:** The technical specs describe self-evolving funnels, cultural signal detection, automated A/B testing, and niche scoring. These are future automation layers — not the starting point. Stafford's own words: "producing me content, marketing content" and "let's just start getting stuff out there."

**Undefined:** Vulture's input/output triple — what content format (posts? shorts? reels? carousels?), which platforms first, which niches first. → [Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/easybooks-meeting-agenda-stafford)

### Part 2: Why a Design Sprint Is Required

Five contradictions exist between Stafford's verbal priorities, his technical documents, and the agreed V1 scope from previous calls. These are not criticisms — they are evidence that the vision is evolving faster than the specifications. Vulture is simultaneously priority one and build position five. It requires Plug's data to learn, but is requested before Plug exists. Its specs describe a growth automation engine; its owner describes content production. Until these are reconciled, any timeline estimate or build commitment would be premature. [Full contradiction analysis →](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/vulture-contradiction-analysis)

### Part 3: What Marius Offers — The Orchestrator Model

Marius does not position as a developer for Vulture. Two reasons:

1. **Capacity.** Multiple concurrent clients mean full-time development for one project isn't viable.
2. **The requirements don't exist.** Committing developer hours to something with five unresolved contradictions and no input/output triple is a recipe for rework.

**What the retainer pays for:**

| What Marius Does | What Stafford Gets |
|-----------------|-------------------|
| Extract what's in Stafford's head — resolve contradictions, produce design docs | His vision organized into buildable specifications |
| Orchestrate developers through his system (Claude Code OS) | Any developer becomes 2-3x productive — his hire, freelancers, anyone |
| Quality-control every output | Nothing ships that hasn't been reviewed against the design |
| Continuously improve the AI systems | Vulture, Opera, Shakespeare get better every iteration |

**Three-Slice Ownership (from [Contract Strategy #847](https://github.com/DaveX2001/deliverable-tracking/issues/847)):**

| Slice | What | Who |
|-------|------|-----|
| AI Function | Design + architecture of Vulture, Opera, Shakespeare | Marius |
| Operators | Day-to-day content review, publishing, channel management | Stafford's hire |
| Improvement Loop | Continuous iteration based on what converts | Marius |

**Who builds:** David (Marius's developer) + whoever Stafford brings. They run through the same system. The system is the differentiator, not the individual developer.

### Part 4: How the Retainer Works

**Week 1: Design Sprint**
- 2-3 hours with Stafford: define 3 niches, generate sample content, resolve input/output triple
- Produce Vulture design doc v1 with concrete specs
- By Friday: clear scope, timeline estimate, first milestone commitment

**Weeks 2-4: Build + Iterate**
- Developers build through the system based on the design doc
- Weekly milestone demos — Stafford sees progress, adjusts priorities
- Marius reviews everything, improves the AI systems continuously

**Monthly cadence:**
- End of each month: what was delivered, what was learned, what's next
- Stafford decides: continue, adjust scope, or stop

**Undefined:** Retainer amount, developer cost allocation (Marius's cost vs Stafford's cost), milestone cadence specifics. → [Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/easybooks-meeting-agenda-stafford)

---

## Source

**Transcripts:**
- [Feb 18 — Product Vision Deep-Dive](https://app.fireflies.ai/view/01KHRCZ9PFND0HC5Z4R5W1ZH5B) — 50 min, Marius + Stafford
- [Mar 10 — Alignment Call](https://drive.google.com/file/d/12HwKajqp8W_4mwPCLMAc_FjEK9Dbwaea/view) — SRT, V1 scope + Vulture discussion
- [Mar 14 — Priority Reversal](https://app.fireflies.ai/view/01KKPDMDY5XXWV4X97MMWCBGPK) — 23 min, Vulture = priority one
- Mar 16 — WhatsApp: Stafford's priority ordering (after Vulture)

**Stafford's Documents:**
- [EB Business Master Plan](https://docs.google.com/document/d/1l7AhojY2FjvOZh1IMnugbKuLLNBpnbH4owv991hraSQ/edit) — Full vision, funnel system, revenue streams
- [March Technical Lifecycle Plan](https://docs.google.com/document/d/1-56z19-gbehQB5EQuvWXsd5waFVSLBjwJxmkqeoNQ8M/edit) — Developer-ready specs, Vulture §3.1

**Artifacts:**
- [Vulture Source Compilation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/vulture-source-compilation) — All Vulture-relevant content, verbatim
- [EasyBooks Design Doc v0.3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/easybooks-design-doc) — Platform-level design doc

**Cross-references:**
- [#1078 — JA Design Doc Pass 1](https://github.com/DaveX2001/deliverable-tracking/issues/1078)
- [#874 — Epic: Livebook Pre-Sales](https://github.com/DaveX2001/deliverable-tracking/issues/874)
- [#847 — Contract Strategy & Retainer Model](https://github.com/DaveX2001/deliverable-tracking/issues/847)

**Session:** `55a34059-8fd8-484f-b664-b559f5c604c5`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

