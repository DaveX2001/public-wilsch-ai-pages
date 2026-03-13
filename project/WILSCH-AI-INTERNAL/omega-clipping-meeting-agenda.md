---
publish: true
---

# Omega AI Video Clipping Service — Meeting Agenda
[[omega-clipping-meeting-agenda]]

Meeting agenda for resolving open design questions from the [design doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-clipping-service-design).

## Meeting Goal

Demonstrate proof of concept, resolve open design questions, and align on next steps before sending the proposal.

1. **Pearl POC demo** — Show real examples extracted from Omega's videos, then refine detection criteria together
2. **Student consent** — Clarify consent framework for featuring individual students in marketing clips
3. **Class recording structure** — Understand class flow so AI can segment recordings
4. **Platform priority** — Decide scope for Pinterest (images) and LinkedIn
5. **Programme pricing** — Understand programme tiers and student journey so the service proposal matches the business it supports
6. **Next steps** — Indicative pricing proposal within 24h

## Pre-Read

- [Omega AI Video Clipping Service — Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-clipping-service-design) — specifically the **Undefined** markers in Part 3 (Pearl Taxonomy) and Part 5 (Output Specification)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Student consent for featured clips
⏱️ 5 min

The Learn to Sing classes were public live streams on YouTube — students joined knowing they were recorded. Using individual students as featured subjects in marketing clips (especially transformation arcs tracking one person across sessions) is a different use case than the original class recording.

- Classes had 10-25 students on camera in Zoom grid format
- Transformation arcs require identifying and featuring specific individuals
- Three students (Simon, Anthony, Ira) are in an active concert program producing story arcs by April

**To resolve:** The consent framework for featuring individual students in marketing clips derived from class recordings.

### 2. Class recording structure
⏱️ 5 min

The AI needs to segment 2-3 hour class recordings into clippable units. The working assumption is: student performs → Omega gives feedback → next student, with ~5 performances per class. Additional structural elements (warm-up, group exercises, introductions) would change how the AI identifies segment boundaries.

- Current classes (e.g., "Learn 2 Sing with Audrey") run ~42 minutes
- Legacy classes (COVID era) ran 2-2.5 hours with ~25 students
- Students choose their own songs

**To resolve:** The typical flow of a class recording from start to finish, including any recurring segments beyond individual performances.

### 3. Platform priority beyond video clips
⏱️ 5 min

Phase 1 covers video clips for YouTube Shorts, TikTok, and Instagram Reels. Two additional platforms were discussed but not prioritized: Pinterest requires image + text posts (not video), and LinkedIn is where Omega reported making the most money — yet it wasn't discussed as a clip destination.

- Pinterest: "just pictures. Pictures with the words" (Omega)
- LinkedIn: highest revenue platform, corporate gig source
- Each additional platform adds a distinct output pipeline

**To resolve:** Which platforms beyond YT/TT/IG should be included in the service scope, and in what priority order.

### 4. Pearl POC demo + signal refinement
⏱️ 10 min

We extracted real pearls from three of Omega's Learn to Sing class recordings using AI-powered content detection. This topic opens with showing those examples, then refining the detection criteria together.

- **Pearl examples:** [POC Results — 6 pearls from 3 videos](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-pearl-poc-results)
- Four pearl categories: coaching feedback, authenticity breakthrough, transformation arc, quotable wisdom
- Each example includes the timestamp, transcript excerpt, and why it qualifies as a pearl
- Omega and Anthony review: "Is this what you'd want to post? What's missing?"

**To resolve:** Validate that the AI finds the right moments. Refine what to detect more of, less of, or differently.

### 5. Programme pricing and student journey
⏱️ 5 min

The clips feed a funnel: social media → omegabone.com → discovery session → paid programme. Understanding the programme structure and what a student pays at each tier helps scope the service to match the business it supports — if the clips bring in even one additional student per month, the service should pay for itself.

- Three programme tiers discussed (30-day / 90-day / 3.5-month album)
- Discovery session is the entry point (free, 30 min)
- Current student base: ~10 regular students across remote and in-person

**To resolve:** The pricing of each programme tier and how students typically progress through them, so the service proposal can be sized appropriately.

### 6. Next steps
⏱️ 5 min

Wrap up with clear expectations for what happens after the meeting.

- Indicative pricing proposal (Richtpreisangebot) sent within 24 hours
- Proposal anchored on programme pricing discussed in Topic 5
- Timeline for Phase 1 kickoff if both sides agree

**To resolve:** Confirm Omega is comfortable with a 24h turnaround on the proposal and align on any remaining questions.

## Meeting Format

- **Type:** Discovery / alignment + proof of concept demo
- **Duration:** ~40 min
- **Expectation:** Omega reviews the commercial design doc beforehand, brings any questions
- **Outcome:** All six discussion topics resolved → indicative pricing proposal sent within 24h

## Related

- **Issue:** [Epic #1071](https://github.com/DaveX2001/deliverable-tracking/issues/1071) | [Design #1076](https://github.com/DaveX2001/deliverable-tracking/issues/1076)
- **Design Doc:** [omega-clipping-service-design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-clipping-service-design)
