---
publish: true
date: 2026-01-07
status: DESIGN-DRAFT - Needs DoD/AC in tracking.md during Design phase
source: Rubber-duck session extending two-session model
---

# Three-Session Model: Design / Implementation / Verification

[[claude-code-architecture]]

## Problem Statement

The two-session model (Implementation → Verification) prevents same-session bias for verification. But there's another bias: **Design context can anchor Implementation**.

When Claude has all the design thinking in context, it might:
- Over-engineer based on design discussion
- Miss simpler solutions that emerge during execution
- Be anchored to the design approach instead of adapting

**Solution:** Separate Design from Implementation, just like we separate Implementation from Verification.

## The Three-Session Model

| Session | Type | What Happens | Output |
|---------|------|--------------|--------|
| **Design** | Human-guided | rubber-duck + ac-create | tracking.md (DoD + AC) |
| **Implementation** | Autonomous | req-clarity → impl-clarity → eval-clarity → execute | Code + DoD checkboxes |
| **Verification** | Autonomous | ac-verify + PR review | verification.jsonl |

**Key principle:** Fresh context between sessions prevents bias AND maximizes context budget.

## Why Three Sessions?

### Bias Prevention (Extended)

| Bias | Source → Target | Problem |
|------|-----------------|---------|
| Confirmation | Implementation → Verification | Verifying own work |
| **Anchoring** | Design → Implementation | Over-committed to design approach |

### Context Budget

| Scenario | Problem |
|----------|---------|
| Design + Implementation in one session | 30-40% context left after design - not enough for course correction |
| Separate sessions | Each starts fresh (100%) |

### Artifacts as Memory

| Session | Reads | Writes |
|---------|-------|--------|
| Design | Issue body | tracking.md |
| Implementation | tracking.md | Code, DoD checkboxes |
| Verification | tracking.md, Code | verification.jsonl |

Implementation reads the ARTIFACT (tracking.md), not the DISCUSSION that created it. No anchoring.

## Session Details

### Design Session (Human-Guided)

**Purpose:** Think through the problem, define what "done" and "correct" look like.

**Tools:** rubber-duck, ac-create, hippocampus

**Flow:**
1. rubber-duck: Explore the problem, clarify approach
2. ac-create: Define acceptance criteria (Given-When-Then)
3. Save to tracking.md

**Ends when:** tracking.md has DoD + AC defined

**Human role:** Active thinking partner, makes design decisions

### Implementation Session (Autonomous)

**Purpose:** Build what was designed.

**Tools:** onboarding → req-clarity → impl-clarity → eval-clarity → execute → commit

**Flow:**
1. onboarding: Read tracking.md, understand DoD + AC
2. Clarity phases: Resolve any technical ambiguities
3. Execute: Build the thing
4. Commit: Push code

**Ends when:** Code pushed, DoD checkboxes updated

**Human role:** Notify if blocked, otherwise autonomous

### Verification Session (Autonomous)

**Purpose:** Verify the implementation meets AC.

**Tools:** ac-verify, Chrome DevTools, PR review

**Flow:**
1. ac-verify: Execute Given-When-Then against running system
2. Log decision traces to verification.jsonl
3. PR review: Check code quality

**Ends when:** verification.jsonl complete, PR approved or issues filed

**Human role:** Notify if blocked, otherwise autonomous (can run in tmux)

## Session Switching

Sessions aren't strictly linear. Can switch back to Design if:
- Implementation reveals design flaws
- New requirements emerge
- Approach proves wrong

**Pattern:** Design ↔ Implementation → Verification

## Artifacts Summary

| Artifact | Created In | Purpose |
|----------|------------|---------|
| tracking.md | Design | DoD + AC specification |
| Code | Implementation | The deliverable |
| verification.jsonl | Verification | Decision traces as precedent |

## Connection to Context Graph

Sessions log decisions to Langfuse for queryable precedent:
- Design decisions: Why this approach?
- Implementation decisions: Why this pattern?
- Verification traces: What was tested, how?

See: [context-graph-langfuse.md](context-graph-langfuse.md)

## Related Documents

- [session-type-model-impl-vs-verify.md](session-type-model-impl-vs-verify.md) - Original two-session model
- [two-session-model-decisions-snapshot.md](two-session-model-decisions-snapshot.md) - Decisions from #199
- [ac-dod-framework.md](ac-dod-framework.md) - AC/DoD methodology
- [ac-verification-decision-trace.md](ac-verification-decision-trace.md) - Verification schema

## Open Questions

- [ ] How to handle session switching gracefully?
- [ ] What triggers "notify human if blocked"?
- [ ] How to parallelize Design + Implementation with tmux?
- [ ] Should Context Graph log all three session types?

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

