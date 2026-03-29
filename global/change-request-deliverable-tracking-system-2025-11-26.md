---
publish: true
---

# Change Request: Deliverable Tracking System
[[team-scaling]]

**From:** Marius
**To:** David
**Date:** 2025-11-26
**Subject:** Proposed changes to tracking system based on our discussion

---

## Summary

After working through the setup questionnaire and thinking about the lifecycle more deeply, I'm proposing some changes to how we'll work together. The core goal remains the same: **get tasks out of my head into a system you maintain, so I can focus on execution.**

---

## Setup Answers

| Question | Answer |
|----------|--------|
| GitHub Username | `MariusWilsch` |
| Repo Name | `deliverable-tracking` |
| Clients | archibus, iitr, rohdex, avo-werke, uwi, pauls-projects, femride, micheal-grants |
| Fields per task | Title, Project/Client, Status, Due Date, Blocker, Dependencies, Definition of Done |
| Updates | CLI + Daily Sync |

---

## Task Lifecycle

```
CREATE (Marius)
    → Task goes to "To Do"

DAILY SYNC (Together)
    → I talk, you ask questions
    → You update board

EXECUTE (Marius)
    → I do the work

CLOSE (Marius)
    → I close issue when done
```

---

## Who Does What

| Activity | Marius | David |
|----------|--------|-------|
| Creates tasks | ✓ | |
| Executes work | ✓ | |
| Closes tasks | ✓ | |
| Tracks status | | ✓ |
| Updates board | | ✓ |
| Flags slippage | | ✓ |
| Asks "what about X?" | | ✓ |
| Answers "what's the status?" | | ✓ |

**My mindset:** In the work, focused on this task
**Your mindset:** Above the work, seeing all tasks

---

## Changes from Your Original Plan

| Your Plan (03_update) | My Proposal | Why |
|-----------------------|-------------|-----|
| David creates issues | Marius creates issues | I have context when tasks emerge |
| David triages inbox | No separate triage | Happens naturally in daily sync |
| Async status updates | Daily sync for all updates | Simpler, one touchpoint |
| Priority (P0/P1/P2) | Due Date instead | More concrete than priority labels |
| 5 fields | 7 fields | Added Due Date, Dependencies, Definition of Done |

---

## Operating Model

### Phase 1: Model A (Start Here)

```
New task → I create via skill/CLI
Daily sync → I talk, you update board
Close → I close issue
```

### Phase 2: Model B (Later)

```
Daily sync → I talk about everything
          → You create/update/close from conversation
```

**When to migrate:** When you're confident capturing tasks correctly from conversation alone.

---

## What You Provide Me

1. **Externalization without overhead** - I talk, you write
2. **Forced daily reflection** - The sync makes me think
3. **External accountability** - Harder to let things drift when you see everything
4. **"What about X?"** - You notice what I forgot
5. **No tracker mode** - I never context-switch to maintaining the system

---

## Next Steps

1. You create repo `MariusWilsch/deliverable-tracking`
2. Set up labels (clients, status)
3. We schedule daily sync time
4. I create initial tasks
5. We start operating in Model A

---

**Questions?** Let me know if anything needs clarification before you set up the repo.

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

