---
publish: true
---

# Omega AI Video Clipping Service
[[omega-clipping-service-design]]

Design doc for an AI-powered service that extracts "pearls" (high-value teaching moments) from Omega Bone's vocal coaching content and produces post-ready short-form clips for social media.

---

## Problem Statement

Omega Bone is a vocal coach with 25+ years of experience and 7,400+ students trained worldwide. She has accumulated a large library of educational singing content — a 10-track album ("Come With Me") and ~300 YouTube class recordings ("Learn to Sing with Omega", COVID era, 2-3h each) — that contains high-value moments buried in hours of raw footage.

These moments — coaching breakthroughs, authenticity discoveries, quotable teaching wisdom — are what Omega calls "pearls." She cannot efficiently identify and extract them manually: "sorting through them makes my brain and my eyes bleed." Generic clipping tools like Opus Clip fail because they detect topics and engagement signals, not the domain-specific coaching moments that constitute Omega's value. As her business advisor Anthony put it: "there's no topic" for generic tools to latch onto.

Omega needs an AI-powered service that understands her teaching domain well enough to extract pearls from raw content and produce post-ready short-form clips for social media (YouTube Shorts, TikTok, Instagram Reels), feeding her marketing funnel: clips → website → discovery session → paid programme.

This design doc does NOT cover: website development, pricing strategy, album production, or the programme structure itself.

**Preconditions:**
- "Come With Me" album exists (10 tracks, produced music videos on YouTube)
- ~300 Learn to Sing class recordings exist on YouTube (Streamyard → YouTube)
- Business funnel is defined (Figma website redesign, 3-tier programme structure)
- Persona document describing the target avatar is pending from client
- Album release date: March 29th (urgency driver)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Omega has a managed AI service that extracts pearls from her content library and delivers post-ready short-form clips she can publish directly to social media. |
| **Success** | Omega reviews a batch of AI-generated clips from her album and class recordings, finds them publishable without manual editing, and confirms they match her teaching philosophy — authenticity over technical perfection, "the next natural singer, not another Whitney Houston." |
| **Done test** | Can we write a meeting agenda with open design questions about how the clipping service works? If no → the design is complete and ready for implementation. |

---

## Approach

### Part 1: Data Sources & Input Types

Two content types serve as input, both hosted on Omega's YouTube channel (@omegabone):

**Primary — "Come With Me" Album (10 tracks)**
Produced music videos featuring nature footage with lyric overlays and Omega's original songs. Single-camera, already edited. Tracks: Beautiful, One, Top of the World, The Beast, I Am, Mirror, Dreams (Living in Love), Born Again, Light of GOD, Come With Me. Album release: March 29th.

**Secondary — Learn to Sing Class Recordings (~300 videos)**
COVID-era live-streamed classes via Streamyard, published to YouTube (mirrored on Facebook). Zoom-grid format with ~10-25 students on camera, 2-3 hours each. Individual students perform songs of their choice, Omega provides feedback. ~5 clippable moments per class.

**Pending:** Persona document from client describing the target avatar in detail. To be integrated when received.

### Part 2: Target Avatar & Content Strategy

The target avatar is psychographic, not demographic: creative adults who carried a childhood dream of singing but never pursued it. Anthony (business advisor) defined the exclusions: not entrepreneurs, not parents — "there's no mention whatsoever of children, business, Entrepreneur on the YouTube channel." Omega clarified: "It's not feminine. It's the creator."

Clips serve Omega's marketing funnel:
- **Top of funnel:** Album clips drive awareness (album = "the top of the funnel" per Omega). Clip emotion: accessibility — "I could do that too."
- **Mid funnel:** Class clips show transformation proof. Clip emotion: confidence — "look how far they came."
- **Conversion:** All clips CTA to omegabone.com → free 30-min discovery session → 3-tier paid programme (30-day / 90-day / 3.5-month album).

### Part 3: Pearl Taxonomy

A "pearl" is a high-value moment worth extracting as a short-form clip. Omega's pearls follow a narrative arc: relatable starting point → coaching moment → breakthrough. This matches the testimonial format on her website redesign.

Four pearl categories identified from the discovery call:

1. **Coaching feedback moments** — Omega giving specific, actionable advice (e.g., "lean into your French accent, don't try to obscure it")
2. **Authenticity breakthroughs** — A student singing naturally for the first time, finding their own voice ("I'm looking for the next natural singer")
3. **Quotable wisdom** — One-liners suitable for text overlay or Pinterest images ("You didn't lose the ability to sing. You just never had someone show you how.")
4. **Tidbits** — Short, impactful teaching moments that stand alone

**Undefined:** Pearl signal refinement — Omega needs to validate these four categories and define specific signals the AI should detect. See [Meeting Agenda Topic 4](#4-pearl-signal-definition-for-ai-detection).

### Part 4: Processing Pipeline

Abstract pipeline with Claude Code SDK as the chosen implementation approach:

1. **Ingest** — Download video from YouTube, extract audio track, generate transcript
2. **Analyze** — AI reviews transcript + audio for pearl signals, identifies candidate clip timestamps. Domain knowledge (Omega's teaching philosophy, pearl taxonomy) is primed into the model context.
3. **Clip** — Extract video segments at identified timestamps, add captions from transcript, format per platform specifications
4. **Stage** — Output clips to staging area (unlisted YouTube or Google Drive) for Omega's review and approval before publishing

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

Managed service — VeloxForce operates the clipping service, Omega is the client.

**Phase 1: Batch Processing**
Process album content first (priority), then class recordings. AI extracts all potential clips. Omega curates — reviews staged clips and approves for publishing. No volume cap on extraction.

**Phase 2: Ongoing Processing**
Recurring content: Sunday group classes + 10 hours/week private lessons. Expected cadence: ~5 clips per class, ~1 clip per private lesson. Omega indicated a year-long engagement timeline.

Pricing is not in scope for this design doc — covered separately in the client proposal.

---

## Source

- **Transcripts:**
  - [Discovery Call 2026-03-08 (52 min)](https://drive.google.com/file/d/1xnIw-xMsN-KZXO72BA4z6DyYAq37wtBd/view) — Omega + Anthony + Marius
  - [Grooming 2026-03-08](https://drive.google.com/file/d/1fwumO3pBf5tRiCy08BU_qoPtxnzaj-m4/view)
  - [Fireflies Transcript](https://app.fireflies.ai/view/01KK6JXB75WTRS613Y4S56T5Z8)
- **Artifacts:**
  - [Figma: Vocal Coach Website Redesign](https://github.com/user-attachments/files/25824674/screencapture-figma-make-I5V9i7drJ3FCziHPcPHmDh-Vocal-Coach-Website-Redesign-2026-03-08-22_50_40.pdf) — 3-tier programme, avatar messaging, testimonial format
  - [YouTube: Come With Me Album (playlist)](https://www.youtube.com/playlist?list=PLFWa3WnH-5diGa82MJNQB0sgGKMzO89pz) — 10 tracks, album content
  - [YouTube: Learn 2 Sing with Audrey (live)](https://youtube.com/live/Odr_khKGmuc) — Recent class format example
  - [YouTube: Ep. 2 Learn to Sing (2021)](https://youtu.be/XxCaFDsWPfs) — Legacy class format example
  - [Epic #1071](https://github.com/DaveX2001/deliverable-tracking/issues/1071) | [Design Issue #1076](https://github.com/DaveX2001/deliverable-tracking/issues/1076)
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/bc288ebc-dddf-4d6b-aeb6-39b09aac97f0.jsonl
