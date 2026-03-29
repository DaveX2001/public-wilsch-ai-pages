# ADR: Unified Script Step in v2 Wizard
[[paul-youtube-fullstack]]

publish: true

## Status
**Accepted** - 2026-01-03

## Context

The v1 Create Video wizard uses **conditional step visibility**:
- "Raw Transcript" step only appears for Video URL mode
- "Voice Configuration" step combines LLM output display + voice selection + speed

This creates UX confusion:
- Steps appear/disappear based on mode selection
- Different step counts between modes (5 vs 4)
- "Voice Configuration" is misleadingly named (contains LLM output editing)

## Decision

v2 will use a **unified step architecture** with mode-adaptive content:

### Step Structure (Always 5 Steps)

| Step | Name | Content |
|------|------|---------|
| 1 | Setup | Source type selection + input (URL/file/description) |
| 2 | **Script** | Mode-adaptive content editing (unified) |
| 3 | **Voice** | Voice selection + speed only |
| 4 | Footage | Image style selection |
| 5 | Preview | Final result |

### Step 2 "Script" - Mode Adaptation

**Video URL mode:**
- Shows raw YouTube transcript in editor
- "Enable AI Enhancement" toggle
- When enhanced: shows processed version (can compare with raw)

**Custom Script mode:**
- File upload or paste text area
- Optional "Enhance with AI" toggle
- Shows uploaded/pasted content

**Title Generation mode:**
- Shows description input + AI-generated script
- "Regenerate" option for different versions

### Key Changes from v1

| Aspect | v1 | v2 |
|--------|-----|-----|
| Raw Transcript step | Conditional (Video URL only) | Always visible as "Script" |
| LLM output display | In Voice Config (Step 3) | In Script (Step 2) |
| Voice step content | LLM output + voice + speed | Voice + speed ONLY |
| Step visibility | Conditional based on mode | Always 5 steps |
| Autopilot flow | Skip wizard → /downloads | Summary Review → Generate |

### Express Path Decision

When `autopilot=true`, all 5 step pills remain visible. Steps 2-4 show as "skipped" (dashed outline). User jumps from Step 1 directly to Step 5, which displays Summary Review content instead of video preview.

**Why:** "Don't hide complexity—make it optional." Industry research shows users benefit from seeing the complete flow even when skipping parts. Builds trust and reduces uncertainty. Pattern: Material UI, GitLab, PatternFly all keep optional steps visible.

**Flow with step indicators:**
```
Manual Mode (autopilot OFF):
[1●] → [2●] → [3●] → [4●] → [5●]
 ↓       ↓       ↓       ↓       ↓
Setup  Script  Voice  Footage Preview

Express Mode (autopilot ON):
[1●] → [2┊] → [3┊] → [4┊] → [5●]
 ↓     skip    skip   skip     ↓
Setup ─────────────────────→ Summary Review
```

**Step 5 content adaptation:**
- Autopilot OFF: Shows video preview (standard Preview)
- Autopilot ON: Shows Summary Review (settings confirmation + Generate button)

### Skip State Decision

All 5 steps remain visible regardless of mode. Skipped steps use distinct visual treatment.

**Why:** Spatial consistency—navigation never changes shape. Users learn the full flow even in express mode. Mental model transfers across configurations.

**Visual states for step pills:**
- **Active (●):** Filled color, current step
- **Completed (✓):** Outline with checkmark, clickable to revisit
- **Upcoming (○):** Gray outline, not yet reached
- **Skipped (┊):** Dashed outline, "(optional)" label, clickable to see why skipped

**Behavior when toggling autopilot on Step 1:**
- Toggle OFF → ON: Steps 2-4 immediately change from "upcoming" to "skipped"
- Toggle ON → OFF: Steps 2-4 change back to "upcoming"
- User sees preview of what express mode will skip before clicking Continue

## Consequences

### Positive
- Consistent user experience across all modes
- Clear semantic naming (Script = your content, Voice = audio settings)
- Users learn one flow that applies to all modes
- No "disappearing steps" confusion

### Negative
- Diverges from v1 behavior (migration consideration)
- Step 2 content varies significantly between modes
- More complex Step 2 component implementation

### Impact on Step Responsibilities

**Step 2 (Script):**
- Owns all content editing (raw + enhanced)
- Handles mode-adaptive switching

**Step 3 (Voice):**
- Audio settings ONLY (voice selection + speed)
- No content display or editing

## References

- [Light Tier Scope](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/light-tier-v2-feature-scope)
- [Strangler Pattern ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/adr-strangler-pattern-sequencing)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

