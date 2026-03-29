---
publish: true
---

# Ralph Loop: Blocker Handling Design

[[ralph-loop]]

**Issue:** [#278](https://github.com/DaveX2001/claude-code-improvements/issues/278)
**Parent:** #259 (Three-Session Model Implementation)

---

## Problem

Ralph hits blockers (missing credentials, AskUserQuestion unavailable in headless mode) and either:
1. Loops infinitely hitting the same blocker
2. Loses context when iteration restarts

No Event Clock trace of what happened or why.

---

## Design Decisions

### Event Trace Schema

**Location:** `~/.claude/context-graph/events.jsonl`

```json
{
  "type": "blocker",
  "timestamp": "ISO-8601",
  "project": "IITR__IITR-NAVIGATION",
  "issue": 486,
  "attempting": "Run test harness for baseline accuracy",
  "cause": "OpenWebUI API key not available, AskUserQuestion unavailable in headless mode",
  "attempted_resolution": ["AskUserQuestion → failed", "hippocampus search → not found"],
  "interpretation": "Cannot proceed without credential. Headless mode prevents interactive resolution.",
  "action_taken": "Paused loop, called mcp-feedback-enhanced"
}
```

**Field decisions:**
- `type`: Let emerge (blocker, decision, skip) - don't pre-define
- `cause`: Generic name (not "blocked_by") - works for any type
- `project` + `issue`: Include for co-occurrence pattern discovery
- `attempted_resolution`: Document what was tried (not prescribe solutions)
- `action_taken`: Document the action taken (pause, notify, etc.) - full decision trace
- No `resolution_path`: Not AI's job to prescribe - let future AI learn from data
- No `resume_from`: Redundant with tracking.md state

### Pause Mechanism

**Tool:** `mcp-feedback-enhanced` (existing MCP tool)

**Flow:**
1. Ralph hits blocker → exhausts alternatives first
2. Writes event trace to `events.jsonl`
3. Calls `mcp-feedback-enhanced` with blocker description
4. Desktop/Web UI opens → human sees message
5. Human responds
6. Tool returns → Ralph continues **with full context preserved**

**Why this tool:**
- Blocks until human responds (true async wait)
- Context preserved (no iteration restart)
- Already built and tested

### Timeout Configuration

- Default: 600s (10 minutes)
- Configured longer for overnight runs: 4-24 hours
- Tool parameter overrides MCP config per-call

### Concurrency Limitation

- **Single active session** - second call replaces first
- Acceptable for current phase (one Ralph at a time)
- Future: SEP-1686 Tasks primitive when available

### Behavioral Trigger (LAST RESORT)

**Before calling feedback tool, Ralph must:**
1. Search hippocampus for credentials/patterns
2. Try alternative approaches
3. Consider degraded scope (skip optional steps)
4. Check CLAUDE.md for operational notes

**What IS a blocker:** Missing credential, access denied, explicit human decision required
**What is NOT:** Uncertainty, missing nice-to-have, ambiguous requirement

---

## Implementation Location

Add to `~/.claude/ralph/prompt_impl.md`:
- New `<blocker-handling>` section after Phase 4 (Validate)
- Strong language to enforce last-resort behavior

---

## References

- [Context Graph ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/context-graph-langfuse)
- [AC Verification Decision Trace](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-verification-decision-trace)
- [mcp-feedback-enhanced](https://github.com/Minidoracat/mcp-feedback-enhanced)
- [SEP-1686: Tasks](https://github.com/modelcontextprotocol/modelcontextprotocol/issues/1686)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

