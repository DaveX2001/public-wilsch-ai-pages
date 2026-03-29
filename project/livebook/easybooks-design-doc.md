---
publish: true
---

# EasyBooks — Design Doc
[[client-stafford]]

This document captures our shared understanding of the EasyBooks platform (formerly Livebook). It is a living document — evolved from v0.1 based on Stafford's formal RFP requesting build plan, timelines, and proof of capability for a four-engine narrative intelligence platform.

**How to read this doc:** Sections marked **Undefined** link to the meeting agenda for SA-Stafford discussion. Everything else represents our current understanding based on two alignment meetings, Stafford's RFP (6 sections A-F), and two technical specification documents he provided.


---

## Problem Statement

People's life stories go unpreserved. When someone dies, decades of experiences, achievements, and wisdom disappear with them. Children inherit a fraction of their parents' lives — the 40-50 years before they were born remain unknown unless someone deliberately captures them.

No existing tool makes story capture feel like a genuine conversation. Forms feel like chores. Questionnaires feel clinical. Writing it down requires a patience most people don't have. But AI does — AI has the absolute patience to sit, listen, and meet someone at their level for as long as it takes. AI can be relatable, adapt its modality (text or voice), and never rush. The right trigger at the right moment, paired with a listener who never tires, is what unlocks genuine stories.

**Preconditions:**
- People have stories worth preserving but lack the mechanism to capture them naturally
- Current tools (journals, memoir services, genealogy platforms) require effort that feels like work, not conversation
- A relatable trigger is needed — people won't start unprompted
- The capture experience must feel like two people going back and forth, not a data collection exercise

EasyBooks extends this problem beyond personal legacy. Founders lose institutional memory. Corporate conversations go unstructured. Creative IP is buried in unorganised experiences. The same AI patience that captures a grandmother's story can extract a founder's journey, structure a corporate case study, or produce a podcast from a raw conversation. The four engines (Vulture, Plug, Opera, Shakespeare) address this at different stages of the narrative lifecycle.

**Reference:** [Anthropic Interviewer](https://www.anthropic.com/research/anthropic-interviewer) — AI-powered interviews at scale, demonstrating that people open up to AI interviewers with 97.6% satisfaction when the experience feels like genuine conversation.

**Additional Preconditions:**
- The same extraction methodology applies across segments: personal legacy, founder journeys, corporate intelligence, creative IP
- A platform approach (not a single-use tool) is needed to serve multiple audience segments from one narrative infrastructure

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A platform that turns lived experience into lasting books — through genuine AI conversation that feels like talking to a like-minded friend, not a clinical interviewer. The book becomes a person's legacy: something their grandchildren can read, something they're proud of. |
| **Success** | A user opens the platform, sees their empty book visualized in front of their eyes — not a vague idea in their head, but a tangible artifact with empty pages. Something relatable from their day triggers them to talk. The AI meets them at their level, like a friend who genuinely cares. Their book becomes less empty. They come back because the book is never done. |
| **Done test** | "Can the SA review this as a concrete RFP response framework — with recommended phasing, schema-level specs per engine, and clear Undefined markers routed to the meeting agenda?" → **YES.** This is v0.2 — evolved from Stafford's formal RFP. Undefined items route to SA-Stafford discussion. |

---

## Approach

### Part 1: Product Identity & Audience

**Product:** EasyBooks. An AI interview + book platform — you talk, the AI asks the right questions, and your words become a printed book.

**Four-engine architecture (evolved from Livebook's three-agent pipeline):**

| Engine | Role | Evolved From |
|--------|------|-------------|
| **Vulture** | Growth engine — finds audiences, creates ads, distributes content | NEW — not in Livebook v0.1 |
| **Plug** | Entry point — landing page, short voice interview, instant book page preview | Livebook's context collector |
| **Opera** | AI interviewer — asks the right questions, collects material for a good book | Livebook's smart interviewer |
| **Shakespeare** | Book writer + printer — turns interview into chapters, formats and prints the book | Livebook's book producer |

**Core value proposition:**
- "You drop dead tomorrow. This is your book. Would you be happy?"
- *or:* "Your book is empty. Fill it."

**Audience segments (initial, including but not limited to):**

| Segment | Hook | Why it works |
|---------|------|-------------|
| **Young adults** | Aspirational — start building your legacy now | Pride in achievements, desire to be remembered |
| **Elderly** | Preservation — capture what your grandchildren will never know | 40-50 years of life that children never witnessed |
| **Children of aging parents** | Gift — give your parent a way to share their story | The sister-bought-dad-a-book pattern |

**V1 beachhead:** Network School (NS) community — built-in audience, immediate feedback loop. NS Family Book is a pre-existing concierge MVP (no-tech, Stafford-led) that validates demand before the platform exists.

**Expanded audience (from Stafford's Business Master Plan):**

| Segment | Product |
|---------|---------|
| **Founders & entrepreneurs** | Founder Journey books, Company Story editions |
| **Corporate B2B** | Customer intelligence, relationship strengthening, internal memory vault |
| **Creators & thought leaders** | Podcast episodes, creative licensing |

**Audience-scope analysis (SA extraction pass, Mar 2026):**

Stafford's technical specs describe 9+ audience segments. His transcripts name 4 concrete use cases — with zero overlap on founders, corporate, or creators. The specifications were AI-generated from broad vision; the transcripts reflect actual near-term actions.

| Source | Audiences Named |
|--------|----------------|
| **Specs** (EB Business Master) | Founders, HNWIs, parents, creators, thought leaders, 4 corporate types |
| **Transcripts** (Feb 18) | Network School community, My Hometown, elderly parents, heritage/diaspora |

These cluster into five paths, each with different scope implications:

| Path | Audience | Acquisition | V1 Viable? |
|------|----------|-------------|------------|
| **A: Community** | NS, My Hometown | Captive (event-based) | Yes — smallest scope |
| **B: Legacy** | Parents, elderly, heritage | Referral / emotional funnels | Yes — medium scope |
| **C: Mass Market** | Founders, creators, everyone | Growth engine required | Yes — largest scope |
| **D: Enterprise** | Corporate B2B | Enterprise sales | No — different product |
| **E: Platform** | Film, licensing, NFTs | Requires content mass | No — requires A/B/C first |

The core pipeline (interview → book) is identical across paths A, B, and C. The AI agents (Opera questioning, Shakespeare composition) are the same ~1 month build regardless of path. What changes is the application layer:

| Path | App Layer | App Build Effort |
|------|-----------|-----------------|
| **A: Community** | Operator dashboard — Stafford facilitates interviews in person | ~1-2 weeks |
| **B: Legacy** | User-facing web app — one flow (landing → interview → book) | ~3-4 weeks |
| **C: Mass Market** | Full platform — multiple funnels, analytics, growth loops | ~2+ months |

The audience path determines how much application development is needed beyond the AI agents.

**Undefined:** V1 audience path selection — determines automation level, template choice, and marketing approach. → [Meeting Agenda](#discussion-topics)

---

### Part 2: Engine Architecture — Vulture → Plug → Opera → Shakespeare

Four single-responsibility engines form the platform pipeline. Each engine has one job and does it extremely well. Vulture feeds Plug, Plug feeds Opera, Opera feeds Shakespeare.

```
Vulture ──→ Plug ──→ Opera ──→ Shakespeare ──→ EasyBook
(signal)    (entry)  (interview)  (composition)    (artifact)
```

#### 2.1 Vulture — Cultural Signal & Growth Intelligence Engine

Vulture discovers audience opportunities and generates campaign creatives. It is the traffic engine — without Vulture, users must be acquired manually. Vulture makes acquisition systematic and self-improving.

**Responsibilities:**
- Detect cultural niche opportunities (trending topics, emotional moments, seasonal triggers)
- Generate campaign creatives (hooks, scripts, video concepts) per niche
- Distribute across platforms (Instagram, TikTok, LinkedIn, X, YouTube Shorts)
- Measure performance and iterate daily — funnels become self-evolving

**Core schemas:**

```
niches                    creatives                 campaigns
------                    ---------                 ---------
id (uuid)                 id (uuid)                 id (uuid)
name                      niche_id (fk)             creative_id (fk)
category                  type (hook/script/video)  platform
score                     content                   status
trend_signals             variant_label             budget
created_at                performance_score         impressions
                          created_at                clicks
                                                    conversions
                                                    created_at
```

**North Star Metric:** Click → Interview Start Rate (Vulture creative → Plug micro-interview)

**Governance:**
- Human approval gate for paid campaigns — no automated spend without review
- Cultural sensitivity flags + escalation queue
- Brand guardrails (no manipulative content, no sensitive-targeting abuse)
- Rate-limit posting automation to avoid platform violations

#### 2.2 Plug — Entry Point

Plug gets someone from "curious" to "I already started my book" in under 5 minutes. It's a landing page with a story trigger — not a complex funnel system.

**What it does:**

| Step | What happens |
|------|-------------|
| **1. Route** | Send the user to the right opening question based on how they arrived |
| **2. Opening question** | "Tell us about the moment that changed your life" — triggers a real response |
| **3. Short voice interview** | 2-5 minutes, 3-5 questions, user speaks (audio-first, text fallback) |
| **4. Instant preview** | User's words appear as a designed book page — "this is already my chapter" |
| **5. Continue** | "Continue Your Book" — they already started, no hard sell needed |

**Core schemas:**

```
funnels                   question_sets             funnel_sessions
-------                   -------------             ---------------
id (uuid)                 id (uuid)                 id (uuid)
name                      funnel_id (fk)            user_temp_id
niche_id (fk)             question_1                funnel_id (fk)
headline_template         question_2                creative_id
identity_prompt_template  question_3                platform_id
question_set_id (fk)      question_4                started_at
cta_template              question_5                completed_at
status                    audio_enabled (bool)      preview_generated (bool)
created_at                max_duration_seconds       converted_to_app (bool)
                                                    book_started (bool)
```

**North Star Metric:** Interview Start Rate → Interview Completion Rate → Preview Generated %

**UX Principles:** < 5 minutes to start, audio-first, zero cognitive overload, emotionally safe, no negativity in first touch

#### 2.3 Opera — AI Interviewer

Opera is not a chatbot. It's an AI interviewer that collects the information needed to write a good book. Think of it as a world-class biographer persona — it knows what makes compelling stories and asks the right questions to get there.

**What it does (one persona, not three separate systems):**

| Capability | What changes in the interview | Example |
|-----------|------------------------------|---------|
| **Matches your tone** | Slows down when you're emotional, matches energy when you're excited | Person gets quiet about loss → AI says "Take your time" |
| **Knows what makes a good story** | When the story is flat (no conflict, no stakes), asks questions to add depth | "I went to school" → AI asks "What almost stopped you?" |
| **Tracks who/what/when** | Remembers people, places, events mentioned — doesn't repeat or lose context | You mention your father in minute 2 → AI follows up about him in minute 10 |

**How the interview progresses:**

| Phase | What the AI is doing | Example Question |
|-------|---------------------|-----------------|
| 1. Context | Getting the basics — who, where, what happened | "Where were you when this happened?" |
| 2. Emotion | Finding the feeling underneath | "What surprised you most about that?" |
| 3. Stakes | Understanding what was at risk | "What could you have lost?" |
| 4. Change | How this shaped you | "How are you different now?" |
| 5. Wisdom | What you'd tell your younger self | "What would your younger self think?" |

**Core schemas:**

```
interview_sessions        entities                  events
------------------        --------                  ------
id (uuid)                 id (uuid)                 id (uuid)
user_id (fk)              session_id (fk)           session_id (fk)
funnel_context            name                      description
phase                     type (person/place/org)    timestamp
turn_count                                          entities (fk[])
voice_profile_id

claims                    emotion_markers           story_scores
------                    ---------------           ------------
id (uuid)                 id (uuid)                 id (uuid)
event_id (fk)             session_id (fk)           session_id (fk)
content                   type                      narrative_strength
confidence                intensity                 emotional_depth
source_link               trigger_question          conflict_density
evidence_type                                       transformation_clarity
                                                    cinematic_potential
                                                    bestseller_readiness
```

**Key metric:** Is the interview producing material good enough to write a compelling book chapter? (conflict present, emotions captured, timeline clear)

**Interview style:** Configurable — gentle biographer, direct interviewer, warm conversationalist, documentary journalist, reflective listener. Adapts to who the person is.

**V1 scope (SA extraction pass, Mar 2026):** Opera must shine — it is the product differentiator. The AI must genuinely respond to what you say and probe deeper (not follow a fixed script). Without this, it's just a questionnaire with nicer wording. Voice cloning, avatar mode, and cinematic features are out of V1 scope.

#### 2.4 Shakespeare — Book Writer + Printer

Shakespeare turns the interview material into a finished book. Opera collects the information; Shakespeare writes and prints it.

**What it does:**

| Step | What happens | Outcome |
|------|-------------|---------|
| 1. Write in your voice | Matches your tone, vocabulary, how you speak | Chapters read like YOU wrote them |
| 2. Turn interview into chapters | Organizes events into flowing prose | Raw transcript → readable book |
| 3. Organize the book | Chapter boundaries, titles, reading order | A book that makes sense front to back |
| 4. Polish the writing | Sentence flow, pacing, sensory detail | Professional-quality prose |
| 5. Apply book template | Page layout, fonts, margins for the format | Memoir, founder story, or yearbook layout |
| 6. Set print specs | Paper size, margins, ink settings | 6x9 KDP B&W (V1 default) |
| 7. Calculate spine width | `pages × 0.002252"` — one line of math | Book spine fits the page count |
| 8. Generate cover | Title + spine + back cover as one PDF | Ready for printing |
| 9. Check before printing | Margins ok? Fonts embedded? Images sharp? | Catches errors before wasting a print run |
| 10. Export | Interior PDF + cover PDF | Upload to KDP → printed book arrives |

**Core schemas:**

```
books                     chapters                  book_versions
-----                     --------                  -------------
id (uuid)                 id (uuid)                 id (uuid)
user_id (fk)              book_id (fk)              book_id (fk)
title                     title                     version_number
template_id (fk)          content                   snapshot_hash
voice_profile             order                     created_at
status                    emotional_arc_position

printer_profiles          export_jobs
----------------          -----------
id (uuid)                 id (uuid)
name                      book_version_id (fk)
trim_size                 printer_profile_id (fk)
bleed                     status
margin_rules              interior_pdf_url
spine_formula             cover_pdf_url
paper_type                preflight_passed (bool)
ink_coverage_limits       created_at
```

**V1 starting point:** 6" × 9" KDP Black & White — widely supported, cost-effective, low complexity, large market. Additional profiles (color, hardback, yearbook) added in later phases.

**Print compliance requirements:**
- PDF with embedded fonts, CMYK color profile, 300 DPI images
- Correct trim size, proper bleed (0.125"), no crop marks
- Minimum 0.375" outer margin, 0.75" inside margin for thicker books
- Dynamic spine calculation, barcode safe zone
- Preflight validation blocks non-compliant exports

**Key metric:** Does the PDF print correctly on the first try?

#### 2.5 How Engines Pass Data

| From → To | What gets passed |
|-----------|-----------------|
| **Vulture → Plug** | Which ad/campaign brought the user (tracking IDs in the URL) |
| **Plug → Opera** | The micro-interview transcript + audio + how they arrived |
| **Opera → Shakespeare** | Organized story material: people, events, timeline, emotional moments + writing style preference + book type |
| **Shakespeare → Output** | Finished PDFs ready for printing or download |

#### 2.6 Platform Services (Cross-Cutting)

All engines share:
- Identity & access management (IAM)
- Consent & permissions service (versioned consent records, not checkboxes)
- Data store + encryption at rest and in transit
- Audit logging (every automated action traceable)
- Content moderation + safety
- Analytics + attribution (creative → funnel → install → book start → purchase)
- Export control + redaction

**Reference:**
- [Anthropic Interviewer](https://www.anthropic.com/research/anthropic-interviewer) — AI interviews at scale (1,250 professionals, 97.6% satisfaction)

---

### Part 3: Revenue Model Principle

**"Extraction is the value. Creation is free."**

| Pipeline Stage | Cost | Rationale |
|---------------|------|-----------|
| **Plug** (triggers) | Free | Hooks them in, creates daily engagement habit |
| **Opera** (interview) | Free | Builds emotional investment through genuine conversation. The interview IS the sales funnel — people invest their stories. |
| **Shakespeare** (book output) | Paid | Extraction crystallizes value — turning conversation into a tangible artifact |

The book is never done. Life keeps happening. Plug keeps triggering. Opera keeps interviewing. Shakespeare keeps producing new chapters. **Continuous extraction = subscription model.** Users don't pay for access — they pay for their book growing.

**Expanded revenue streams (from Stafford's Business Master Plan):**

| Stream | Engine | Stage |
|--------|--------|-------|
| SaaS subscription | Platform | Core |
| Premium book creation fees | Shakespeare | Core |
| Podcast generation credits | Opera → Shakespeare | Extension |
| NFT minting fees | Platform | Extension |
| NFT royalty percentage | Platform | Extension |
| Corporate contracts | Opera (B2B) | Extension |
| Creative licensing commissions | Platform | Extension |
| Narrative consultancy | Opera | Extension |
| AI voice cloning packages | Opera | Extension |
| Enterprise memory licensing | Platform | Extension |

**Revenue-scope analysis (SA extraction pass, Mar 2026):**

Stafford's documents list 10 revenue streams with no prioritization. Each stream requires different infrastructure — picking 2 vs 5 is the difference between a focused MVP and an architecture that serves everything. The pipeline principle ("extraction free, creation paid") means payment infrastructure only exists at Shakespeare (book output), not at earlier stages.

All 8 Extension streams are out of V1 scope. V1 revenue candidates are the 2 Core streams: SaaS subscription and/or premium book creation fees. The user journey suggests the first dollar is a per-book fee (person completes interview → wants the book → pays). Subscription is the retention model (book is never done → keep paying).

**Undefined:** V1 revenue stream prioritization — Stafford ranks his 10 streams to determine which 1-2 fund V1. Pricing specifics remain open. → [Meeting Agenda](#discussion-topics)

---

### Part 4: Delivery & Engagement

**Engagement model:** Outsourced AI department — "I own the function, you own the results." Wilsch AI owns the delivery function (agents, systems, improvement loop). Stafford owns the results and outputs. Stafford trains his own operators.

**Three-Slice Ownership (from [#847 Contract Strategy](https://github.com/DaveX2001/deliverable-tracking/issues/847)):**

| Slice | Asset | Owned By |
|-------|-------|----------|
| **1. AI Function** | Built engines, agents, systems | Wilsch AI |
| **2. Operators** | People trained to work with AI | Stafford (self-trained) |
| **3. Improvement Loop** | System Engineer (continuous improvement) | Wilsch AI |

**Team:** Marius (SA / delivery lead) + David (developer) + AI force multiplier. **Undefined:** Whether the AI-augmented team model is communicated to Stafford. → [Meeting Agenda](#discussion-topics)

**Structure:** 3-month initial retainer, renewable if both sides happy. Base retainer + success-based component (concept — no numbers in this doc).

**Undefined:** Commercial terms — retainer amount, success fee structure, space fee, duration specifics. → [Meeting Agenda](#discussion-topics)

---

### Part 5: Methodology Foundation

Opera's interview methodology is adapted from a tested knowledge extraction approach used in AI-assisted design work — proven across multiple projects to produce genuine, structured output from conversational input.

The core principles (same-level interaction, one element at a time, relatable triggers, adaptive probing) transfer from professional knowledge extraction to personal story extraction. The mechanism is identical; the domain and emotional tone differ.

---

### Part 6: Technical Architecture

**Recommended tech stack (based on Stafford's requirements and team capability):**

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Backend | FastAPI (Python) | Async-first, schema validation, team expertise |
| Database | Supabase (PostgreSQL) | Row-level security, real-time, auth built-in |
| Vector Store | pgvector (via Supabase) | Semantic search for story matching |
| AI/LLM | Claude API | Tool-calling, structured outputs, conversation |
| PDF Engine | Dedicated PDF library (not HTML-to-PDF) | Print compliance requires controlled rendering |
| Audio/TTS | Whisper (STT) + open-source TTS | Audio-first input, voice output |
| Frontend | React | Standard, Stafford's team can maintain |
| Hosting | TBD per deployment model | Depends on data sovereignty requirements |

**Database tables** (shared across all engines): Users, interviews, people/places/events mentioned, book chapters, book versions, export jobs, consent records, audit logs.

**Every book statement links back to the interview:** If a sentence appears in the book, you can trace it back to exactly when the person said it. Essential for credibility, edits, and disputes — and a requirement from Stafford's RFP (Section F).

---

### Part 7: V1 Scope & Build Phases

**Core principle (SA extraction pass, Mar 2026):** The build is constant — configuration is the variable. The core pipeline (Plug entry → Opera interview → Shakespeare book) is identical regardless of audience, revenue model, or template choice. Stafford's 5 configuration choices (audience path, revenue stream, book template, voice style, output format) determine how the product is positioned, not what is built.

**V1 Core — In Scope:**

| Engine | V1 Scope | Must Shine? |
|--------|----------|-------------|
| **Plug** | Landing page → short voice interview (3-5 questions) → instant book page preview → hand off to Opera | Must exist |
| **Opera** | AI interviewer that responds to what you say, probes deeper, knows what makes a good book, tracks people/events/timeline | Must shine |
| **Shakespeare** | Turns interview into chapters, writes in your voice, formats as book, prints via KDP (6x9 B&W + digital PDF) | Must work |

Opera is the product differentiator. Without adaptive questioning that genuinely responds to what the user says, the interview is a questionnaire — not the "patient AI friend" from the problem statement. Plug's instant preview and Shakespeare's book output serve Opera's extracted material.

**30-Day Delivery Commitment (by April 23rd):**

Every item traces back to a specific section of Stafford's EB Business Master or March Technical Lifecycle Plan.

| # | What gets delivered | Source | Priority | Status |
|---|-------------------|--------|----------|--------|
| | **PLUG (Entry Point)** | | | |
| 1 | Landing page with an opening question that triggers a real response | March Tech: Plug §1-2 | Should | ✅ In |
| 2 | Short voice interview (3-5 questions, user speaks) | March Tech: Plug §3 | Should | ✅ In |
| 3 | User's words appear as a designed book page instantly | March Tech: Plug §4 | Should | ✅ In |
| 4 | Multiple landing pages for different audiences | March Tech: Plug §1 (4 types) | | ❌ Out |
| 5 | Headlines that adapt based on who's clicking | March Tech: Plug Phase 2 | | ❌ Out |
| 6 | AI generates different preview designs per person | March Tech: Plug Phase 2 | | ❌ Out |
| 7 | Share your book preview on social media | March Tech: Plug Phase 2 | | ❌ Out |
| | **OPERA (AI Interviewer)** | | | |
| 8 | AI responds to what you say — probes deeper, matches your tone, reads the room | March Tech: Opera §Layer 1 + Phase 2 (pulled in) | Must | ✅ In |
| 10 | AI knows what makes stories compelling and asks accordingly | March Tech: Opera §Layer 2 | Should | ✅ In |
| 11 | AI tracks people, places, events — doesn't lose context | March Tech: Opera §Extraction | Must | ✅ In |
| 12 | AI scores whether the interview is producing good book material | March Tech: Opera §Story Quality | Should | ✅ In |
| 13 | AI speaks in a cloned version of your voice | March Tech: Opera Phase 2 | | ❌ Out |
| 14 | AI appears as a visual avatar | March Tech: Opera Phase 2 | | ❌ Out |
| 15 | AI rates stories for "cinematic potential" | March Tech: Opera Phase 2 | | ❌ Out |
| 16 | Pick up the interview across multiple sessions | March Tech: Opera Phase 3 | | ❌ Out |
| | **SHAKESPEARE (Book Writer + Printer)** | | | |
| 17 | One book template (chosen based on audience) | March Tech: Shakespeare §Layer 5 | Should | ✅ In |
| 18 | Chapters read like YOU wrote them (matches your voice) | March Tech: Shakespeare §Layer 1 | Should | ✅ In |
| 19 | Interview material becomes flowing chapters | March Tech: Shakespeare §Layer 2 | Must | ✅ In |
| 20 | Printed book via KDP (6x9 B&W) + digital PDF | March Tech: Shakespeare Phase 1 | Must | ✅ In |
| 21 | PDF checked before printing (catches errors) | March Tech: Shakespeare §Preflight | Should | ✅ In |
| 22 | Cover + spine generated automatically | March Tech: Shakespeare §Layers 7-8 | Should | ✅ In |
| 23 | Physical test print sent to KDP | March Tech: Shakespeare Phase 1 | Should | ✅ In |
| 24 | Color printing | March Tech: Shakespeare Phase 2 | | ❌ Out |
| 25 | Other book sizes beyond 6x9 | March Tech: Shakespeare Phase 2 | | ❌ Out |
| 26 | Yearbook template (multi-author community books) | March Tech: Shakespeare Phase 2 | | ❌ Out |
| 27 | E-reader format (EPUB) | March Tech: Shakespeare Phase 2 | | ❌ Out |
| | **ENTIRE SUBSYSTEMS** | | | |
| 28 | Growth engine that finds audiences and creates ads | March Tech: Vulture (full section) | | ❌ Out |
| 29 | Mint stories as NFTs / digital collectibles | EB Master: §Products & Revenue | | ❌ Out |
| 30 | Corporate division (company intelligence, memory vault) | EB Master: §Target Market | | ❌ Out |
| 31 | Marketplace to license stories for film/TV | EB Master: §Products & Revenue | | ❌ Out |
| 32 | Turn conversations into podcast episodes | EB Master: §Extensions | | ❌ Out |
| | **APP LAYER** (not in Stafford's engine specs) | | | |
| 33 | Landing page with opening question | Required by Plug | Should | ✅ In |
| 34 | Voice recorder in the browser | Required by Opera | Must | ✅ In |
| 35 | User login | Required by all | Must | ✅ In |
| 36 | Interview conversation screen | Required by Opera | Must | ✅ In |
| 37 | Book page preview display | Required by Plug | Should | ✅ In |
| 38 | Download / view your finished book | Required by Shakespeare | Should | ✅ In |
| 39 | Admin panel for managing interviews and books | Required for operations | | ❌ Out |
| 40 | Analytics dashboard | Required for optimization | | ❌ Out |

**Score: 18 in (7 Must / 11 Should) · 21 out.** This table IS the 30-day commitment. If items from the "Out" column are required in the first engagement, the scope exceeds what can be delivered by April 23rd.

**Build separation:** AI agents (Opera questioning, extraction, composition) and the application layer (frontend, audio recording, user flows, book viewer) are different skill sets built in parallel. The agents produce data and content; the application displays and interacts with it. Both are required for a working product.

**Phasing:**

**Phase 1 — MVP (Month 1): Core Pipeline**
- Plug: landing page with opening question, voice recording, instant book page preview
- Opera: AI interviewer that responds to what you say, probes for conflict/stakes/transformation, organizes people/events/timeline
- Shakespeare: one book template, writes chapters in user's voice, 6x9 KDP B&W + digital PDF, checks PDF before printing, generates cover
- Physical test print with KDP

**Phase 2 — V1 (Month 2-3): Polish + Scale Prep**
- Opera: gets better at detecting flat stories and asking the right follow-ups, more interview styles
- Shakespeare: additional book templates based on audience, color printing option
- Analytics dashboard, shareable book snippets for social media
- Admin panel for managing interviews and books

**Phase 3 — V2 (Post-retainer): Extensions**
- Vulture (growth/campaign engine — manual acquisition is acceptable until this point)
- NFT/tokenisation layer
- Corporate B2B division
- Voice cloning + avatar interview mode
- Interesting Conversations (live-to-podcast engine)
- Creative licensing marketplace

**Undefined:** V1 product configuration — template (memoir/yearbook/founder), voice style, and output format selection depend on audience path. → [Meeting Agenda](#discussion-topics)

---

### Part 8: Compliance & Governance

Stafford's RFP lists compliance as non-negotiable (Section F). Compliance is a feature, not a policy doc — enforced through data model constraints, permissioning, consent workflows, audit logs, and human approval gates.

**Consent service (must exist from MVP):** Consent is not a checkbox — it is a versioned record. Types: data storage, audio recording, transcription, publishing (private/unlisted/public), voice clone, avatar, co-author contributor agreement, corporate interview, NFT mint (with permanence disclosure). All consent changes are audit logged. Consent must be scoped (what + where + how long) and revocable where feasible (except immutable NFT aspects, disclosed upfront).

**Operational compliance by stage:**

| Stage | Engine | Key Controls |
|-------|--------|-------------|
| Acquisition | Vulture | Paid campaigns require human approval, brand guardrails, cultural sensitivity flags |
| Onboarding | Plug | Minimal data capture, clear consent, age gating (especially yearbooks) |
| Story capture | Opera | Emotional safety + skip/pause/stop controls, no therapy claims, accurate extraction + confidence marking |
| Publishing | Shakespeare | Preflight print validation, copyright/IP checks, redaction options, private vs public export |
| Distribution | Extensions | Per-output permissions, contributor rights checks, permanent/irreversible actions gated and disclosed |

**Privacy & security baseline:** Encryption at rest and in transit, role-based access control, tenant isolation (corporate), secure secret storage, data retention schedules (configurable by tenant), user export and deletion workflows (with blockchain exception disclosures for NFTs).

**Safety and content moderation:** Automated filters + human review queue. Sensitive niche gating (grief, trauma). Prohibited content enforcement. "No therapy claims" language policy across all engines.

---

### Part 9: Proof of Capability

Stafford's RFP Section D requests examples of similar work. Candidate projects for SA review:

| Requirement | Candidate Project | Relevance |
|-------------|------------------|-----------|
| AI-agent systems (tool-calling, structured outputs) | TBD — SA selects | Direct parallel to Opera/Plug |
| Growth/automation systems | TBD — SA selects | Relevant to Vulture |
| Document generation (PDF, print-ready) | TBD — SA selects | Direct parallel to Shakespeare |
| Audio/transcription | TBD — SA selects | Relevant to Opera voice mode |
| Creative content automation | Paul Faceless YouTube | Content generation + automation pipeline |

**Undefined:** Which VeloxForce projects to showcase — confidentiality constraints apply. SA makes final selection. → [Meeting Agenda](#discussion-topics)

---

## Source

**Transcripts:**
- [hmf-nphz-oeh](https://app.fireflies.ai/view/01KHRCZ9PFND0HC5Z4R5W1ZH5B) — 50 min, Feb 18 2026 — Product vision deep-dive with Stafford
- [hhb-sesi-hsp](https://app.fireflies.ai/view/01KHRGHPQTBECRD3JRHTTGTQ7Y) — 14 min, Feb 18 2026 — Business relationship, VP delivery role, E-Myth
- [euq-ocaj-ajd](https://app.fireflies.ai/view/01KHXJSM63VJB0JT88KS8E4NZJ) — 41 min, Feb 20 2026 — Compensation model, app scope, success fee discussion

**RFP & Technical Specs (Stafford, March 2026):**
- Stafford's formal RFP (6 sections A-F) — posted as [#874 comment 2026-03-08](https://github.com/DaveX2001/deliverable-tracking/issues/874#issuecomment-2707966123)
- [EB Business Master Plan.docx](https://github.com/user-attachments/files/25821977/EB.-.My.2026.Business.Master.-.Easybooks.docx) — 8 revenue streams, NFT layer, corporate B2B, creative marketplace
- [March Technical Lifecycle Plan.docx](https://github.com/user-attachments/files/25821978/March.Technical.Plug.opera.and.Shakesspeare.and.full.lifecycle.plan.docx) — Developer-ready specs per engine (Plug, Opera, Shakespeare, compliance framework)

**Data Artifacts:**
- `My_NS_Family_Book_Original_Drawings_and_UX_Plan.docx` — Original UX flow, design system, book templates
- `My_NS_Family_Book_Updated_UX_Project_Reset.docx` — Updated UX with resolved feedback
- 7x WhatsApp mockup images — Pick Your Story, Landing, Social Linking, Permissions, Profile, Sign In, Book Confirmation

**Cross-references:**
- [#874 — Epic: Livebook Pre-Sales](https://github.com/DaveX2001/deliverable-tracking/issues/874)
- [#847 — Contract Strategy & Retainer Model](https://github.com/DaveX2001/deliverable-tracking/issues/847)
- [#1078 — JA Design Doc Pass 1](https://github.com/DaveX2001/deliverable-tracking/issues/1078)

**External References:**
- [Anthropic Interviewer](https://www.anthropic.com/research/anthropic-interviewer) — AI-powered interviews research
- [Stafford Proposal v1](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/stafford-plug-opera-proposal) — Previous strategic positioning (methodology-heavy)
- [Monthly Plan Feb-Mar](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/monthly-plan-2026-02-23-to-2026-03-31) — Stafford = one additional retainer slot

**Sessions:**
- v0.1: `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/ab077198-670d-47ef-b884-eca052c0b443.jsonl`
- v0.2: `/Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-09-SALES-PIPELINE--deliverable/041422f6-c485-4011-af8c-edd509708a61.jsonl`
- v0.3 (SA extraction): `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/905c048c-84e0-4dd0-ab8c-af065edc87c8.jsonl`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

