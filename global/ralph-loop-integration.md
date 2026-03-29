---
publish: true
date: 2026-01-12
status: HYPOTHESIS - Ideas to verify through implementation
source: Rubber-duck session exploring Ralph integration
---

# Ralph Loop Integration: Ideas & Hypotheses

[[ralph-integration]]

## Overview

Exploring how Ralph autonomous coding loop integrates with three-session model.

## Source References

- [Medium: Claude Code + Ralph](https://medium.com/coding-nexus/claude-code-ralph-how-i-built-an-ai-that-ships-production-code-while-i-sleep-b59c9e1e7f8a)
- [Playbook: ghuntley/how-to-ralph-wiggum](https://github.com/ghuntley/how-to-ralph-wiggum)
- [Anthropic: Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

## Concept Mapping

| Ralph Concept | Three-Session Equivalent | Notes |
|---------------|-------------------------|-------|
| loop.sh | Outer orchestration | Mode-agnostic |
| prompt.md | Session behavior | One per mode |
| prd.json | tracking.md | DoD + AC |
| progress.txt | AGENTS.md learnings | Combined with ops |

## Hypotheses (To Verify)

### H1: Three prompts = Three sessions

**IF:** We implement the three-session model using three prompt modes
**BY:** Creating prompt_design.md (interactive), prompt_impl.md (autonomous), prompt_verify.md (autonomous)
**WILL:** Enable separation of concerns while using a single loop mechanism
**BECAUSE:** Each session type has different behaviors - design needs human input, impl/verify can be autonomous

**Prompt modes:**
- prompt_design.md (interactive) - creates sub-issues with DoD + AC
- prompt_impl.md (autonomous) - implements with sanity backpressure
- prompt_verify.md (autonomous) - runs ACs, writes verification.jsonl

Loop stays the same. Prompts define behavior.

### H2: Orchestration evolution

**IF:** We evolve from manual mode switching to auto-chaining
**BY:** Adding logic to detect mode completion and automatically switch to next mode
**WILL:** Reduce human intervention from "start each session" to "create parent issue"
**BECAUSE:** The loop can determine next step based on state (all DoD done → verify, verify fail → impl)

**Current:** Manual mode switching. Human runs `./loop.sh impl`, monitors, then runs `./loop.sh verify`.

**Future:** Auto-chaining. Loop detects impl complete → switches to verify → if fail → back to impl. Three sessions, one orchestration layer.

### H3: Sanity checks = Ralph's backpressure

**IF:** We preserve Ralph's backpressure mechanism in prompt_impl.md
**BY:** Adding sanity checks (migration applied? API responds? UI renders?) before marking DoD items complete
**WILL:** Catch infrastructure issues during implementation, not after
**BECAUSE:** Ralph's core insight is that immediate feedback loops prevent compounding errors across iterations

**Layer-specific sanity checks:**
- Database: Migration applied? Columns exist?
- Backend: Endpoint responds? No crashes?
- Frontend: Component renders? Build passes?

This is NOT an extension - it's Ralph's core. We preserve it in prompt_impl.md.

### H4: AC verification as separate session

**IF:** We run AC verification in a separate session from implementation
**BY:** Adding prompt_verify.md that runs Given-When-Then checks against live system
**WILL:** Prevent same-session confirmation bias
**BECAUSE:** The AI that built it cannot objectively verify it - fresh context enables genuine validation

**Implementation:**
- Ralph relies on tests during build only
- We extend with prompt_verify.md for Given-When-Then verification
- Results go to verification.jsonl

This IS an extension of Ralph - adding the three-session model's bias prevention.

### H5: tracking.md combines prd.json + specs

**IF:** We combine Ralph's multiple artifacts into a single tracking.md
**BY:** Using DoD section for task list and AC section for specifications
**WILL:** Reduce file management overhead while preserving information locality
**BECAUSE:** Ralph reads context at each iteration - fewer files = less context pollution

**Mapping:**
- DoD section = task list (Ralph's prd.json)
- AC section = specifications (Ralph's specs/*.md)

More efficient than multiple files. Same information, better locality.

### H6: Parent issue as scope container

**IF:** We use parent issues to define scope before Ralph runs
**BY:** Human creates parent with phases, Ralph creates sub-issues from parent
**WILL:** Eliminate "design from zero" ambiguity
**BECAUSE:** Anthropic's insight - initializer sets comprehensive scope, coding agent works incrementally

**Pattern:**
- Parent provides 80% clarity on scope
- Design mode creates sub-issues FROM parent phases
- Matches Anthropic's initializer pattern

## Future End State

If all hypotheses prove true, the end state is a **queue model** where human and Ralph work in parallel:

```
┌─────────────────────────────────────────────────────────────┐
│                    HUMAN (Issue Designer)                    │
│                                                             │
│   Creates parent issues with phases                         │
│   Queues them up continuously                               │
│   Doesn't wait - continues designing next project           │
│                                                             │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
              ┌───────────────────────┐
              │      WORK QUEUE       │
              │                       │
              │  Parent #482 ← doing  │
              │  Parent #500 ← next   │
              │  Parent #501 ← queued │
              │                       │
              └───────────┬───────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                 RALPH LOOP (Execution Engine)                │
│                                                             │
│   Picks parent from queue                                   │
│   For each phase: design → impl → verify                    │
│   Auto-chains between modes                                 │
│   Moves to next parent when done                            │
│                                                             │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐                 │
│  │ design  │───▶│  impl   │───▶│ verify  │──┐              │
│  └─────────┘    └─────────┘    └─────────┘  │              │
│       ▲                                      │              │
│       └──────── next phase / fix ────────────┘              │
│                                                             │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
              ┌───────────────────────┐
              │    READY FOR REVIEW   │
              │                       │
              │  Completed projects   │
              │  Human tests when     │
              │  they have time       │
              │                       │
              └───────────────────────┘
```

**Human role:** Issue designer. Create parent issues, queue them, review completed work when ready.

**Ralph role:** Execution engine. Process queue autonomously, design sub-issues, implement with backpressure, verify with ACs, auto-chain between modes.

**The dream:** Human attention only needed at two points:
1. **Initial scope** - create parent issue with phases
2. **Final review** - test completed work when ready

### Ralph Loop Detail (per parent issue)

For each parent issue in the queue, Ralph executes this loop:

```
    ┌──────────────────────────────────────────────┐
    │              RALPH LOOP                       │
    │                                              │
    │  ┌─────────┐    ┌─────────┐    ┌─────────┐  │
    │  │ design  │───▶│  impl   │───▶│ verify  │  │
    │  │         │    │         │    │         │  │
    │  │ Creates │    │ Builds  │    │ Runs    │  │
    │  │ sub-    │    │ with    │    │ ACs     │  │
    │  │ issue + │    │ sanity  │    │         │  │
    │  │ DoD/AC  │    │ checks  │    │         │  │
    │  └─────────┘    └─────────┘    └────┬────┘  │
    │       ▲                             │       │
    │       │         ┌───────────────────┘       │
    │       │         │                           │
    │       │    pass │  fail                     │
    │       │         ▼                           │
    │  next phase◄────┴────►back to impl          │
    │                                              │
    └──────────────────────────────────────────────┘
           │
           ▼
    All phases complete → Parent issue closed → Next in queue
```

## Implementation v1 (2026-01-12)

First iteration - subject to change based on testing.

### File Locations

| File | Location | Purpose |
|------|----------|---------|
| loop.sh | ~/.claude/ralph/ | Global loop script |
| prompt_impl.md | ~/.claude/ralph/ | Implementation mode template |
| CLAUDE.md.template | ~/.claude/ | Skeleton for new projects |
| CLAUDE.md | Project root | Project-specific instance |

### loop.sh Behavior

- **Required args:** mode (impl/verify/design) + issue number
- **No defaults:** Exits with help message if args missing
- **Max iterations:** Optional third argument
- **Alias:** `ralph` → ~/.claude/ralph/loop.sh

### prompt_impl.md Structure

Phases wrapped in XML tags (`<phase-0>` through `<phase-5>`):
- Phase 0: Get Up to Speed (read tracking.md, navigate worktree)
- Phase 1: Identify Current Pass (find first pass with unchecked items, verify prior passes complete)
- Phase 2: Understand & Plan (layer→tool mapping for ALL items in current pass)
- Phase 3: Implement (all DoD items in current pass, sanity check each)
- Phase 4: Validate (sanity checks per item, fix and retry)
- Phase 5: Complete Pass (mark all pass items [x], commit, switch_verify)

**Unit of work:** One Pass per iteration (not one DoD item). Passes group related items sharing context and dependencies. Forced verification gate between passes catches issues before the next pass builds on top.

### CLAUDE.md Rules

- **Sections:** Build & Run, Operational Notes
- **Updates:** Append only, never delete
- **Order:** New entries at bottom (bottom = current truth)
- **Contradictions:** Add anyway, bottom supersedes

### Sanity Checks

Dynamic per implementation, not pre-defined. Determined in Phase 2 based on layers touched.

## Implementation v2: prompt_verify.md (2026-01-12)

Specification for verification mode. Designed in rubber-duck session.

### File Location

| File | Location | Purpose |
|------|----------|---------|
| prompt_verify.md | ~/.claude/ralph/ | Verification mode template |

### prompt_verify.md Structure

| Phase | Content |
|-------|---------|
| **0** | Get Up to Speed: Read tracking.md + prior verification.jsonl + git state |
| **1** | Select ACs: List all ACs from tracking.md, process sequentially (all per iteration) |
| **2** | Per-AC Prep: Layer detection → tool selection → pre-verification code review |
| **3** | Execute GWT: Follow ac-verify's 7 principles, always console+network |
| **4** | Result Handling: passed → next; failed → #232 middle ground judgment |
| **5** | Record + Commit: Append verification.jsonl, uncheck failed DoDs, commit |

### Layer Detection (from ac-verify)

| Layer | Keywords in AC | Tool |
|-------|----------------|------|
| UI | click, see, display, show, navigate | Chrome DevTools |
| Database | record, stored, query, table | Supabase MCP |
| API | endpoint, request, response, HTTP | curl/Bash |

### Failure Handling (#232 Middle Ground)

No explicit heuristic. Claude judgment using #232 principle:
- **Quick fix** (config/env/typo): Apply fix, retry verification
- **Big failure** (logic wrong/incomplete): Record failure, exit

**Anti-patterns:**
- ❌ Give up after first failure
- ❌ Rewrite architecture when targeted fix works

### DoD Authority

| Action | Allowed? |
|--------|----------|
| Uncheck DoD (revert false positive) | ✅ Yes |
| Check DoD (mark complete) | ❌ No (impl's job) |

### Exit Signal

- All ACs pass: `<promise>VERIFY_COMPLETE</promise>`
- Failures exist: Exit normally (manual mode switching)

### Commit Scope

- verification.jsonl (always)
- tracking.md (if DoD items unchecked)

### Feedback Loop

prompt_impl.md Phase 0 reads verification.jsonl **full trace**:
- AC ID
- Reasoning
- Trace steps
- Artifacts

This enables impl to learn from prior verification failures.

## How to Extend

Add hypotheses as new patterns emerge. Keep stateless.

## Related Documents

- [three-session-model.md](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model)
- [ac-dod-framework.md](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

