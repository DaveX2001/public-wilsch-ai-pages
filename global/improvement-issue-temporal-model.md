---
publish: true
date: 2026-01-11
status: DESIGN - Pending implementation
---

# Improvement Issue Temporal Model

[[claude-code-architecture]]

## Problem Statement

Improvement ideas accumulate in `claude-code-improvements` because:
- Capture is easy (flag-for-improvement works)
- Processing doesn't happen (no dedicated time)
- Similar issues created without awareness of related issues
- Manual cleanup requires context that only exists during actual work

## Design Principle

**Cleanup through action, not triage.**

The decision "duplicate vs similar" requires work context. You can't decide in a vacuum - you decide when implementing a fix and can see if it covers multiple issues.

## Temporal Information Model

| Stage | Information Surfaced | User Decision |
|-------|---------------------|---------------|
| **Capture** | "Similar issues exist: #X, #Y" | Create anyway? Or add to existing? |
| **Work** | "Related: parent + siblings" | Consider while implementing |
| **Commit** | "What does this fix close?" | Multi-close via `Fixes` keyword |

## Capture Flow

After introspection, before issue creation:

```
Find similar issues (sub-agent)
         |
         v
+----------------+----------------+----------------+
| No matches     | Matches exist, | Parent already |
|                | no parent      | exists         |
+----------------+----------------+----------------+
| Create         | Create parent  | Add as child   |
| standalone     | + link both    | to existing    |
| (miscellaneous)| as children    | parent         |
+----------------+----------------+----------------+
```

### Three Capture Paths

**Path 1: Miscellaneous**
- No similar issues found
- Create standalone issue
- May become parent later if related issues emerge

**Path 2: Create Parent + Children**
- Similar issues exist but no parent
- Create new parent issue (theme-level)
- Link new issue + existing similar issues as children

**Path 3: Add to Existing Parent**
- Parent already exists for this theme
- Add new issue as child of existing parent

## Work Flow

When selecting issue to work on:

1. **Surface related issues:** Show parent + siblings
2. **Context during implementation:** Related issues visible
3. **At commit:** Prompt "Does this fix also address #A, #B?"

## Commit Flow

```
PR body:
Fixes DaveX2001/claude-code-improvements#parent
Fixes DaveX2001/claude-code-improvements#child1
Fixes DaveX2001/claude-code-improvements#child2
```

GitHub auto-closes ALL referenced issues on merge.

**Result:**
- No manual "close as duplicate" decisions
- Context preserved in closed issues (not deleted)
- Cleanup = natural byproduct of fixing

## Shared Sub-Agent: Find Similar Issues

Used by BOTH capture and work stages.

**Algorithm (two-phase):**
1. **Title scan (cheap):** Fetch all issue titles, keyword match
2. **Body read (targeted):** For top N candidates, read bodies to confirm similarity

**Input:** Description/keywords of the improvement
**Output:** List of potentially related issues with:
- Issue number + title
- One-sentence summary
- Existing parent (if any)

## Implementation Components

| Component | Purpose |
|-----------|---------|
| Find similar issues sub-agent | Shared search logic |
| flag-for-improvement update | Add parent-child capture flow |
| improve-system update | Surface related issues at work time |
| PR template | Multi-issue `Fixes` keywords |

---

**Related:**
- [improve-system-architecture](improve-system-architecture) - Current capture/fix workflow
- [adr-adopt-epic-sub-issue-pattern](adr-adopt-epic-sub-issue-pattern-for-deliverable-tracking) - Parent-child pattern for deliverables

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

