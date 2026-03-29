---
publish: true
date: 2026-01-13
status: PLAN - Ready for execution
---

# Mohamed Onboarding Plan

[[team-scaling]]

## Overview

Onboarding plan for Mohamed (RocketDevs developer) to join as execution capacity using the clarity workflow system.

## Success Criteria (Day 14)

| Criterion | Description |
|-----------|-------------|
| Uses clarity workflow | Implements issues end-to-end using the system |
| Human attention at 2 points | Initial scope + final review only |
| System validation | Proves workflow scales to another executor |

## Onboarding Steps

### Step 1: Grooming Exposure

**Goal:** Integrate Mohamed into daily operations

**Actions:**
- Send grooming doc from deliverable-tracking repo
- Add to daily grooming calendar invite
- He learns the rhythm by participating

**Four Questions (per item):**
1. Assignee correct?
2. Blocked or ready?
3. Context clear?
4. Maker or manager task?

### Step 2: System Install

**Access Required:**

| System | Level | Notes |
|--------|-------|-------|
| GitHub | Full | deliverable-tracking + project repos |
| Supabase | Full | Project databases |
| SSH | Full | WILSCH-AI-SERVER, etc. |
| Claude Code | 1 seat | Uses one of 4 available seats |

**Plugin Contents:**

Skills:
- onboarding
- worktree
- requirements-clarity
- implementation-clarity
- evaluation-clarity
- issue-comment
- rubber-duck
- flag-for-improvement
- LSP (pyright/typescript)

Dependencies:
- ~/.claude/lib/ scripts
- ~/.claude/CLAUDE.md (global protocol)

### Step 3: Explain Once (Live Call, Recorded)

**Format:** Live screen share, recorded for rewatching

#### Part 1: Concepts (~45 min)

| Topic | Source | Purpose |
|-------|--------|---------|
| Navigator/driver | CLAUDE.md global protocol | Core mental model |
| Clarity workflow | CLAUDE.md global protocol | How work flows |
| Flag-for-improvement | [hippocampus](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) | Escape hatch |
| All skills | Skills list above | Available commands |
| AC/DoD | [hippocampus](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) | Success structure |

#### Part 2: Walkthrough (~45-90 min)

Using issue #508 (Paul: Per-Template Assets) as example:

| Step | What Happens |
|------|--------------|
| Show tracking.md | See DoD/AC structure |
| Run /onboarding | Start session, link issue |
| Clarity phases | Walk through AI questions |
| Execute | Watch code generation |
| Commit | Task completion |

### Step 4: Trial by Fire

**Support Model:**
- First 1-2 weeks: High availability for blockers
- Mohamed can call anytime (even during maker time)
- Goal: Resolve blockers quickly at the beginning
- Better to interrupt than be stuck

**Escape Hatch:**
- When system breaks: `/flag-for-improvement "what went wrong"`
- Move on, Marius fixes later
- Creates virtuous improvement cycle

## The Dream State

```
MARIUS (Designer)           MOHAMED (Executor)
    |                            |
    v                            v
Design issues         ->   Implementation (clarity workflow)
                           |
                          Verification
                           |
    <----------------------+
Final review
```

**Human attention only at:**
1. Initial scope - design the issue
2. Final review - test completed work

## Related Documents

- [Three-Session Model](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model)
- [Ralph Loop Integration](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ralph-loop-integration)
- [AC/DoD Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework)
- [Improve System Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

