---
publish: true
date: 2026-01-06
---

# Paul UI Design Spec: Channels & Presets

[[client-paul]]

## Overview

Design improvements for the Channels page and Channel Detail (presets) page based on competitor analysis of Produce.so and TubeChef.

**Prototype route:** `/design-lab` in the Paul frontend (temporary, delete after implementation)

## Problem Statement

Current Paul UI for channels/presets is functional but feels sparse:
- Single preset card fills ~15% of viewport, rest is empty black space
- Preset Sheet has minimal context - blind dropdown selections
- No visual differentiation between presets
- Information density mismatched for scale (1 preset or 30 presets)

## Competitors Analyzed

| Product | Pattern | Key Insight |
|---------|---------|-------------|
| **Produce.so** | Visual gallery with thumbnails | Cards as "products to choose" not "database rows" |
| **TubeChef** | Two-column form + summary | Live summary panel, cost transparency, contextual tips |

## Design Decisions

### 1. Card Grid Density

**Before:** 1 card visible per viewport
**After:** 6 cards visible (3-column grid)

**Card anatomy improvements:**
- Taller thumbnail area (`h-48` vs `h-36`) - visual dominates over text
- Type badge ("long-form" / "short-form")
- Config summary: Voice • Image Style (inline)
- Usage stats: "12 videos • 2 days ago"
- Settings gear icon on hover

**Rationale:** Cards should answer "what is this and what can I do with it?" at a glance, not just "what is this called?"

### 2. Rich Sheet (Two-Column Layout)

**Before:** Single column, 3 dropdowns, no context
**After:** Two-column layout with summary panel

**Left column (2/3):**
- Voice dropdown + preview button (🔊)
- Image Style dropdown + preview button (👁️)
- Video Template dropdown
- Helper text per field

**Right column (1/3):**
- Current Config summary (Voice, Style, Template)
- Usage Stats (videos created, last used)

**Footer:**
- Cancel + Save Changes buttons

**Width:** 720px (vs default ~400px)

**Rationale:** User needs context when making selections. Preview buttons enable "try before you save." Summary panel confirms selections before committing.

### 3. Data Requirements

| Feature | Field | Status |
|---------|-------|--------|
| Voice preview | `voices.audio_url` | ✅ Exists |
| Image style preview | `image_styles.image_path` | ✅ Exists |
| Current selections | Template relations | ✅ Exists |
| Usage stats | `templates.videos_created_count` | ❌ New column |
| Last used | `templates.last_used_at` | ❌ New column |

**Migration for stats:**
```sql
ALTER TABLE templates
ADD COLUMN videos_created_count INTEGER DEFAULT 0,
ADD COLUMN last_used_at TIMESTAMPTZ;
```

Can fake initially with "—" placeholder.

## Implementation Reference

### Prototype Location

```
frontend/src/pages/DesignLabPage.tsx  # Temporary prototype
frontend/src/core/router.tsx          # Route: /design-lab
```

Delete after implementing in actual Channel Detail page.

### Key Components to Update

1. **ChannelDetailPage.tsx** - Replace current card grid with dense grid
2. **PresetCard component** - New component with thumbnail + stats + config summary
3. **PresetSheet component** - Two-column layout with summary panel

### CSS/Tailwind Patterns

**Card thumbnail height:**
```tsx
<div className="relative h-48 bg-gradient-to-br ...">
```

**Sheet width + flex layout:**
```tsx
<SheetContent className="sm:max-w-[720px] flex flex-col h-full">
```

**Two-column grid:**
```tsx
<div className="flex-1 grid grid-cols-3 gap-8 py-6">
  <div className="col-span-2">...</div>  {/* Form */}
  <div className="col-span-1">...</div>  {/* Summary */}
</div>
```

## Open Questions

1. **Sheet still needs work** - Vertical space not fully utilized. Consider:
   - Add image style thumbnail preview in summary panel
   - Add more generous spacing between form fields
   - Or accept that 3 fields don't need full height

2. **Real thumbnails vs gradients** - Current prototype uses gradient placeholders. Real implementation should use `image_styles.image_path`.

3. **Settings button visibility** - Currently appears on hover. Consider always-visible for mobile.

## Screenshots

See `/tmp/design-lab-*.png` files for captured screenshots during design session:
- `design-lab-prototype.png` - Initial card grid
- `design-lab-v2-cards.png` - Improved card grid with taller thumbnails
- `design-lab-v2-sheet.png` - Two-column sheet layout

## Related

- [Paul v2 Data Model and UI Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/paul-v2-data-model-and-ui-design)
- Issue #440: Channels-First Navigation

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

