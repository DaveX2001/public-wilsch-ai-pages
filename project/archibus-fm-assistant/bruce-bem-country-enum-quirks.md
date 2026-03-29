---
publish: true
---

# Bruce BEM Country Enum Quirks
[[client-archibus]]

Validated findings from live API testing against Bruce BEM staging (`Assets/ValidateAssets`). The country enum has data quality issues that any fuzzy-match implementation must handle.

## Trailing Spaces (25 of ~249 entries)

These country values have a trailing space — exact match fails without it:

| Country (as stored) | Note |
|---------------------|------|
| `"Bahamas "` | |
| `"British Indian Ocean Territory "` | |
| `"Cayman Islands "` | |
| `"Central African Republic "` | |
| `"Cocos (Keeling) Islands "` | |
| `"Comoros "` | |
| `"Congo "` | Republic of the Congo |
| `"Cook Islands "` | |
| `"Dominican Republic "` | |
| `"Faroe Islands "` | |
| `"French Southern Territories "` | |
| `"Gambia "` | |
| `"Holy See "` | Vatican |
| `"Lao People's Democratic Republic "` | |
| `"Marshall Islands "` | |
| `"Netherlands "` | |
| `"Niger "` | |
| `"Northern Mariana Islands "` | |
| `"Philippines "` | |
| `"Russian Federation "` | |
| `"Sudan "` | |
| `"Turks and Caicos Islands "` | |
| `"United Arab Emirates "` | |
| `"United States Minor Outlying Islands "` | |
| `"United States of America "` | |

## Typos and Ambiguities

| Value | Issue |
|-------|-------|
| `"North Kores"` | Typo — should be "North Korea" |
| `"Korea"` | Ambiguous — presumably South Korea |
| `"Congo "` | Ambiguous — Republic of the Congo (trailing space) |
| `"Congo DR"` | Non-standard — Democratic Republic of the Congo |

## Implications for Fuzzy Match (#863)

1. **Always `.strip()` both input and enum values** before comparison
2. **Fuzzy lookup** needed for common aliases (e.g., "Holland" → "Netherlands ", "USA" → "United States of America ")
3. **Cannot fix server-side** — this is Rein's database, treat as given
4. **Validation error format** returns `valid_values` array with all 249 entries — useful for building lookup table at runtime

## Source

- API endpoint: `POST Assets/ValidateAssets` with `country: "Netherlands"` (no trailing space) → error response includes full enum
- Discovered: 2026-02-19, issue #841 verification session
- Related: [Step 3 Field Tier Mapping](bruce-bem-country-enum-quirks.md) field #17 (Country)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

