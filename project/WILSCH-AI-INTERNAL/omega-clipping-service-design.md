---
publish: true
---

# Omega's AI Video Clipping Service
[[client-omega]]

Design doc for an AI-powered service that extracts "pearls" (high-value teaching moments) from Omega Bone's vocal coaching content and produces post-ready short-form clips for social media.

---

## Problem Statement

Omega Bone is a vocal coach with 25+ years of experience and 7,400+ students trained worldwide. She has accumulated ~300 YouTube class recordings ("Learn to Sing with Omega", COVID era, 2-3h each) containing high-value moments buried in hours of raw footage.

These moments are what Omega calls **"pearls"** — high-value teaching moments where coaching breakthroughs, authenticity discoveries, and quotable wisdom surface naturally during lessons. Pearls follow a narrative arc: relatable starting point → coaching moment → breakthrough (see Part 3: Pearl Taxonomy for the full classification). She cannot efficiently identify and extract them manually: "sorting through them makes my brain and my eyes bleed." Generic clipping tools like Opus Clip fail because they detect topics and engagement signals, not the domain-specific coaching moments that constitute Omega's value. As her business advisor Anthony put it: "there's no topic" for generic tools to latch onto.

Omega needs an AI-powered service that understands her teaching domain well enough to extract pearls from raw content and produce post-ready short-form clips for social media (YouTube Shorts, TikTok, Instagram Reels), feeding her marketing funnel: clips → website → discovery session → paid programme.

This design doc does NOT cover: website development, pricing strategy, album content processing, album production, or the programme structure itself.

**Preconditions:**
- ~300 Learn to Sing class recordings exist on YouTube (Streamyard → YouTube)
- Business funnel is defined (Figma website redesign, 3-tier programme structure)
- Persona document with burden index: see [Omega Persona Doc] (separate artifact)
- Client urgency: career transition — Omega faces potential job loss and needs her singing business to become viable income ("I need to be in business sooner than later")

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Omega has a managed AI service that extracts pearls from her content library and delivers post-ready short-form clips she can publish directly to social media. |
| **Success** | Omega reviews a batch of AI-generated clips from her class recordings, finds them publishable without manual editing, and confirms they match her teaching philosophy — authenticity over technical perfection, "the next natural singer, not another Whitney Houston." |
| **Done test** | Can we write a meeting agenda with open design questions about how the clipping service works? If no → the design is complete and ready for implementation. |

---

## Approach

### Part 1: Data Sources & Input Types

One content type serves as input, hosted on Omega's YouTube channel (@omegabone):

**Learn to Sing Class Recordings (~300 videos)**
COVID-era live-streamed classes via Streamyard, published to YouTube (mirrored on Facebook). Zoom-grid format with ~10-25 students on camera, 2-3 hours each. Individual students perform songs of their choice, Omega provides feedback. ~5 clippable moments per class.

**Excluded from scope:** Omega's "Come With Me" album (10 tracks, produced music videos) is not processed by this service. Album content may be discussed with the client for future inclusion as a top-of-funnel awareness channel. See [Meeting Agenda Topic 6](#6-album-clips-as-future-top-of-funnel).

### Part 2: Target Avatar & Content Strategy

The target avatar is psychographic, not demographic: creative adults who carried a childhood dream of singing but never pursued it. Anthony (business advisor) defined the exclusions: not entrepreneurs, not parents — "there's no mention whatsoever of children, business, Entrepreneur on the YouTube channel." Omega clarified: "It's not feminine. It's the creator."

The full avatar with burden index (52 problems scored by severity and service relevance) is maintained as a separate persona document. See [Omega Persona — Burden Index](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-persona-burden-index).

Class clips serve the entire marketing funnel — a single content type that drives both awareness and conversion:
- **Awareness:** Clips showing Omega's coaching style and teaching philosophy attract the target avatar — people who see themselves in the students and think "I could do that too."
- **Transformation proof (mid-funnel focus):** Clips showing student progression — from tentative first attempts to confident performance — provide the social proof that Omega's method works. This is the primary content priority.
- **Conversion:** All clips CTA to omegabone.com → free 30-min discovery session → paid programme. Conversion path details (programme tiers, student journey) to be clarified. See [Meeting Agenda Topic 5](#5-programme-pricing-and-student-lifetime-value).

### Part 3: Pearl Taxonomy

A "pearl" is a high-value moment worth extracting as a short-form clip (see Problem Statement for the brief definition). Omega's pearls follow a narrative arc: relatable starting point → coaching moment → breakthrough. This matches the testimonial format on her website redesign.

Four pearl categories identified from the discovery call:

1. **Coaching feedback moments** — Omega giving specific, actionable advice (e.g., "lean into your French accent, don't try to obscure it")
2. **Authenticity breakthroughs** — A student singing naturally for the first time, finding their own voice ("I'm looking for the next natural singer")
3. **Transformation arcs** — Visible before→after progression within a class: tentative start → coaching moment → breakthrough performance (e.g., Simon's concert programme — "several transformations from beginning to end")
4. **Quotable wisdom** — One-liners suitable for text overlay or Pinterest images ("You didn't lose the ability to sing. You just never had someone show you how.")

**Undefined:** Pearl signal refinement — categories defined, threshold established (narrative arc: before → coaching → after). Client validates by reviewing POC output at the meeting. See [Meeting Agenda Topic 4](#4-pearl-poc-demo--signal-refinement).

**Proof of concept:** Pearl examples identified from the discovery call transcript, where Omega describes specific moments from her classes:

| Category | Example | Transcript Ref |
|----------|---------|---------------|
| Coaching feedback | "lean into his French accent and not to try to obscure it... little tidbits like that" | 22:55-23:10 |
| Authenticity breakthrough | "I'm not looking for another Whitney Houston. I'm looking for the next natural singer" + "I help them craft the song so that it fits their own story" | 15:26-15:48 |
| Transformation arc | Simon's concert programme — "several transformations from beginning to end" + "By April, I should have full story arcs" | 11:02, 16:37 |
| Quotable wisdom | "the meat and potatoes. No salad, no garnish." | 01:54-02:00 |

These are Omega *describing* pearls, not pearls extracted from footage. Full validation requires transcription of actual class recordings (YouTube videos lack auto-captions).

**POC validation (2026-03-13):** Three class recordings transcribed and analyzed — Carlos (34 min), Tina (42 min), Ira (81 min). Six pearls extracted across all four categories. The narrative arc (before → coaching → after) reliably identifies pearl moments in real content. Pearl density: ~2 strong candidates per 30-40 min class, consistent with the ~5 clippable moments per class estimate. Full results: [Pearl POC Results](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-pearl-poc-results).

### Part 4: Processing Pipeline

Claude Code SDK orchestrates the pipeline. The agent is "the handyman" — it coordinates specialized tools for each stage:

| Stage | Tool | Function |
|-------|------|----------|
| **Download** | `yt-dlp` | Extract video from YouTube channel |
| **Audio extraction** | `ffmpeg` | Separate audio track from video |
| **Transcription** | `faster-whisper` (or FFmpeg 8.0 native Whisper) | Generate timestamped transcript, 4x faster than standard Whisper. POC validated with AssemblyAI CLI as cloud alternative (handles upload/polling automatically) |
| **Pearl detection** | Claude API (via SDK) | Analyze transcript with domain context (pearl taxonomy, teaching philosophy) → identify candidate timestamps |
| **Clipping** | `ffmpeg` | Trim video at identified timestamps |
| **Captioning** | `ffmpeg` subtitle burn-in | Auto-generate captions from transcript segments |
| **Composition** | [Remotion](https://www.remotion.dev/docs/ai/mcp) (optional) | Programmatic overlays, branding, platform formatting via MCP integration |
| **Staging** | Google Drive API / YouTube API | Upload to review area |

**Tool selection rationale:**
- **FFmpeg 8.0 + Whisper** is the core — a single tool chain for transcription AND clipping. Native Whisper support eliminates separate transcription services.
- **Remotion MCP** is for CREATING polished video compositions (text overlays, branding, transitions), not for the clipping itself. Useful for the final polish stage when clips need platform-specific formatting.
- **OpusClip** was explicitly rejected by client — "too generic, no topic" (Anthony). Domain-specific pearl detection requires custom AI, not engagement-signal tools.
- [HuggingFace Video Generation Leaderboard](https://huggingface.co/spaces/ArtificialAnalysis/Video-Generation-Arena-Leaderboard) — for video generation, not relevant to clipping existing content. Tracked for future reference if generated content is needed.

The approach is iterative: ship a first batch fast, Omega reviews ("you're gonna hate it, you're gonna love it"), feedback improves the model's understanding of what constitutes a pearl. Not a traditional software project — a primed AI agent that improves through use.

### Part 5: Output Specification

Deliverable = post-ready short-form clips that Omega can publish directly.

**Primary platforms (Phase 1):**
- YouTube Shorts (~60 seconds, vertical)
- TikTok (shorter than 60s, vertical)
- Instagram Reels (vertical, up to 90s)

**Clip format:** Video segment + auto-generated captions/subtitles + platform-appropriate formatting (aspect ratio, duration limits).

**Undefined:** Platform priority for Pinterest (image + text, not video) and LinkedIn (Omega's highest revenue platform). See [Meeting Agenda Topic 3](#3-platform-priority-beyond-video-clips).

### Part 6: Delivery Model

Managed service — Wilsch AI Services operates the clipping service, Omega is the client.

**Phase 1: Batch Processing**
Process the Learn to Sing class library (~300 videos). AI extracts all potential clips. Wilsch AI operates the pipeline, curates output, and presents clip batches to Omega for approval before publishing. No volume cap on extraction.

**Phase 2: Ongoing Processing**
Recurring content: Sunday group classes + 10 hours/week private lessons. Expected cadence: ~5 clips per class, ~1 clip per private lesson. Year-long engagement timeline.

**Operator Transition**

The service transitions from managed to semi-automated as pearl detection quality improves:

- **Managed phase** (months 1-3): Wilsch AI operates the pipeline, reviews raw AI output, curates clips before presenting to Omega. Higher operator involvement, higher service value.
- **Auto + curate phase** (month 4+): Pipeline runs automatically on new content, stages clips directly. Omega curates from a staging area — approves or skips. Operator involvement decreases, retainer adjusts downward.

The transition point depends on pearl detection accuracy. At 80%+ accuracy, Omega can curate directly without pre-filtering. Below that, managed operation prevents the exact pain she hired the service to eliminate ("sorting through them makes my brain and my eyes bleed").

**Engagement Model**

Flat monthly retainer from the start — the ongoing nature of content production (weekly classes, continuous lessons) makes a one-time fixed-price project structurally wrong. The 3-month initial term serves as the prove-first checkpoint: real engagement data from the managed phase informs whether to add a success component tied to commercial outcomes (student acquisition, programme enrollment driven by clips).

Retainer entry is priced lower than a comparable fixed-price engagement. The trade-off: lower monthly commitment in exchange for Wilsch AI participating in the upside after the prove-first period. If the service demonstrably drives new students, the success component benefits both sides. If it doesn't, the retainer ends cleanly at the 3-month checkpoint.

The proposal format is a Richtpreisangebot (indicative pricing) — a one-pager covering outcome and indicative price range, sent after programme pricing is established. The proposal leads with what Omega gets (post-ready clips, managed curation, improving quality), not how the system works internally.

**Undefined:** The retainer price depends on programme pricing — what a new student is worth over the programme lifecycle. Without this, the service cannot be priced as a fraction of the value it creates. See [Meeting Agenda Topic 5](#5-programme-pricing-and-student-lifetime-value).

Pricing mechanics (actual numbers, tiers, success fee percentages) are not in scope for this design doc — covered in the client proposal after the meeting.

---

## Source

- **Transcripts:**
  - [Discovery Call 2026-03-08 (52 min)](https://drive.google.com/file/d/1xnIw-xMsN-KZXO72BA4z6DyYAq37wtBd/view) — Omega + Anthony + Marius
  - [Grooming 2026-03-08](https://drive.google.com/file/d/1fwumO3pBf5tRiCy08BU_qoPtxnzaj-m4/view)
  - [Fireflies Transcript](https://app.fireflies.ai/view/01KK6JXB75WTRS613Y4S56T5Z8)
- **Commercial Doc:**
  - [Commercial Design Doc — "Omega's Teaching Moments, Found by AI"](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-commercial-design-doc) — client-facing version (Pass 5)
- **Artifacts:**
  - [Figma: Vocal Coach Website Redesign](https://github.com/user-attachments/files/25824674/screencapture-figma-make-I5V9i7drJ3FCziHPcPHmDh-Vocal-Coach-Website-Redesign-2026-03-08-22_50_40.pdf) — 3-tier programme, avatar messaging, testimonial format
  - [YouTube: Come With Me Album (playlist)](https://www.youtube.com/playlist?list=PLFWa3WnH-5diGa82MJNQB0sgGKMzO89pz) — 10 tracks, album content
  - [YouTube: Learn 2 Sing with Audrey (live)](https://youtube.com/live/Odr_khKGmuc) — Recent class format example
  - [YouTube: Ep. 2 Learn to Sing (2021)](https://youtu.be/XxCaFDsWPfs) — Legacy class format example
  - [Epic #1071](https://github.com/DaveX2001/deliverable-tracking/issues/1071) | [Design Issue #1076](https://github.com/DaveX2001/deliverable-tracking/issues/1076)
  - [Contract Strategy & Retainer Model Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/contract-strategy-retainer-model-design) — engagement model framework (#847)
  - [Evidence Chain System #851](https://github.com/DaveX2001/deliverable-tracking/issues/851) — prerequisite: surface-agnostic evidence capture for SDK agent failures (enables quality feedback loop on pearl detection)
  - [Manage-Artifact v2 CCI-629](https://github.com/DaveX2001/claude-code-improvements/issues/629) — prerequisite: instruction-fixing methodology for the SDK agent that runs the clipping pipeline
- **Sessions:**
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/bc288ebc-dddf-4d6b-aeb6-39b09aac97f0.jsonl — Pass 1 (technical design)
  - /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/d8f852f9-f840-4054-8510-e80642a42466.jsonl — Pass 2 (commercial model)
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-09-SALES-PIPELINE--deliverable/ed570609-3341-4b1e-9ce0-fb6ace67a3b3.jsonl — Pass 3 (SA feedback processing)
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-09-SALES-PIPELINE--deliverable/5f02c86d-50d2-4bce-b021-4c592622dca8.jsonl — Pass 4 (Pearl POC + taxonomy update)
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-09-SALES-PIPELINE--deliverable/057bbeb9-4f11-4d2d-bdad-89b70cbea0b0.jsonl — Pass 5 (Commercial design doc + fresh transcriptions)
