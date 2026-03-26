---
publish: true
---

# General Decomposition Design
[[general-decomposition]]

Methodology for decomposing design docs into implementation issues. Codifies trunk-first decomposition, spike-or-issues gates, and proof points — derived from IITR DS-Kit Navigation and Archibus Bulk-Import Pipeline post-mortems (March 2026).

---

## Problem Statement

The JA→SA→Decompose pipeline produces thorough design docs, careful SA reviews, and well-structured issue decompositions. Each step works correctly in isolation. The gap is between "design approved" and "fully decomposed" — no mechanism validates that core assumptions actually work before building an entire issue tree on top of them.

The result: approved-but-invalid issues. The design doc passes review, 4–6 issues are created with ACs and dependencies, and days into implementation a foundation assumption collapses. The remaining issues become waste — approved, designed, but impossible to implement as written.

**IITR evidence:** PageIndex OSS was adopted from research during JA extraction passes, survived two SA reviews (both focused on doc quality, not technical validation), and became the foundation for 6 decomposed issues (#1111–#1116). Three days into implementation, PageIndex OSS shipped tree generation only — retrieval was commercial API. All 6 issues became partially or fully invalid. ([Post-Mortem](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-post-mortem))

**Archibus evidence:** The bulk-import pipeline was built and witnessed in 3 days. Pipeline logic was correct. But "demo-ready" required infrastructure that wasn't in the original acceptance surface — subdomain routing, deployment identity, MCP discovery, reference data quality. Each witness ceremony surfaced 3–5 new findings. Separately, bridge tool #1154 had a full AC lifecycle (5 ACs, SA review, redesign) before Spike A validated what the runtime could actually do — invalidating the ACs before implementation started. ([Post-Mortem](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/archibus-post-mortem))

**This is NOT:**
- A failure of individual pipeline components (JA extraction, SA review, decomposition quality)
- A problem with design doc thoroughness
- Specific to any one project or technology

**Preconditions:**
- JA→SA→Decompose pipeline has no validation step between "design approved" and "fully decomposed"
- Workflow default naturally produces full decomposition (all issues at once)
- SA reviews create high confidence in the design doc — "approved" feels like "validated"
- Issues are progressively more expensive: design is cheap, AC creation costs more, implementation costs most

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Decomposition methodology is codified — trunk-first, spike-or-issues gate, proof points, design doc update rules — and calibrated through real project application |
| **Success** | David applies trunk-first decomposition to the next project (IITR Vector B restart or Archibus bridge). The pattern prevents approved-but-invalid issues. Observations collected on the position epic validate or refine the methodology. |
| **Done test** | Next decomposition produces 2–3 issues to the first proof point (not 6). A spike-or-issues decision is made explicitly at the proof point. No stale issues accumulate on the board. |

---

## Approach

Four parts. Each builds on the previous. Parts 1–2 define the decomposition lifecycle. Parts 3–4 define supporting rules that prevent the failure patterns.

### Part 1: Trunk-First Decomposition

Decompose the full issue tree mentally — all dependencies, all vertical slices. Then only CREATE issues up to the first proof point. Maximum 2–3 issues.

The proof point is context-dependent:

| Proof Point Type | Example | What It Validates |
|-----------------|---------|-------------------|
| **Technical assumption** | "Does PageIndex OSS support retrieval?" | Core technology actually works |
| **Data dependency** | "Does the client's Excel match our schema?" | Input data is usable |
| **Client decision** | "Will they accept vector RAG over tree-based?" | Direction is approved |
| **Runtime capability** | "Can LibreChat execute FastMCP tools?" | Target environment works |

The Developer identifies the biggest unknown in the pipeline during decomposition and draws the trunk boundary there. This is a Developer concern, not a JA concern — the JA designs from sources and doesn't have the dependency-tree context to determine which assumption blocks the most downstream work. The Developer, creating the issue dependency tree, sees which issue failure would make everything downstream stale. Everything after the proof point stays in the design doc — not on the board.

**After the proof point passes:** decompose the next 2–3 issues. Repeat until the full pipeline is implemented. Each cycle is: decompose → validate → decompose further.

**After the proof point fails:** close the 2–3 trunk issues. Pivot the design doc. The blast radius is 2–3 issues with minimal AC investment — not 6 issues with full ACs, SA reviews, and dependencies.

**IITR evidence:** 6 issues (#1111–#1116) decomposed at once. When #1112's foundation collapsed (PageIndex OSS limitation), #1113–#1116 became approved-but-invalid. With trunk-first, only #1111–#1112 would have been created. Blast radius: 2 issues, not 6.

**Archibus evidence:** 4 pipeline issues (#1055–#1064) decomposed at once. When witness ceremonies revealed the acceptance surface was larger than scoped, all 4 were affected by scope expansion. Post-spike, SA creates fresh implementation issues with runtime constraints already validated.

**Post proof point on the epic:** Leave a comment on the epic issue saying "this is the proof point" — makes the decision visible for future reference.

### Part 2: Spike-or-Issues Gate

At the proof point, a decision: proceed with implementation issues or spike first.

**The question:** "Do you feel confident to proceed, or should we spike first?"

This is a human decision, not an AI gate. The AI can suggest proof points and flag uncertainty, but cannot know whether something actually works — it starts at 0% every session. The human decides based on experience and risk tolerance.

**When to spike:**
- First time using a technology in the pipeline (PageIndex, LibreChat + FastMCP)
- Runtime behavior that can't be validated through research alone
- Integration points between systems that haven't been tested together

**When NOT to spike:**
- Clear, low-ambiguity decompositions (e.g., ROHDEX CI/CD — well-understood infrastructure)
- Technology already validated in a prior project
- All components individually proven, integration is straightforward

**Spike format:**
- A spike IS a running system — witnessable by Dev Lead, not just research notes
- A spike replaces the first implementation issue when uncertainty exists — not an additional issue alongside it
- Spike deliverable: knowledge or a running environment. Not screenshots.
- Label: `maker/spike` (no ACs required — the spike validates whether ACs can be written)

**Spike board flow:**
- Single-cycle label — no spec/implement flip (unlike dev/design → dev/implement)
- Board path: Backlog → Working → Review (spike witness) → Done
- Spike witness tests **primitives**, not ACs — "does the runtime support this capability?"
- Verdict output: a proven-vs-assumed map. What the spike validated vs. what's still assumed.
- This map feeds directly into the Developer's next decomposition — ACs can only be written for capabilities the spike proved.

**Archibus evidence (#1167):** Dev Lead witnessed 6 primitives (skill discovery, skill loading, file write, discovery+chaining, field editing, session isolation). All 6 passed. The `edit_file` capability was discovered mid-ceremony — invalidating a prior assumption that surgical editing wasn't available. Proven vs. assumed separation prevented false confidence in capabilities the spike didn't test.

**Lifecycle:** Design doc approved → Developer decomposes mentally → identifies proof point → spike-or-issues decision → if spike: create spike issue, validate, then decompose further with validated constraints → if confident: create trunk issues, implement to proof point.

**Archibus evidence:** Spike A (#1167) validated LibreChat + FastMCP runtime in one day. Caught #1154 AC staleness before implementation. The spike primitive was invented from the IITR post-mortem during the Archibus timeline — and immediately proved its value.

**IITR evidence (counterfactual):** A spike on "can PageIndex OSS do retrieval?" would have taken hours and saved 3 days of building on a broken foundation. The IITR post-mortem invented the spike primitive; Archibus validated it.

**Spike issue template.** Undefined markers in design docs become spike issues when ready for validation. The format (established across #1220, #1222, #1229, #1250):

**What / Why.** Frame the spike's purpose and the risk it mitigates. A spike exists because an assumption blocks decomposition — the What names the assumption, the Why explains what breaks if it's wrong.

**Spike Primitives.** Numbered table of independently testable claims. Each primitive isolates one variable — if the spike fails, the primitive table tells you which layer failed. Order primitives so each builds on the previous (P1 must pass before P2 is meaningful).

**Proof Point.** The binary comparison or question the spike answers. Usually "does X produce the same result as Y?" or "does X work at all?" The proof point is what makes the spike pass or fail — without it, the spike is open-ended research, not validation.

**Proven-vs-Assumed Map.** Output template with Status and Evidence columns, filled in during execution. Before the spike, claims are "Assumed" (empty evidence). After, each is "Proven" or "Failed" with a specific artifact. This is the spike's deliverable — the map travels back to the design doc or decomposition.

**After Spike.** Routing logic for passes and fails. Passes unlock the next decomposition trunk. Fails identify which primitive broke and what the fallback is. Both outcomes are valuable — a failed spike prevents wasted implementation effort.

```
## What?
{Assumption that blocks decomposition}

## Why?
{What breaks if the assumption is wrong}

## Spike Primitives

| # | Primitive | What's tested |
|---|-----------|--------------|
| 1 | {layer 1} | {isolated variable} |
| 2 | {layer 2} | {builds on P1} |

## Proof Point
{Binary question — pass/fail}

## Proven-vs-Assumed Map

| Claim | Status | Evidence |
|-------|--------|----------|
| {claim 1} | Assumed | |
| {claim 2} | Assumed | |

## After Spike
**Passes:** {next trunk / decomposition}
**Fails:** {which primitive, fallback path}
```

### Part 3: Design Doc Update Rules

Design docs are updated only after proof points — not after every implementation session.

**The problem:** Updating the design doc with unvalidated designs creates a document that looks authoritative but contains assumptions. When the assumption fails, the doc must be rewritten — and anything decomposed from the stale doc inherits the error.

**The rule:** Proof first, then document. When staging config works, when a spike passes, when a runtime validates — that's when the design doc gets updated. The doc should only contain what IS proven, not what MIGHT work.

**IITR evidence:** PageIndex entered the design doc as the core retrieval mechanism during JA extraction passes. It survived two SA reviews and looked authoritative. When the OSS limitation surfaced, the entire approach section had to be rewritten and Track B added.

**Archibus evidence:** Bridge tool #1154 went through a full design cycle before runtime validation. Post-spike, the design doc was updated with validated LibreChat constraints — only then could meaningful ACs be written.

**CCI parallel:** Same pattern observed in CCI — behaviors were documented in design docs before proving they worked. SA stopped doing this: "erst gesagt okay, ich proof erst, dass das Behavior funktioniert und dann packe ich ein Design doch."

### Part 4: Config Persistence

When staging config works, write it to CLAUDE.md or compose immediately — not after reaching stability, during the session that achieves stability.

**What goes in CLAUDE.md:** Not HOW things work (that's in the code), but WHERE to find how things work. Progressive disclosure — pointers to the right files, not duplication of their content. For frequently-used infrastructure (test harness, deployment targets), a CLAUDE.md entry prevents rediscovery every session.

**What goes in compose/.env:** Actual config values — API endpoints, model names, port mappings. These are the values that die between sessions when only stored in issue comments.

**IITR evidence:** Test harness failed 3 times across sessions 167–170: stale API key (→401), wrong model name (`qwen3.5-32k:latest` doesn't exist in Ollama), wrong API parameter (`body["think"]=False` is Ollama-only, OpenRouter needs `reasoning_effort: "none"`). Each failure required rediscovery. Once stable config was reached, it was documented — but the cost was 3 sessions of rediscovery.

**Archibus evidence:** F9 (missing deployment identity in CLAUDE.md) and F11 (test data missing from rebuilt container) — both instances of config knowledge dying between sessions. Each witness ceremony that surfaced these could have been avoided by persisting working config during the implementation session.

### Part 5: Position Accountability

Decomposition touches multiple positions in the VP/Delivery path. Each position has distinct context that determines their role.

| Position | Decomposition Role | Context Owned |
|----------|-------------------|---------------|
| **Developer** | Full mental decomposition → trunk boundary identification → issue creation | Issue dependency tree — sees which assumption blocks the most downstream work |
| **JA** | Design doc extraction. Recognizes "needs empirical validation" during extraction | Source material — transcripts, conversations, data artifacts |
| **Dev Lead** | Witnesses spikes via spike-specific ceremony (primitives, proven-vs-assumed) | Review queue — quality gate authority |

**Developer as decomposer:** The Developer reads the design doc and decomposes — full mental issue tree, then identifies the trunk boundary. This is operationalized as a skill (similar to AC-create): the skill guides the Developer through decomposition and trunk boundary identification, not as a belief to internalize from reading a design doc.

**JA spike recognition:** During extraction passes, the JA encounters moments where design reaches an empirical ceiling — "this can only be proven by building/testing it." Two paths:

- **Validation** (small, in-session): the JA can spike immediately. Install a tool, run a command, check a web resource. Write the result into the design doc as proven fact. Example: validating MiniMax M2.5 memory fit on DGX Spark via web search, or installing Tailscale to confirm persistent SSH access.
- **Construction** (large, needs Developer): the JA marks it for the Developer. Building a test pipeline, reading source code to validate a library's capabilities, or running integration tests. Example: "Does PageIndex OSS support retrieval?" — requires inspecting the codebase, not just reading documentation.

The threshold is validation vs. construction: quick checks that answer yes/no stay in the JA pass. Building something to test it goes to the Developer.

**Undefined:** VP/Delivery and SA roles in decomposition — VP/Delivery creates epics and routes to JA or Developer, SA reviews design doc quality. Their specific accountability in the trunk-first flow (proof point decisions, design doc update authority) is not yet defined.

**Undefined:** Decomposition skill mechanics — the exact process by which the skill guides the Developer through full decomposition → trunk boundary identification. Pending empirical evidence from first skill application.

**Undefined:** Spike witness ceremony format — the detailed structure of how the Dev Lead witnesses a spike (which primitives to test, success criteria table structure, proven-vs-assumed map format). Evidence from Archibus #1167 is sufficient for the Developer to work from; formal design deferred.

**Undefined:** Proof point ownership split — the Developer defines WHAT the proof point is and WHAT primitives are being tested (the success criteria — this is the spike equivalent of tracking.md). The Dev Lead defines the gemba (HOW to witness it) just-in-time during Phase 3 of the witness ceremony — witness methodology is a Dev Lead competency, not something the Developer specifies upfront. In #1167, the Developer's tracking.md only contained pass/fail — Finding #1 called this out: "No documentation of tool evaluation." The Developer should have defined the proof point and primitives; the Dev Lead would then determine how to witness them. Evidence: [#1167 Finding 1](https://github.com/DaveX2001/deliverable-tracking/issues/1167#issuecomment-4079492662), CCI #605 and #648 observations on proof point witnessing and question-level attribution.

---

## Source

**Post-Mortems:**
- [IITR Post-Mortem](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-post-mortem) — root cause chain, 5 process failures, corrective actions
- [Archibus Post-Mortem](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/archibus-post-mortem) — scope expansion, IITR correctives validated

**Design Doc Hubs:**
- [#1093](https://github.com/DaveX2001/deliverable-tracking/issues/1093) — IITR DS-Kit Navigation (78 comments, full extraction pass history)
- [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852) — Archibus Bulk-Import Pipeline

**CCI:**
- [#620](https://github.com/DaveX2001/claude-code-improvements/issues/620) — Decomposition Skill (merge target for position epic)
- [#605](https://github.com/DaveX2001/claude-code-improvements/issues/605) — Dev Lead Position Epic (spike witness observations)

**Transcripts:**
- [Grooming 2026-03-17](https://app.fireflies.ai/view/01KKXKGJB20Q1K0XDHXMH77BVM) — IITR post-mortem discussion
- [Grooming 2026-03-18](https://app.fireflies.ai/view/01KM0732PDYDAT62D5DFDAHV01) — trunk-first, spike-or-issues, proof points
- [Grooming 2026-03-18 (second)](https://app.fireflies.ai/view/01KM0CAF30PJ3V27S2RDB15BHQ) — IITR/Archibus cleanup, config persistence, commitment board

**Issue:**
- [#1181](https://github.com/DaveX2001/deliverable-tracking/issues/1181) — Project Post-Mortem → General Decomposition Design Doc

**Position Epic:**
- [#657](https://github.com/DaveX2001/claude-code-improvements/issues/657) — Trunk-First Decomposition (observations + live application evidence)
- [#605](https://github.com/DaveX2001/claude-code-improvements/issues/605) — Dev Lead Position Epic (spike witness observations)

**Evidence Sessions (position accountability extraction):**
- c4c32879 — CCI Board Structure extraction (Session C closure, spike signal during JA pass)
- e489c708 — DGX Spark infrastructure extraction (Tailscale spike mid-JA-pass, validation vs. construction)
- 91c2aad3 — Archibus spike witness ceremony (#1167, 6 primitives, proven-vs-assumed)

**Sessions:**
- /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/367cbb0b-aaba-4bc1-8ae3-521edf7d3b55.jsonl
- /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/e75e684b-1190-4897-9b27-90939fb9f5b2.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

