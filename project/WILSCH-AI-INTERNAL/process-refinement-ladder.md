---
publish: true
---

# Process Refinement Ladder

[[team-scaling]]

Pattern for evolving manual work into automation through progressive refinement.

---

## The Three Stages

```
Stage 1: Human Manual (Zineb)
   → Discover friction points
   ↓
Stage 2: Human + AI Interactive (David + Claude Code)
   → Standardize process
   ↓
Stage 3: AI Autonomous (SDK Agent)
   → Full automation
```

---

## Stage 1: Human Manual

**Executor:** Zineb (or any human without AI tooling)

**Purpose:** Discover how the process actually works

**Output:**
- Friction points documented in issue comments
- "I don't have access to X"
- "I don't know where to find Y"
- "This step requires Z"

**Issue Type:** manager (Zineb-eligible)

**Closing Criteria:** Process executed manually with friction points documented

---

## Stage 2: Human + AI Interactive

**Executor:** David + Claude Code (interactive session)

**Purpose:** Standardize and optimize the process

**Input:** Friction points from Stage 1

**Output:**
- Documented steps in tracking file
- Claude Code can assist but human drives
- Workarounds for friction points

**Issue Type:** maker/spec-implement

**Closing Criteria:** Process documented and repeatable with AI assistance

---

## Stage 3: AI Autonomous

**Executor:** SDK Agent (no human in loop)

**Purpose:** Full automation

**Input:** Standardized process from Stage 2

**Output:**
- Autonomous agent runs on schedule
- Human only reviews exceptions
- Process completes without intervention

**Issue Type:** maker/spec-implement (SDK agent build)

**Closing Criteria:** Agent runs autonomously, human monitors only

---

## When to Use This Pattern

Apply to recurring operational work:
- Expense/invoice collection
- Status monitoring
- Report generation
- Data synchronization

**Not for:**
- One-time tasks (just do them)
- Decision-heavy work (can't automate judgment)
- Client-facing communication (needs human touch)

---

## Issue Structure

For a process going through refinement:

```
Epic: {Process Name} Automation
├── Stage 1: {Process} - Manual (Zineb)
├── Stage 2: {Process} - Interactive (David)
└── Stage 3: {Process} - Autonomous (SDK Agent)
```

Each stage is a separate issue with clear closing criteria.

---

## Related Documents

- [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) - Zineb eligibility criteria

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

