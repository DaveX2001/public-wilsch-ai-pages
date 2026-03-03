---
publish: true
---

# Junior Architect Operations Manual — Wilsch AI Services
[[business-identity]]

Systems documentation for the **Junior Architect** position. Structure follows [EMyth Systems Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-systems-guide) (9-step system documentation). Each system specifies: Result, Diagram, Benchmarks, Accountabilities, Timing, Resources, Quantification, Standards, Documentation.

> _"The system runs the business. The people run the system."_ — Michael Gerber

**Reader:** AI operator who understands Claude Code but not the JA workflow.

**Core Principle:** Define what IS known, surface what ISN'T. The design doc is both the output and the diagnostic tool. Working through each section reveals whether you have enough context to define it. If yes — write it. If no — create a meeting agenda. The loop runs until no Undefined sections remain.

---

## System 1: Extraction Pass

### 1. Result

To produce complete design docs through iterative knowledge extraction from sources — by working through design doc sections, writing what can be defined and converting what cannot into meeting agendas — until no Undefined sections remain and the delivery pipeline has what it needs.

### 2. Diagram

```
SCOPE → SURFACE → PROBE → UPDATE → ASSESS
  ↓         ↓         ↓         ↓         ↓
Pick part  List      Probe     Write     Publish
+ sources  ambiguities  one-by-one  diffs    + review
```

One extraction pass per session. Session-atomic: the full cycle completes before the session ends.

### 3. Benchmarks

#### SCOPE: Define the Extraction Boundary

1. Identify what to work on — a specific Approach part, component, or scope unit
2. Read the **project index** for the epic (hippocampus artifact listing all sources)
3. Present source selection as a **table with rationale**, recency-biased: most recent pass conversation > latest design doc version > recent transcripts > older artifacts
4. Ask: "Anything else beyond the project index you want me to load?"
5. **Gate:** AskUserQuestion — user confirms scope + sources before proceeding

**Why the table matters:** Source declaration builds shared understanding. The user sees which sources you intend to read and WHY — they can add context you don't have. (Evidence: #852 Pass 4 — user said "I really like the way we do this.")

#### SURFACE: Produce the Uncertainty List

1. Read the scoped part text AND user-directed sources
2. Generate a numbered list of uncertainties grouped by scope unit
3. **Filter** items already answered in loaded sources — don't surface what the data already resolves
4. Present using validated format: bold section headers, numbered sparks (S1, S2...), calibrated-out items shown separately
5. **Gate:** AskUserQuestion — user validates list before proceeding

**Critical rule:** List, don't discuss. SURFACE produces a list. No resolution attempts, no investigation, no discussion — even when findings are interesting. Interesting findings become list items, not conversations.

**Source authority belongs to the user.** Do NOT autonomously select which transcripts to read during SURFACE. Transcript mining is PROBE work.

#### PROBE: One Item at a Time

1. Use `sequential_thinking` MCP tool before EACH question — self-route to the source most likely to have the answer
2. Present a **context paragraph** (what the source says, what changed, what's unclear) followed by a **short question** (one line, forces a choice)
3. AskUserQuestion with 2–3 options + permanent **"Next"** option (user owns advancement)
4. After each answer: think — *what would a stranger still not understand about this topic?* Ask that
5. **Visual examples BEFORE abstract choices** — when a choice requires understanding concrete options, show them first
6. **One item at a time.** One example at a time. The one-by-one principle applies to everything requiring user judgment
7. Not probing further is a valid outcome — routes to: meeting agenda, next extraction pass, or backtrack

**Self-routing priority:** Check sources first, user is the convergence point. Asking the user a question already answered in a loaded transcript = protocol violation.

**Probing calibration:** Probe until someone who wasn't in the session would understand the design decision. Calibrate to Solution Architect understanding.

**Push-back authority:** Challenge to make implicit reasoning explicit — not to be right. When the user can articulate the reasoning, accept it. When they cannot, probe deeper.

#### UPDATE: Write Resolutions into Documents

1. Read hippocampus references BEFORE writing:
   - `~/.claude/skills/hippocampus/references/design-doc-workflow.md` (stable components, template)
   - `~/.claude/skills/hippocampus/references/meeting-agenda-workflow.md` (5-question structure, template)
2. Route by outcome:

| PROBE outcome | Document |
|---------------|----------|
| Resolved items | Design doc (via hippocampus) |
| Undefined items | Meeting agenda (via hippocampus) |
| Mixed | Both — design doc first, meeting agenda second |

3. **Per-section gate loop** — for EACH section or component:
   - Draft diff for THIS section ONLY
   - Present via AskUserQuestion (Apply / Adjust)
   - On Apply: write THAT section
   - STOP. Do NOT write the next section yet
   - Draft next section. Repeat.
4. **Voice shift:** PROBE captures the user's thinking through conversation. UPDATE writes for the design doc's **reader** — the Developer who implements, the client who aligns, the SA who reviews. Verbatim fallback in the doc = ambiguity not truly resolved.
5. **Simple language.** Tables and examples before paragraphs. If the user says "why are you making this so complicated?" — you are.
6. Inline `**Undefined:**` markers for unresolved items, each linking to a specific meeting agenda topic. **1:1 mapping** — every Undefined marker generates exactly one discussion topic. No exceptions.
7. Per-element source attribution: Fireflies link + search anchor words (ideal), session reference in Source section (minimum)

**Anti-patterns from observed practice:**
- Writing 2+ sections after a single Apply = violation. Every time.
- Writing the entire meeting agenda after approving Meeting Goal = violation. Every time.
- Presenting 3 examples in a batch = violation. Walk each one-by-one.

#### ASSESS: Publish and Review

1. Publish via hippocampus: `verify_publish.sh "{tier}" "{filename}"`
2. Present commit link + published URL as formatted list
3. **Pause** — say "Review when ready — I'll wait." Do NOT ask the re-entry question yet
4. **Wait** for user to signal review is complete (user reads via Speechify — different medium, different quality of judgment)
5. Scope next pass using **multi-track format** with named items:

```
Next pass scope — two tracks:

Track 1: [Name] (items A1–A6)
- Item description
- Item description

Track 2: [Name] (items B1–B3)
- Item description
```

6. Propose next scope via AskUserQuestion — user has final decision authority
7. Post issue comment via `/issue-comment` formalizing re-entry

**ASSESS applies to ALL outputs** — not just design docs. When the output is sub-issues, status comments, or observations: still publish, still present, still pause for review.

**Session completion is AI-governed.** When the pass is complete, announce it:

```
✅ EXTRACTION PASS COMPLETED

Part: [which part was extracted]
Published: [doc URL]
Next start: [where next session should begin]
```

### 4. Accountabilities

Junior Architect (interactive AI mode — human thinking aided by AI). The human provides domain knowledge and judgment. The AI provides structure, source investigation, and documentation.

### 5. Timing

- One extraction pass per session
- Session-atomic: SCOPE through ASSESS completes before session ends
- At 80% token usage: finish current stage, then ASSESS with what you have

### 6. Resources

| Resource | Purpose |
|----------|---------|
| `/probe` command | SCOPE → SURFACE → PROBE stages |
| `/capture` command | UPDATE → ASSESS stages |
| hippocampus skill | Document creation + publishing |
| `sequential_thinking` MCP tool | Reasoning before each PROBE question |
| Fireflies / `/read-transcript` | Transcript access and mining |
| `conversation-reader` skill | Read prior session context |
| GitHub Issues | Issue tracking (DaveX2001/deliverable-tracking) |

### 7. Quantification

- Extraction passes completed per project (progress metric)
- Undefined markers resolved per pass (pass effectiveness)
- Undefined markers remaining in design doc (completion distance)
- SURFACE items that were already answered in sources (SURFACE quality — lower = better)

### 8. Standards

- Every AskUserQuestion during PROBE: 2–3 options + permanent "Next" option
- Per-section gate in UPDATE: never batch sections
- Source attribution per element in design doc
- ASSESS publishes BEFORE asking re-entry question — user needs Speechify review time
- Design doc follows 4-section structure: **Problem Statement** → **Success Definition** → **Approach** → **Source**
- Meeting agendas follow 5-question structure: **WHY** (Meeting Goal) → **WHAT context** (Pre-Read) → **WHAT unknowns** (Discussion Topics) → **WHAT outcome** (embedded in Goal) → **HOW** (Meeting Format)
- Discussion topics are **statements, not questions** — single "To resolve:" per topic with ⏱️ time estimate
- Escalate to Solution Architect when alignment cannot be achieved after 2 meeting iterations on the same uncertainty

### 9. Documentation

- This Operations Manual (system overview)
- [JA Protocol](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-protocol) (AI persona definition)
- [Design Doc Methodology](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/design-doc-methodology) (stable components + workflow)
- `/probe` command (`~/.claude/commands/probe.md`)
- `/capture` command (`~/.claude/commands/capture.md`)

---

## System 2: Meeting Execution

### 1. Result

To resolve Undefined design doc items through structured stakeholder meetings — producing transcripts that feed the next extraction pass.

### 2. Diagram

```
Meeting Agenda ──→ Meeting ──→ Transcript ──→ Next Extraction Pass
(from System 1)    (live)     (Fireflies)    (back to System 1)
```

### 3. Benchmarks

1. **Meeting agenda exists** from previous extraction pass (System 1 UPDATE produced it)
2. Each discussion topic has a **"To resolve:"** anchor — this is what you came to clarify
3. Open the meeting agenda on screen — share screen with participants
4. Walk through topics **in order**, anchoring on "To resolve:" statements
5. Let stakeholders speak — the agenda is a stimulus for discussion, not an interrogation script
6. Fireflies records the transcript automatically — no manual capture needed
7. After meeting: transcript becomes available → ready for next extraction pass (System 1)

**The meeting agenda is an anchor, not a deliverable.** It's not sent beforehand. It's the JA's preparation — knowing which questions need answers so that when those answers appear in conversation, the Undefined markers can be resolved.

### 4. Accountabilities

- **Junior Architect:** Prepares agenda (via System 1), facilitates discussion using "To resolve:" anchors
- **Solution Architect:** Participates or reviews, provides organizational context
- **Client stakeholders:** Provide domain knowledge that resolves Undefined items

### 5. Timing

- Meetings scheduled as needed when Undefined markers exist in the design doc
- No meeting without an agenda — prevents topic sprawl
- Meeting produces transcript within hours (Fireflies processing time)

### 6. Resources

| Resource | Purpose |
|----------|---------|
| Meeting agenda | Produced by System 1 UPDATE |
| Google Meet / video call | Meeting platform |
| Fireflies | Automatic transcription |
| Screen sharing | Present agenda to participants |

### 7. Quantification

- Discussion topics resolved per meeting (effectiveness)
- Undefined markers remaining after processing the resulting transcript (residual ambiguity)
- Meetings needed per project (cycle count)

### 8. Standards

- **Always bring a prepared meeting agenda** — no "general catch-up" meetings (PA Standard #7)
- Meeting agenda topics are **statements, not questions** — the facilitator decides which questions to ask in-context
- **One "To resolve:" per topic** — single ask, not compound questions
- **Escalate to SA** after 2 meeting iterations on the same uncertainty (PA Standard #6)
- When a meeting resolves topics, **strip the agenda to open items only** — don't preserve history with resolved checkmarks

### 9. Documentation

- This Operations Manual
- [Meeting Agenda Methodology](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/design-doc-methodology) (five-question structure)
- `~/.claude/skills/hippocampus/references/meeting-agenda-workflow.md` (operational reference)

---

## System 3: Epic Creation & Handoff

### 1. Result

To package completed design docs into GitHub epics ready for Developer decomposition — so that the delivery pipeline receives what it needs to begin implementation.

### 2. Diagram

```
Design Complete? ──→ Handoff Ready? ──→ Create Epic ──→ Developer Picks Up
(zero Undefined)     (all context in doc)   (/deliverable-tracking)
```

### 3. Benchmarks

1. **Verify Design Complete gate:** Zero `**Undefined:**` markers remain in the design doc. Test: "Can I write a meeting agenda with open design questions?" → must be NO
2. **Verify Handoff Ready gate:** All context captured within the epic — Developer needs nothing from outside. Check:
   - Problem statement present
   - Technical decisions documented
   - Data schemas included (where applicable)
   - Scope boundaries explicit (in/out)
   - Client agreements referenced
   - Dependencies listed
   - Evaluation criteria defined
   - Source material linked
3. Create epic via `/deliverable-tracking` skill — includes title, labels, parent linking
4. Link design doc in epic body (published URL)
5. Post handoff comment with decomposition approach for the Developer
6. Flag to Solution Architect for review

### 4. Accountabilities

- **Junior Architect:** Creates epic, verifies both gates
- **Solution Architect:** Reviews handoff, confirms epic is Developer-ready

### 5. Timing

When the design doc has zero Undefined markers after the final extraction pass. Epic creation happens in the ASSESS phase of the final pass.

### 6. Resources

| Resource | Purpose |
|----------|---------|
| `/deliverable-tracking` skill | Epic creation with proper formatting |
| GitHub Issues | DaveX2001/deliverable-tracking |
| Completed design doc | Published in hippocampus |

### 7. Quantification

- Extraction passes required from first pass to handoff-ready (efficiency)
- Time from project kickoff to epic creation (throughput)
- Developer questions after handoff (handoff quality — fewer = better)

### 8. Standards

- **Both gates must pass** before epic creation (Design Complete + Handoff Ready)
- Design doc follows **4-section structure:** Problem Statement → Success Definition → Approach → Source
- Scope boundaries (in/out) explicitly documented — what's NOT the problem is as important as what IS
- Source section records all session references for chronological traceability

### 9. Documentation

- This Operations Manual
- [Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-position-agreement-wilsch-ai-services) (Standards section: handoff gates)
- `/deliverable-tracking` skill reference

---

## Tools Quick Reference

| Tool | When | Invocation |
|------|------|------------|
| `/probe` | Start of extraction pass | SCOPE → SURFACE → PROBE |
| `/capture` | After PROBE completes | UPDATE → ASSESS |
| hippocampus | Document creation + publishing | Via `/capture` or directly |
| `sequential_thinking` | Before each PROBE question | MCP tool, automatic |
| `/read-transcript` | Mining transcripts for answers | During PROBE self-routing |
| `conversation-reader` | Reading prior session context | Source investigation |
| `/deliverable-tracking` | Epic + issue creation | System 3 |
| `/issue-comment` | Post standardized issue comments | Session completion |

---

## Logic: Why This Manual Exists

The Junior Architect position works ON the business by extracting knowledge that would otherwise stay locked in transcripts, conversations, and people's heads. Without a documented system, this extraction depends on who does it — their mood, their interpretation, their shortcuts.

With this manual, the system runs the extraction. The operator runs the system. A different operator following these same benchmarks produces the same result: a complete design doc with zero Undefined markers, ready for Developer decomposition.

That's the franchise prototype.

---

## Source

- [EMyth Systems Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-systems-guide) — 9-step system documentation framework
- [MG-0080 Operations Manual Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-operations-manual-guide) — Operations Manual structure
- [JA Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-position-agreement-wilsch-ai-services) — WHAT the JA does (results, work listing, standards)
- [JA Protocol](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-protocol) — WHO the AI is (extraction pass lifecycle, authority model)
- [Junior Architect Position Epic #589](https://github.com/DaveX2001/claude-code-improvements/issues/589) — 34 observations from real extraction passes (behavioral evidence)
- [#852 Archibus Step 1+2](https://github.com/DaveX2001/deliverable-tracking/issues/852) — 5 extraction passes (primary evidence source)
- [#513 AVO Soßen Sourcing](https://github.com/DaveX2001/deliverable-tracking/issues/513) — Design doc + meeting agenda pair (output artifact evidence)
- E-Myth interview session (2026-03-03) — System clustering and operational clarification
