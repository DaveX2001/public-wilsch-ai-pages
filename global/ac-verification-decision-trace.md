---
publish: true
---

# AC Verification Decision Trace

[[claude-code-architecture]]

A domain-specific context graph for capturing AC verification reasoning as searchable precedent.

## The Problem (Issue #153)

AI verification claims are self-reported and unverifiable. Evidence is ephemeral (lives in conversation only). Future sessions can't verify tests actually ran.

**Bad pattern example:**
> GitHub comment: "E2E validation complete. Both autopilot paths tested and passed"

AI accepted this as proof. But it's a **claim**, not evidence.

## Key Insight: State Clock vs Event Clock

From Context Graph research:

| Clock | Captures | Status |
|-------|----------|--------|
| **State** | What's true now | We have this |
| **Event** | What happened + why + reasoning | Was missing |

> "If your context graph can't answer 'what if,' it's just a search index."

## Key Insight: Not "Proof" but "Precedent"

This is NOT proof it still works. It IS:

1. **Historical trace** - snapshot at {time} showing it WAS tested
2. **Searchable precedent** - future runs can reference how similar cases were handled
3. **Baseline for divergence** - if results differ, we have something to investigate

## Key Insight: GWT = Reproducible Spec

- Given-When-Then IS the test script
- No coded tests to maintain
- Adapts to changes naturally
- **Link to GWT, don't duplicate:** `gwt_ref: "tracking/issue-377.md#ac2"`

**Execution tool by layer:**

| Layer | Tool | Example |
|-------|------|---------|
| **UI** | Chrome DevTools | Navigate, click, check DOM state |
| **Database** | Supabase MCP | Query tables, verify records |
| **API** | curl / Bash | HTTP requests, check responses |

## Key Insight: Decision Trace vs State

**State only (search index):**
```json
{ "url": "/queue", "console_errors": 0 }
```

**Decision trace (event clock):**
```json
{
  "step": "network",
  "observation": "8x 304s",
  "interpretation": "304 = cache hit, not failure"
}
```

The `interpretation` field IS the reasoning. This makes it an event clock.

## Schema Fields (First-Principles)

| Field | Required | Reasoning |
|-------|----------|-----------|
| `ac` | Yes | Identifies what was tested |
| `gwt_ref` | Yes | Links to GWT, no duplication |
| `recorded` | Yes | Historical framing (precedent, not current) |
| `result` | Yes | Queryability ("show all failed ACs") |
| `trace` | Yes | THE CORE - event clock with reasoning |
| `reasoning` | Yes | AC-level synthesis |
| `artifacts` | Failures only | Screenshot + console dump |

## Trace Entry Structure

| Field | Required | Description |
|-------|----------|-------------|
| `step` | Yes | What was checked (url, console, network) |
| `action` | Optional | What was done (only when action taken) |
| `observation` | Yes | What was observed |
| `interpretation` | Optional | What it means (THE REASONING) |

## Two Levels of Reasoning

| Level | Field | Example |
|-------|-------|---------|
| **Per-step** | `interpretation` in trace | "304 = cache hit" |
| **AC-level** | `reasoning` at top | "AC passed because URL matched AND console clean" |

## Artifacts Rule

| Result | Artifacts |
|--------|-----------|
| **Failed** | Screenshot + Console dump |
| **Passed** | None (trace is enough) |

## Domain-Specific Context Graph

For AC verification domain, this CAN answer "what if?":

| Question | Answer |
|----------|--------|
| "What if 304s appear again?" | Yes - precedent says cache hits OK |
| "What if URL has params?" | Yes - can reason from GWT |
| "What if new error type?" | Yes - compare to baseline |

## JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "ACVerificationDecisionTrace",
  "description": "Historical precedent - NOT proof of current state",
  "type": "object",
  "required": ["ac", "gwt_ref", "recorded", "result", "trace", "reasoning"],
  "properties": {
    "ac": {
      "type": "string",
      "pattern": "^AC[0-9]+$",
      "examples": ["AC1", "AC2"]
    },
    "gwt_ref": {
      "type": "string",
      "description": "Link to GWT definition",
      "examples": ["tracking/issue-377.md#ac2"]
    },
    "recorded": {
      "type": "string",
      "format": "date-time"
    },
    "result": {
      "type": "string",
      "enum": ["passed", "failed"]
    },
    "trace": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["step", "observation"],
        "properties": {
          "step": { "type": "string" },
          "action": { "type": "string" },
          "observation": { "type": "string" },
          "interpretation": { "type": "string" }
        }
      }
    },
    "reasoning": {
      "type": "string",
      "description": "AC-level synthesis"
    },
    "artifacts": {
      "type": "array",
      "description": "For FAILURES only",
      "items": { "type": "string" }
    }
  }
}
```

## Example: Pass

```json
{
  "ac": "AC2",
  "gwt_ref": "tracking/issue-377.md#ac2",
  "recorded": "2026-01-02T08:31:59Z",
  "result": "passed",
  "trace": [
    { "step": "navigate", "action": "Clicked Continue", "observation": "Page navigated" },
    { "step": "url", "observation": "/queue", "interpretation": "matches Then clause" },
    { "step": "heading", "observation": "Video Queue", "interpretation": "additional confirmation" },
    { "step": "console", "observation": "0 errors", "interpretation": "clean execution" },
    { "step": "network", "observation": "0 failures, 8x 304s", "interpretation": "304 = cache hit" }
  ],
  "reasoning": "AC passed: URL matched (/queue) AND heading confirmed AND console clean AND network clean (304s = cache hits)"
}
```

## Example: Failure

```json
{
  "ac": "AC2",
  "gwt_ref": "tracking/issue-377.md#ac2",
  "recorded": "2026-01-15T10:22:00Z",
  "result": "failed",
  "trace": [
    { "step": "navigate", "action": "Clicked Continue", "observation": "Page navigated" },
    { "step": "url", "observation": "/create-video", "interpretation": "Expected /queue - MISMATCH" },
    { "step": "console", "observation": "TypeError: Cannot read property 'autopilot'", "interpretation": "JS error" },
    { "step": "network", "observation": "POST /video_process -> 500", "interpretation": "Backend failed" }
  ],
  "reasoning": "AC failed: URL mismatch. Root cause: TypeError caused 500, fallback to Step 2.",
  "artifacts": [
    "ac2-failure-screenshot.png",
    "ac2-failure-console.txt"
  ]
}
```

## Implementation Decisions (#146)

Decisions made during implementation session (2026-01-02):

### Verify Phase Trigger

| Decision | Rationale |
|----------|-----------|
| **After Push, before Task Completed** | Work is preserved first (push), then verified |
| **Can span sessions** | If context exhausts after push, next session continues from "verify pending" |

### Gating Model

| Model | Behavior |
|-------|----------|
| **Hybrid** | Must attempt verification (gate on execution) |
| | Failure is valid result (not gate on outcome) |
| | Task Completed happens regardless of pass/fail |

### Folder Structure

```
.claude/tracking/issue-{N}/
├── tracking.md        # DoD + AC (Given-When-Then specs)
├── verification.jsonl # Decision traces (this schema)
└── artifacts/         # Screenshots for failures only
```

### Artifact Naming

Pattern: `{ac}-{result}-{timestamp}-{type}.{ext}`

Example: `ac2-failure-2026-01-15T10-22-00Z-screenshot.png`

### Reasoning Generation

| Timing | What |
|--------|------|
| **Batch, not streaming** | AI generates reasoning AFTER AC complete |
| **Two levels** | Per-step `interpretation` + AC-level `reasoning` |

### Phase Transition Announcements

```
📍 Moving to [Phase Name]...
```

Added to Communication Standards in CLAUDE.md.

### GWT Reference Update

`gwt_ref` now points to subfolder: `tracking/issue-{N}/tracking.md#ac2`

## References

| Source | Link |
|--------|------|
| Issue #89 | https://github.com/DaveX2001/claude-code-improvements/issues/89 |
| Issue #146 | https://github.com/DaveX2001/claude-code-improvements/issues/146 |
| Issue #153 | https://github.com/DaveX2001/claude-code-improvements/issues/153 |
| Context Graph: How to Build | https://x.com/akoratana/status/1872755487498289488 |
| Context Graph: CRM to CRCG | https://www.linkedin.com/pulse/from-crm-crcg-practical-example-context-graphs-animesh-koratana-cxcpc |
| Context Graph: Trillion $ | https://www.foundation.co/resources/ai-trillion-dollar-opportunity-context-graphs |

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

