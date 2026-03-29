# Two-Session Model: Decisions Snapshot

[[claude-code-architecture]]

---
publish: true
date: 2026-01-03
source: Rubber-duck session on issue #199
status: SNAPSHOT - Decisions at this point in time, may evolve
---

## Face Announcement

**As of 2026-01-03:** This document captures decisions made during a rubber-duck session analyzing the two-session model for implementation vs verification separation. These decisions represent our current understanding and may evolve as we implement and learn.

**If you're reading this in a future session:** Check issue #199 comments and related PRs for updates. This snapshot is a reference point, not the final word.

**Related documents:**
- [Session Type Model ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/session-type-model-impl-vs-verify) - Original model documentation
- [This Snapshot](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/two-session-model-decisions-snapshot) - Decisions from rubber-duck session

---

## 1. Core Model

### Two Session Types

| Session | Phases | Ends With |
|---------|--------|-----------|
| **Implementation** | Req → Impl → Eval → Execute → Commit | Push → Task Completed |
| **Verification** | ac-verify → PR review | verification.jsonl + merge |

**Key principle:** Same-session bias causes AI to conflate implementation completion with verification. Separation prevents this.

---

## 2. Sanity vs AC Verification

| Type | Question | When | Examples |
|------|----------|------|----------|
| **Sanity** | "Does it exist/run?" | Execute (impl session) | Migration applied, API responds, UI renders |
| **AC** | "Does it behave correctly?" | Separate session | Business logic, edge cases |

**Layer-specific sanity checks:**
- **Database:** Migration applied? Columns exist?
- **Backend:** Endpoint responds 200?
- **Frontend:** Component renders? Build passes?
- **Documentation:** File created in right location?

---

## 3. Defense in Depth

| Layer | What | Catches |
|-------|------|---------|
| 1 | Sanity checks (Execute) | Infrastructure/ops issues |
| 2 | Push (evidence checkpoint) | Creates audit trail |
| 3 | AC-verify (separate session) | Business logic issues |

**Implication:** Push is OK even if implementation has bugs - defense in depth catches issues later.

---

## 4. Behavioral Triggers

**Term choice:** "Behavioral triggers" not "hooks" (hooks is overloaded in Claude Code).

**Pattern:** WHEN/WHY/Behavior - singular purpose each.

### Push → Task Completed
```
WHEN: git push completes successfully
WHY: Creates authoritative evidence checkpoint
BEHAVIOR: Announce "✅ TASK COMPLETED" with commit hash
```

### Sanity Pass → Execute Complete
```
WHEN: All sanity checks pass
WHY: Infrastructure verified, ready for commit
BEHAVIOR: Proceed to Commit phase
```

---

## 5. Task Completed Format

**Minimal - single purpose:**
```
✅ TASK COMPLETED
Commit: [hash]
```

**Purpose:** Proof of work exists (like blockchain checkpoint). NOT about correctness.

**What it excludes:** DoD status, verification status, handoff - these are separate concerns.

---

## 6. DoD vs AC (Parallel Gates)

| Concept | Question | Updated When |
|---------|----------|--------------|
| **DoD** | "Does it exist?" | Impl session (after sanity) |
| **AC** | "Does it behave?" | Verify session (via ac-verify) |

**Key insight:** Parallel gates, not traced to each other. Both must pass independently.

---

## 7. Tester Assignment (Simplified)

| Criterion Type | Tester |
|----------------|--------|
| **Binary** (exists, responds, renders, state) | AI (Chrome DevTools, API, file checks) |
| **Subjective** (looks good, makes sense, is smart) | User |

---

## 8. Protocol Decisions

| Decision | Resolution |
|----------|------------|
| Session type awareness | Protocol stays generic - no distinction |
| Handoff mechanism | User-level via issue comments, not protocol |
| Self-healing authority | Within Execute (details during implementation) |
| Eval-Clarity scope | Expand to include layer/sanity identification |
| Execute ending | Likely "sanity pass = done" (defer to implementation) |

---

## 9. Related Issues

| Issue | Connection |
|-------|------------|
| #199 | This snapshot's source |
| #198 | DoD timing in onboarding |
| #200 | conversation-reader chunking |

---

## 10. Key Quotes

> "When you use the same AI system to both write and review code, you're not getting a second opinion. You're getting confirmation bias at scale."

> "Task Completed is like a blockchain checkpoint - proof that work happened, regardless of correctness."

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

