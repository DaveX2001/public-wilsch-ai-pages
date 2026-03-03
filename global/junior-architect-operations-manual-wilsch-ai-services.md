---
publish: true
---

# Junior Architect Operations Manual — Wilsch AI Services
[[business-identity]]

Operations Manual for the Junior Architect position at Wilsch AI Services. Framework: Michael Gerber, _The E-Myth Revisited_, Chapter 15 (Management Strategy) + E-Myth Operations Manual Guide (MG-0080).

**Position:** Junior Architect
**Reports To:** Solution Architect
**Version:** v1 (Draft — 2026-03-03)

---

## How to Use This Manual

This is your "owner's manual" for the Junior Architect position. It tells you:

- **How your session flows** (Onboard → Probe → Capture → Review)
- **What tools to use** (skills guide you through the methodology)
- **Why things work this way** (logic behind each process)

**Read the referenced skills once** to understand WHY they exist. The skills guide you through the HOW via prompts.

---

## Section 1–3: Company-Wide (TBD)

_To be created: Company Story, Products & Services, Policies_

For now, reference:
- [Strategic Objective](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Organization Chart](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- [Primary Aim](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)

---

## Section 4: Position

[Junior Architect Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-position-agreement-wilsch-ai-services)

---

## Section 5: Systems (JA Workflow)

### Session Start

**Purpose:** Start each session with clear direction — know what to work on and which sources to load.

---

#### Flow

**Step 1: Onboard**

```
/onboarding → select epic issue
```

[`/onboarding` source](https://github.com/MariusWilsch/junior-architect-position-plugin/blob/main/commands/probe.md)

AI loads issue context. You see: issue body, recent comments, project index, worktree state.

**What AI loads:**
- Issue body (What, Why, Closing Criteria)
- Last 20 comments (current state, next step)
- Project index (list of all sources for this epic)
- Tracking file (if exists)

**What you see:** AI suggests a **next step** based on the latest issue comment. Together, these sources tell you where you are:

| Source | What it tells you | How to read it |
|--------|-------------------|----------------|
| **Issue body** | Original requirement | Static context |
| **Comments** | What happened, what's next | Newest = current state |
| **Project index** | Available sources | What you can load |
| **Design doc** | Current state of knowledge | Defined sections vs Undefined markers |

**Step 2: Choose investigation depth**

During onboarding, you pick:

| Option | When to use |
|--------|-------------|
| **Skip** | Context is fresh, want to move fast |
| **Quick** | Need a reminder of where things stand |
| **Full** | Picking up after a while — need to see what's verified vs not |

---

#### Logic

**Why start with onboarding:** The JA position is multi-session work — each extraction pass picks up where the last one left off. Onboarding loads the context so you don't guess. The project index is your map of available sources.

**Why investigation depth matters:** Full investigation compares the design doc state against actual content — useful when there's been a gap between sessions. Skip when you just finished a pass yesterday.

---

### The Extraction Pass

**Purpose:** Transform sources (transcripts, conversations, data artifacts) into design doc sections. Each pass deepens the design doc — writing what can be defined, marking what can't.

**Closing criteria:** Design doc updated, published, reviewed via Speechify. Next pass scope identified.

**One pass per session.** The full cycle completes before the session ends.

---

#### Flow

**Step 1: Scope**

```
/probe
```

[`/probe` source](https://github.com/MariusWilsch/junior-architect-position-plugin/blob/main/commands/probe.md)

AI asks: what part of the design doc are you working on? It reads the project index and presents a source table:

| Source | Type | Rationale |
|--------|------|-----------|
| Pass 3 conversation | Conversation | Most recent context |
| Feb 27 transcript | Transcript | Client meeting after last pass |
| Design doc v3 | Design doc | Current state |

**You see:** A table of sources the AI wants to load, with rationale for each.

**You judge:** Are these the right sources? Add any the AI missed. Confirm before proceeding.

**Why the table:** You know what the AI is working from. You can add context it doesn't have — "also load the email thread from Thomas" or "skip that transcript, it's not relevant."

---

**Step 2: Surface**

AI reads your confirmed sources and produces a numbered uncertainty list:

```
1. Hierarchy Inference
- S1: How does the AI handle ambiguous column names?
- S2: What happens when hierarchy levels are skipped?

2. Column Mapping
- S3: Are suffix variants distinct or grouped?
```

**You see:** Numbered items grouped by scope unit. Items the AI filtered out are shown separately with reasons.

**You judge:** Is this list complete? Are there uncertainties the AI missed? Approve the list before probing starts.

**Key rule:** The AI only lists here — no discussion, no resolution attempts. Just the list.

---

**Step 3: Probe**

AI walks through each item one-by-one. For each item:

1. You see a **context paragraph** — what the source says, what's unclear
2. You see a **question** with 2–3 options + "Next"
3. You answer — pick an option or type your thinking into "Other"
4. AI asks a follow-up: "what would a stranger still not understand?"
5. When you're satisfied, click **"Next"** to advance to the next item

**What it looks like in practice:**

> AI shows you a table or example first, then asks: "Which of these represents your intent?"
> You pick one, or explain in your own words.
> AI rephrases your answer back to check understanding.
> You confirm or correct.
> Click "Next."

**Important:**
- **You own the pace.** AI never advances without your "Next."
- **Visual before abstract.** If a choice requires understanding options, AI shows concrete examples first — then asks.
- **"I don't know" is valid.** Items you can't resolve route to a meeting agenda for the client.
- **~70% of answers come through "Other"** — the preset options frame your thinking, but your own words are usually richer.

---

**Step 4: Capture**

```
/capture
```

[`/capture` source](https://github.com/MariusWilsch/junior-architect-position-plugin/blob/main/commands/capture.md)

AI writes your PROBE resolutions into the design doc. **One section at a time:**

1. AI shows you a diff for ONE section
2. You choose: **Apply** or **Adjust**
3. AI writes that section
4. AI shows you the NEXT section diff
5. Repeat until all resolutions are written

**What you see for each diff:**
- The section as it would read in the published doc
- Voice shifted from your conversation to instructional prose — written for the Developer who implements, the client who aligns, the SA who reviews

**Unresolved items** get inline `**Undefined:**` markers in the design doc. Each marker links to a specific meeting agenda discussion topic. The meeting agenda is created in the same `/capture` session.

| PROBE outcome | What gets written |
|---------------|-------------------|
| Resolved | Design doc section (prose for the reader) |
| Undefined | Marker in design doc + discussion topic in meeting agenda |
| Mixed | Both — design doc first, meeting agenda second |

---

**Step 5: Review**

AI publishes the design doc via hippocampus and shows you the commit link + published URL.

**Your job:** Open the published doc. Read it via Speechify — hearing the full document holistically catches things that in-chat section-by-section review misses.

**After your review:**
- AI proposes the next pass scope using a multi-track format
- You decide: which track next? Or is the design done?
- AI posts an issue comment capturing the re-entry point

```
✅ EXTRACTION PASS COMPLETED

Part: [which part was extracted]
Published: [doc URL]
Next start: [where next session should begin]
```

---

#### Logic

**Why one pass per session:** Each pass is a complete cycle — scope, surface, probe, write, review. Finishing the cycle before ending the session means every session produces a published artifact. No half-finished states.

**Why per-section gate in capture:** Writing all sections at once overwhelms your validation. You can only judge one component at a time — corrections compound when batched. One section, one judgment, one approval.

**Why Speechify review matters:** In-chat, you read section-by-section as it's written. Published, you hear the full artifact holistically. Different medium, different quality of judgment. Things that sounded fine in isolation can feel wrong when heard together.

**Why "Next" is yours:** The AI asks questions to deepen understanding. But only you know when understanding is sufficient. Premature advancement skips context the next person needs. You're the state machine.

**Why voice shift matters:** PROBE captures YOUR thinking. UPDATE rewrites it for the READER — someone who wasn't in the session. If the design doc reads like a conversation transcript, the ambiguity wasn't truly resolved.

**Why 1:1 Undefined→meeting agenda mapping:** When a future AI processes the meeting transcript, the mapping lets it trace each discussed topic back to the specific Undefined marker in the design doc. Without this mapping, connecting transcript content to design doc updates requires guesswork.

---

### Meeting Execution

**Purpose:** Resolve Undefined design doc items through structured stakeholder meetings.

**Closing criteria:** Transcript available for next extraction pass.

---

#### Flow

**Step 1: Prepare**

Your meeting agenda already exists — it was created during the last extraction pass (Step 4: Capture). Each topic has a **"To resolve:"** anchor.

Open the published meeting agenda before the meeting.

---

**Step 2: Run the Meeting**

1. Share your screen with the meeting agenda visible
2. Walk through topics in order
3. Anchor on **"To resolve:"** statements — that's what you came to clarify
4. Let stakeholders speak — the agenda opens space, you don't interrogate
5. Fireflies records everything automatically

**What this looks like:**
- You point to a topic: "This is about how we handle suffix variants in the data."
- You read the "To resolve:" statement: "Whether 00004 and 00004B are the same ingredient in different grades."
- The client explains. You listen. Fireflies captures it.

---

**Step 3: After the Meeting**

Transcript becomes available in Fireflies (usually within hours). Next session: run `/probe` on the new transcript. The extraction pass resolves the Undefined markers using what the client said.

---

#### Logic

**Why the agenda isn't sent beforehand:** The meeting agenda is YOUR preparation tool, not a deliverable for the client. Its value is that you know which "To resolve:" items need answers. When those answers surface in conversation, you recognize them. The client doesn't need to pre-read your internal uncertainty list.

**Why statements, not questions:** Questions lead participants toward a specific frame, narrowing the answer space. Statements present the uncertainty and let the facilitator decide in-context which question to ask. The agenda is a trigger, not an interrogation script.

**Why always bring an agenda:** No "general catch-up" meetings. Every meeting targets specific unknowns. If you can't write an agenda, you don't need a meeting — you need another extraction pass.

**Why escalate after 2 iterations:** If the same uncertainty survives two meetings, it's not a knowledge gap — it's an alignment problem. Escalate to Solution Architect.

---

### Epic Creation & Handoff

**Purpose:** Package a completed design doc into a GitHub epic ready for Developer decomposition.

**Closing criteria:** Epic created, SA has reviewed, Developer can start.

---

#### Flow

**Step 1: Verify Design Complete**

Ask yourself: "Can I write a meeting agenda with open design questions?"

- **YES** → You're not done. More extraction passes or meetings needed.
- **NO** → Design is complete. All sections are Defined. Proceed.

Check the design doc for any remaining `**Undefined:**` markers. Zero markers = gate passed.

---

**Step 2: Verify Handoff Ready**

Ask yourself: "Can a Developer start from this epic without asking me anything?"

Check:
- Problem statement present
- Technical decisions documented
- Data schemas included (where applicable)
- Scope boundaries explicit (in/out)
- Client agreements referenced
- Dependencies listed
- Evaluation criteria defined
- Source material linked

---

**Step 3: Create Epic**

```
/deliverable-tracking
```

AI creates the epic in `DaveX2001/deliverable-tracking` with proper title, labels, and parent linking. The design doc URL is linked in the epic body.

---

**Step 4: Handoff**

Post a comment on the epic with the decomposition approach — how you'd suggest the Developer breaks this into sub-issues.

Flag to Solution Architect for review. SA confirms the epic is Developer-ready.

---

#### Logic

**Why two gates:** Design Complete and Handoff Ready are different things. A design doc can have zero Undefined markers (Design Complete ✓) but still be missing context that the Developer needs (Handoff Ready ✗). The Developer shouldn't have to read between the lines.

**Why scope boundaries matter:** "What's NOT the problem" is as important as "what IS." Without explicit boundaries, the Developer may build features you intentionally excluded — or a client may expect something you intentionally deferred.

**Why SA reviews:** The SA has the organizational context to judge whether the epic fits the delivery pipeline. The JA has the design depth. Both perspectives needed before the Developer starts.

---

### End of Session

**Purpose:** Leave a clean handoff so the next session (yours or someone else's) can pick up without guessing.

**Ritual:**

1. **For completed extraction passes:**
   - AI announces `✅ EXTRACTION PASS COMPLETED` with published URL and next scope
   - Run `/issue-comment` to formalize re-entry on the issue

2. **For interrupted sessions (80% context):**
   - Finish current stage (don't leave mid-PROBE)
   - Run `/capture` with what you have → publish
   - Run `/issue-comment` noting where to resume

---

## Reference Documents

| Document | Purpose |
|----------|---------|
| [JA Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-position-agreement-wilsch-ai-services) | What the JA is accountable for |
| [JA Protocol](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-protocol) | AI persona — how the AI behaves in JA sessions |
| [Design Doc Methodology](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/design-doc-methodology) | Design doc stable components + workflow |
| [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) | Master routing — which path, which stage |

### Skills and Commands (Source: [junior-architect-position-plugin](https://github.com/MariusWilsch/junior-architect-position-plugin))

| Tool | Type | Used in |
|------|------|---------|
| [`/probe`](https://github.com/MariusWilsch/junior-architect-position-plugin/blob/main/commands/probe.md) | User-invoked command | Extraction Pass Steps 1–3 |
| [`/capture`](https://github.com/MariusWilsch/junior-architect-position-plugin/blob/main/commands/capture.md) | User-invoked command | Extraction Pass Steps 4–5 |
| [`/onboarding`](https://github.com/MariusWilsch/junior-architect-position-plugin/blob/main/commands/probe.md) | User-invoked command | Session Start |
| [`/issue-comment`](https://github.com/MariusWilsch/junior-architect-position-plugin/blob/main/commands/probe.md) | User-invoked command | End of Session |
| [`/deliverable-tracking`](https://github.com/MariusWilsch/junior-architect-position-plugin/blob/main/commands/probe.md) | User-invoked command | Epic Creation |
| [hippocampus](https://github.com/MariusWilsch/junior-architect-position-plugin) | AI-invoked skill | Document creation + publishing |
| [conversation-reader](https://github.com/MariusWilsch/junior-architect-position-plugin) | AI-invoked skill | Prior session context |
| [read-transcript](https://github.com/MariusWilsch/junior-architect-position-plugin) | AI-invoked skill | Transcript mining |

---

## Gaps to Fill (Future Sessions)

### Process Gaps
- [ ] **Blocked/Stuck:** Escalation path when source material is insufficient
- [ ] **Project index maintenance:** How to keep the artifact index current as passes produce new docs
- [ ] **Multi-epic projects:** How extraction passes work when a project spans multiple epics

### Skill Gaps
- [ ] **Epic creation integration:** Automate the two-gate verification in `/deliverable-tracking`

---

## Source

- [EMyth Systems Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-systems-guide) — 9-step system documentation framework
- [MG-0080 Operations Manual Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/emyth-chapters/emyth-operations-manual-guide) — Operations Manual structure
- [JA Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-position-agreement-wilsch-ai-services) — WHAT the JA does
- [JA Protocol](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-protocol) — WHO the AI is
- [Junior Architect Position Epic #589](https://github.com/DaveX2001/claude-code-improvements/issues/589) — 34 observations from real extraction passes
- [#852 Archibus Step 1+2](https://github.com/DaveX2001/deliverable-tracking/issues/852) — 5 extraction passes (primary evidence)
- [#513 AVO Soßen Sourcing](https://github.com/DaveX2001/deliverable-tracking/issues/513) — Design doc + meeting agenda pair
- E-Myth interview session (2026-03-03) — System clustering and voice correction
