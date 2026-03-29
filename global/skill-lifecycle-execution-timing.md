---
publish: true
---

[[claude-code-architecture]]

# ADR: Adopt Skills as Pre-packaged HOW with Lifecycle-Orchestrated Execution

## Status
**Accepted** | Date: 2025-11-29

## Context
- Skills were invoked immediately during clarity phases instead of being deferred to execution
- This caused confusion between discovery (deciding to use a skill) and execution (actually running it)
- No clear separation between "what skills apply?" (implementation-clarity's job) and "run the skill" (execute phase's job)
- Needed a principle-level decision about when skills enter the workflow and when they actually run

## Decision
We will treat skills as pre-packaged domain expertise (HOW to do something) that are:

- **Discovered** during Implementation-Clarity (identified as relevant to requirements)
- **Confirmed** by user before adding to execution plan
- **Executed** during the Execute phase (not immediately during clarity phases)

Skills focus on domain expertise. The lifecycle handles timing. Skills don't need to know about phases - the orchestration layer (commands) handles when to invoke them.

## Consequences

### ✅ Positive
- Clear separation: clarity phases = discovery, execute phase = invocation
- User controls which skills enter the plan via confirmation
- Skills remain focused on HOW, agnostic to lifecycle timing
- Prevents premature execution during planning/disambiguation

### ❌ Negative
- Requires explicit deferral discipline (no immediate `Skill()` calls during clarity)
- Commands must track confirmed skills and invoke them at the right phase
- Slight complexity in handoff from confirmation to execution

### ⚪ Neutral
- Exception exists: Hippocampus runs immediately at requirements-clarity (knowledge retrieval prerequisite)
- Implementation-clarity command needs skill confirmation workflow
- Execute phase must check for confirmed skills to invoke

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Invoke skills immediately when discovered** | Conflates discovery with execution; user loses control over plan |
| **Skills decide their own timing** | Adds complexity to skills; breaks separation of concerns |
| **No skill confirmation** | User has no visibility or control over which skills enter plan |
| **All skills run at requirements-clarity** | Most skills are for HOW, not WHAT - wrong phase for action skills |

---

**Key Resources:**
- Related: `claude-code-extension-invocation-taxonomy.md` (what skills are)
- Implementation: `~/.claude/commands/implementation-clarity.md` (skill confirmation workflow)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

