---
publish: true
---

[[claude-code-architecture]]

# LLM Pre-Filtering for Chrome DevTools MCP

## Problem

Chrome DevTools MCP tools return verbose output that consumes context window:

| Tool | Typical Output | Useful Signal | Noise |
|------|----------------|---------------|-------|
| `take_snapshot` | 58 UIDs | ~4 relevant | 93% |
| `list_console_messages` | 130 messages | ~5 errors | 96% |
| `list_network_requests` | 50+ requests | ~3 relevant | 94% |

**Economics:** ~8,500 tokens per snapshot at $0.015/1K = ~$0.13 wasted per call.

## Solution

Add `--context` flag that sends output to a fast LLM for filtering before returning to Claude.

**ROI:** ~$0.005 LLM cost → ~$0.12 saved = 25x return per call.

## Contract

### Input

- `--context <query>` is **required**
- Natural language: `--context "find play buttons"`
- Escape hatch: `--context "*"` returns full output

### Output

- Same format as today, just filtered
- No breaking changes

### Example

```bash
# Filtered (typical)
node scripts/take_snapshot.js --context "find play buttons"
# → 4 UIDs returned

# Full output (escape hatch)
node scripts/take_snapshot.js --context "*"
# → 58 UIDs returned

# Missing context (error)
node scripts/take_snapshot.js
# → Error: --context is required
```

## Architecture

### Target Scripts

Priority order:
1. `take_snapshot.js` - highest call frequency
2. `list_console_messages.js` - debugging sessions
3. `list_network_requests.js` - API debugging

### Implementation Path

Fork upstream → implement → PR when ready.

**Repo:** `ulasbilgen/mcp-skills-plugins`

**Structure:**
```
scripts/
├── take_snapshot.js           ← Modify: add --context flag
├── list_console_messages.js   ← Modify: add --context flag
├── list_network_requests.js   ← Modify: add --context flag
├── llm_filter.js              ← NEW: shared filter helper
└── mcp_client.js              ← unchanged
```

### Model Selection

**Primary:** Gemini 2.5 Flash-Lite via OpenRouter
- ~$0.0003/call
- <1 second latency
- Multimodal capable

**Fallback:** Gemini 3 Flash (if quality issues)

**API Key:** Load from `~/.claude/.env` (OPENROUTER_API_KEY)

### Local Model Consideration

Evaluated but rejected:
- Base M2: 4-5 seconds (too slow)
- M2 Pro/Max: 2-2.5 seconds (borderline)
- Gemini Flash-Lite: <1 second (chosen)

Can migrate to local (Qwen2.5 7B via Ollama) later if economics change.

## Instruction Placement

Claude needs to know to USE `--context`. Document in:

1. **SKILL.md Best Practices:**
```markdown
## Best Practices

1. **Always use --context for verbose tools** - Specify what you're looking for:
   node scripts/take_snapshot.js --context "find login button"
   This reduces context usage by 90%+.
```

2. **Tool descriptions** - Update `--help` text to emphasize --context requirement.

## Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│ 1. CLI receives: --context "find play buttons"                  │
├─────────────────────────────────────────────────────────────────┤
│ 2. Call MCP tool → get full snapshot (58 UIDs)                  │
├─────────────────────────────────────────────────────────────────┤
│ 3. Send to Gemini 2.5 Flash-Lite:                               │
│    ┌────────────────────────────────────────┐                   │
│    │ System: You filter accessibility trees │                   │
│    │ User: Context: "find play buttons"     │                   │
│    │       Snapshot: [58 UIDs]              │                   │
│    │ Return: Only matching UIDs             │                   │
│    └────────────────────────────────────────┘                   │
├─────────────────────────────────────────────────────────────────┤
│ 4. LLM returns: ["162_38", "162_44", "162_50", "162_56"]        │
├─────────────────────────────────────────────────────────────────┤
│ 5. Script filters original output to only those UIDs            │
├─────────────────────────────────────────────────────────────────┤
│ 6. Print filtered output (same format, fewer items)             │
└─────────────────────────────────────────────────────────────────┘
```

## Related

- Issue: DaveX2001/deliverable-tracking#271
- Upstream: github.com/ulasbilgen/mcp-skills-plugins
- Reference: [[programmatic-tool-calling]] pattern

## Status

Design complete. Ready for implementation.

---

*Rubber-duck session: 2025-12-18*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

