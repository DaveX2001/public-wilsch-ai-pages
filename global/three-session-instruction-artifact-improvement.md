---
publish: true
---

[[claude-code-architecture]]

# ADR: Adopt Three-Session Model for Instruction Artifact Improvements

## Status
**Accepted** | Date: 2025-12-03

## Context
- Ad-hoc changes to instruction artifacts (skills, commands, protocol) break AI behavior unpredictably
- No formal process exists for diagnosing, fixing, and verifying instruction changes
- Context from failure sessions is lost when starting fix sessions
- Existing lifecycle phases (requirements → implementation → evaluation → execute → commit) work for code but lack verification step for meta-changes
- soloforce is the dedicated system improvement project; project sessions are for regular work

## Decision
We will use a three-session model for instruction artifact improvements:

**Session A (Project):** Regular work where failure/issue is observed. User notices "this needs fixing."

**Session B (soloforce):** Diagnosis and fix session using `/improve-system` command as entry point:
- Loads conversation from Session A via extraction script
- Guides through existing phases: rubber-duck → requirements-clarity → implementation-clarity → evaluation-clarity
- skill-creator discovered at implementation-clarity for skill changes
- Pre-commit pause: prompts for verification before finalizing

**Session C (Project):** Verification session
- Trigger the same scenario that failed
- Confirm behavior differs (fix worked)
- Report back to Session B

**Context transfer:** Extracted conversation JSONL preserves diagnosis context across session boundaries.

## Consequences

### ✅ Positive
- Formal process prevents ad-hoc breakage
- Context preserved via conversation extraction (no re-explaining)
- Verification catches failures before commit lands
- Hooks into existing lifecycle phases (no new protocol)
- Clear session boundaries: project work vs system improvement

### ❌ Negative
- Requires three sessions for complete fix cycle
- Verification is manual (user must test in Session C)
- /improve-system command must be implemented

### ⚪ Neutral
- soloforce becomes the dedicated system improvement hub
- Conversation extraction script becomes part of standard workflow
- Pre-commit verification is command behavior, not lifecycle phase change

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Fix in same session as failure** | Context not preserved; no verification step; mixes project work with meta-work |
| **Pre-change testing/simulation** | Expensive to simulate all scenarios; post-failure introspection is surgical and on-demand |
| **Add verification as formal lifecycle phase** | Over-engineering; verification is specific to instruction changes, not all tasks |
| **No formal process (status quo)** | Ad-hoc changes cause unpredictable breakage; business-critical infrastructure requires process |

---

**Key Resources:**
- **Architecture blueprint:** [improve-system-architecture.md](improve-system-architecture.md) (detailed implementation guide)
- Implementation tracking: [Issue #49](https://github.com/DaveX2001/deliverable-tracking/issues/49)
- Conversation extraction: `soloforce/research/scripts/extract_conversation.py`
- Related: `skill-lifecycle-execution-timing.md` (skill discovery pattern)
- Related: `skill-creator/SKILL.md` (skill modification workflow)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

