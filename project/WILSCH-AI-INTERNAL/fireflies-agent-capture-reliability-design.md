---
publish: true
---

# Fireflies Agent: Capture Reliability Design
[[claude-code-sdk-agents]]

**Date:** 2026-01-23
**Status:** Ready for Implementation
**Owner:** David
**Issue:** [#601](https://github.com/DaveX2001/deliverable-tracking/issues/601)
**Repo:** `DaveX2001/deliverable-tracking/agents/fireflies-webhook`

---

## Problem Context

### Role Shift
Marius has shifted from **issue executor** to **issue designer**. As designer, he is responsible for ensuring work gets done, but cannot:
- Review all triage input sources manually
- Execute work not captured in the system

**Consequence:** If the transcript agent doesn't create an issue, the work doesn't exist in the system, and it won't get done.

### System Dependency
The Fireflies agent processes meeting transcripts and routes them to GitHub issues. The system must work reliably because manual review of all transcripts is not feasible.

---

## Failure Modes Identified

### 1. Confidence Gap (Items Vanish)

**Current thresholds:**
```
0.9-1.0   → AUTO-CREATE
0.5-0.89  → ⚠️ DISAPPEARS (not created, not reviewed)
<0.5      → Ignored (intentional)
```

Items with medium confidence (0.5-0.89) are:
- Not auto-created (below 0.9 threshold)
- Not added to `unmatchedItems` (so never become review issues)
- **Simply vanish from the pipeline**

**Evidence from real transcripts:**

| Item | Confidence | Outcome |
|------|------------|---------|
| Mail Processing Service | 0.85 | Vanished |
| Grok Trading Algorithm | 0.75 | Vanished |
| Marketing Strategy | 0.85 | Vanished |
| PDF/Image Extraction | 0.80 | Vanished |

All detected correctly, all fell through the gap.

### 2. Topic Segmentation (Items Missed)

Transcripts contain multiple topics. If topic segmentation fails, entire topics are missed before routing even begins.

Current approach: LLM identifies topics and routes in single pass.

Potential improvement: External topic detection (e.g., AssemblyAI) as preprocessing step.

### 3. Core Insight: Themes vs Action Items

**The agent extracts the wrong thing.**

| What agent does | What is needed |
|-----------------|----------------|
| Extracts **action items** (what to DO) | Identifies **parent themes** (what was DISCUSSED) |
| "Send Estonian BIM requirements to Marius" | "Estonian BIM Standards" |
| Verb-based: Send, Create, Research, Define | Noun-based: Marketing, BIM Standards, Trading |

The agent's fundamental approach needs to change:
- **From:** Find action items with assignees and deadlines
- **To:** Identify the parent themes discussed in this meeting

Marius (issue designer) then decides what actions to take for each theme.

---

## Design Principles

### 1. Capture First, Routing Second

**Must-have:** 100% of actionable items reach backlog
**Nice-to-have:** Intelligent routing (can iterate later)

Rationale: The cost of missing work (won't get done) exceeds the cost of extra issues (cleanup required).

### 2. Grooming Handles Cleanup

The daily grooming process with David is designed to handle volume:
- 15-minute time-box per item
- Four questions classify each item (Assignee, Blocked/Ready, Context, Maker/Manager)
- Definition of Ready gate before To-Do

If items reach backlog, grooming filters the noise. The agent's job is to get items INTO backlog.

### 3. No Item Should Disappear

Every detected item must route somewhere:
- Create new issue (high confidence)
- Comment on existing issue (match found)
- Create review issue (uncertain items)
- Skip (only for truly non-actionable content)

The confidence gap violates this principle.

---

## Success Criteria

**Primary metric:** % of actionable transcript content that becomes tracked issues

**Target:** 100% capture (accept cleanup cost)

**Validation approach:** Compare agent output against human review of same transcripts. Any item a human would track that the agent missed = failure.

---

## Evaluation Approach (Topic Segmentation)

### Methodology

1. **AssemblyAI in isolation** - External topic detection only
2. **LLM in isolation** - Current single-pass approach
3. **LLM validation** - Compare outputs, identify gaps
4. **Decision** - Hybrid, either/or, or new approach based on results

### Success criteria for topic detection

- Recall: No actionable topics missed
- Boundary accuracy: Topics correctly separated (not merged or split wrong)
- Precision: Acceptable noise level (lower priority)

---

## Implementation Scope

### Location
`DaveX2001/deliverable-tracking/agents/fireflies-webhook`

### Key Files
- `prompt.md` - LLM instructions (confidence bands, what to detect)
- `index.ts` - Routing logic (thresholds, output handling)

### Phased Approach (Suggested)

**Phase 1: Close the Confidence Gap** (Quick win)
- Route medium-confidence items (0.5-0.89) somewhere
- Options: Lower auto-create threshold OR add to review issues
- Small change in `index.ts`

**Phase 2: Improve Topic Detection** (Larger effort)
- Evaluate AssemblyAI preprocessing
- Compare with current LLM approach
- Decide on hybrid vs replacement

---

## Test Cases

Three transcript examples from 2026-01-23 demonstrate the problem:

### Test Case 1: Roman Meeting
- **Transcript:** Business development discussion
- **Detected:** Mail Processing Service (0.85), Grok Trading Algorithm (0.75)
- **Created:** None
- **Expected:** Both should become issues

### Test Case 2: Kaia Marketing
- **Transcript:** Marketing strategy discussion
- **Detected:** YouTube Content Creation Plan (0.85)
- **Created:** None (went to reviewIssues[588] only)
- **Expected:** Issue should be created

### Test Case 3: Mujahid/ARCHIBUS
- **Transcript:** ARCHIBUS development discussion
- **Detected:** PDF/Image Extraction Tool (0.8)
- **Matched:** #373, #548 (correct)
- **Created:** None for new issue
- **Expected:** New issue for PDF extraction tool

---

## Related Documents

- [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) - Stage definitions
- [Grooming Guide](https://github.com/DaveX2001/deliverable-tracking/blob/main/GROOMING.md) - Daily cleanup process
- [AssemblyAI Topic Detection](https://www.assemblyai.com/docs/speech-understanding/topic-detection) - External option

---

## Open Questions

1. Should the confidence threshold be lowered (0.9 → 0.5) or should medium items go to review?
2. What is the expected volume increase from 100% capture?
3. How does AssemblyAI topic detection compare to LLM for Fireflies transcripts specifically?

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

