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
- Business funnel is defined (Figma website redesign, product matrix: $1k–$10.5k LTV)
- Persona document exists ([Burden Index, 52 problems](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-persona-burden-index))
- 2026 1-on-1 lessons confirmed as primary content source (recorded at NS)
- Pearl detection POC validated (6 pearls from 3 videos, narrative arc signal)
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

Three content types serve the service, each with a distinct role:

**Primary (Pearl Source) — 2026 1-on-1 Lessons**
Current individual sessions recorded at Network School (NS). One student per session, ~42 minutes. Higher video and audio quality than legacy recordings. Structure: Omega's intro song → standard welcome → student introduction (background + why they're here) → song selection → warm-up → performance → coaching feedback. The student introduction segment naturally surfaces problem identification pearls (stage fright, vocal struggles, dream outcomes). The perform→coach→breakthrough arc is the same as group classes but with deeper, longer interactions per student.

**Secondary (Pearl Source, Deprioritized) — Learn to Sing Group Classes (~300 videos)**
COVID-era live-streamed classes via Streamyard, published to YouTube (mirrored on Facebook). Zoom-grid format with ~10-25 students on camera, 2-3 hours each. Deprioritized due to low video quality — participants joined from Southeast Asia, Central and South Africa with limited internet bandwidth (2020 era). ~5 clippable moments per class, but clip quality constrained by source quality.

**Branding Element — "Come With Me" Album (10 tracks)**
Produced music videos featuring nature footage with lyric overlays and Omega's original songs. Album release: March 29th, $150/download, vinyl + merch kit at 1,000 sales. The album is NOT a pearl extraction source — it provides outro clips for short-form videos (every clip ends with an album song snippet for brand recognition) and sits in Omega's product stack as a standalone monetization stream.

### Part 2: Target Avatar & Content Strategy

The target avatar is psychographic, not demographic: creative adults who carried a childhood dream of singing but never pursued it. Anthony (business advisor) defined the exclusions: not entrepreneurs, not parents — "there's no mention whatsoever of children, business, Entrepreneur on the YouTube channel." Omega clarified: "It's not feminine. It's the creator."

The full avatar with burden index (52 problems scored by severity and service relevance) is maintained as a separate persona document. See [Omega Persona — Burden Index](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-persona-burden-index).

Class clips serve the entire marketing funnel — a single content type that drives both awareness and conversion:
- **Awareness:** Clips showing Omega's coaching style and teaching philosophy attract the target avatar — people who see themselves in the students and think "I could do that too."
- **Transformation proof (mid-funnel focus):** Clips showing student progression — from tentative first attempts to confident performance — provide the social proof that Omega's method works. This is the primary content priority.
- **Conversion:** All clips CTA to "book a free vocal analysis call" (Omega's "diagnostics") via scheduling link or QR code — NOT to omegabone.com (website is being rebuilt). Sales call → product matrix.

**Product Matrix (all three personas funnel into one package):**

| Tier | Price | Content |
|------|-------|---------|
| Downsell | $1,000/mo | 1 song, vocal training |
| Main | $5,000 | 12-week concert programme — 6 songs, culminates in live concert |
| + Recording | +$2,500 | Concert recording / album |
| + Songwriting | +$3,000 | Original songwriting training |
| **Max LTV** | **$10,500** | |

### Part 3: Pearl Taxonomy

A "pearl" is a high-value moment worth extracting as a short-form clip (see Problem Statement for the brief definition). Omega's pearls follow a narrative arc: relatable starting point → coaching moment → breakthrough. This matches the testimonial format on her website redesign.

Five pearl categories confirmed across two meetings (discovery call + in-person meeting 2026-03-18):

1. **Coaching feedback moments** — Omega giving specific, actionable advice (e.g., "lean into your French accent, don't try to obscure it")
2. **Authenticity breakthroughs** — A student singing naturally for the first time, finding their own voice ("I'm looking for the next natural singer")
3. **Transformation arcs** — The before→coaching→after journey showing a student's progression (e.g., Ira's recovery story woven into "Space Oddity" performance)
4. **Quotable wisdom** — One-liners suitable for text overlay or Pinterest images ("A baby can hoop and holler all day and not get hoarse. Because they're screaming on their breath.")
5. **Problem identification** — Students articulating their vocal struggles, failed attempts, and dream outcomes. Follows a sales framework: problem → what they've tried → struggles → aspirational goal. Naturally surfaces during the student introduction segment of 1-on-1 lessons (e.g., "I wanted to get over my stage fright and be able to sing in front of other people").

**Detection signal:** The narrative arc (relatable starting point → coaching moment → breakthrough) reliably identifies pearls across all 5 categories. Validated by POC: 6 pearls extracted from 3 class recordings, ~2 strong candidates per 30-40 min session. Problem identification (category 5) validated against 1-on-1 lesson format — the student introduction segment contains natural problem/dream outcome framing.

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

**Clip format (confirmed 2026-03-18):**
1. **No intro** — start immediately with hook
2. **Hook** (first 2 seconds) — pearl moment OR student stating their problem
3. **Content** — pearl / teaching moment
4. **Outro** — album song clip (brand recognition, fills to ~60s if pearl is shorter)
5. **Branding** — purple / lavender frame throughout all videos
6. **Duration** — ~60 seconds target
7. **CTA** — "Book a free vocal analysis call" via description link + QR code overlay linking to Omega's scheduling calendar
8. **No background music** — teaching content stays clean. Album outro is foreground music (brand recognition).

**Consent:** All source videos are public YouTube recordings — no separate consent required. Sensitive content (e.g., personal stories involving recovery, health) is curated by Omega during the staging/approval step.

**Deferred platforms:** Pinterest (quote graphics from legacy class recordings) and LinkedIn (highest revenue platform) are not in current scope. Omega's priority is revenue-generating platforms first (YT/TT/IG). Pinterest and LinkedIn may be added in a future phase.

### Part 6: Delivery Model

Managed service — Wilsch AI Services operates the clipping service, Omega is the client.

**Phase 1: Batch Processing**
Process 2026 1-on-1 lessons first (priority), then legacy class recordings as quality permits. AI extracts all potential clips. Wilsch AI operates the pipeline, curates output, and presents clip batches to Omega for approval before publishing. No volume cap on extraction.

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

Programme pricing now confirmed (see Part 2 Product Matrix: $1k–$10.5k LTV). Retainer priced as fraction of student lifetime value. Specific pricing in the [commercial doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-commercial-design-doc).

---

## Source

- **Transcripts:**
  - [Discovery Call 2026-03-08 (52 min)](https://drive.google.com/file/d/1xnIw-xMsN-KZXO72BA4z6DyYAq37wtBd/view) — Omega + Anthony + Marius
  - [Grooming 2026-03-08](https://drive.google.com/file/d/1fwumO3pBf5tRiCy08BU_qoPtxnzaj-m4/view)
  - [Fireflies Transcript](https://app.fireflies.ai/view/01KK6JXB75WTRS613Y4S56T5Z8)
  - [In-Person Meeting 2026-03-18 (~1h)](https://drive.google.com/file/d/1h-YrcQda38girPmoor3Ndsb8hqQM2jrk/view) — Omega + Marius at NS
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
  - /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-09-SALES-PIPELINE--deliverable/ffc1ee74-f0a9-4f7a-804c-cc74126b0ea1.jsonl — Pass 6 (2026-03-18 meeting integration, 13 resolutions)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

