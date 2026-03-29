---
publish: true
---

[[claude-code-architecture]]

# Task Lifecycle Reference

Quick reference for task lifecycle phases and modes.

## Verify Phase

**What:**
- Explicit phase between Execute and Commit
- Checks implementation against Evaluation criteria defined earlier
- Binary outcome: success or failure against defined criteria

**Why:**
- Currently implicit - execution happens, then commit, no explicit verification
- Makes testing a first-class phase, not an afterthought
- Extension point for future tooling (Chrome MCP, test runners, etc.)

## Rubber-Duck Mode

**What:**
- Pre-lifecycle exploration mode, NOT part of the execution lifecycle
- Pattern: Reflect back → Investigate JIT → Ask questions → Repeat
- Has investigation authority but NO execution authority
- Use cases: debugging, root cause analysis, thinking about approaches, triage

**Why:**
- Figuring out WHAT the problem is before entering the lifecycle
- Lifecycle (Requirements→Execute) is for SOLVING, rubber-duck is for UNDERSTANDING
- Prevents premature execution before problem is clear

## Phase Announcements

**What:**
- Announce phase transitions explicitly
- Include: phase name + authority level
- Format: "Entering [Phase] - [authority description]"

**Why:**
- Phases are currently implicit - hard to know where you are
- Each phase has different authority - this should be visible
- Phases are hooks for improvements - explicit transitions enable extension

---

**Related:**
- [improve-system-architecture.md](improve-system-architecture.md)
- [three-session-instruction-artifact-improvement.md](three-session-instruction-artifact-improvement.md)
- Research: [Issue #144](https://github.com/DaveX2001/deliverable-tracking/issues/144)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

