---
publish: true
---

# Beta Launch Scope — Remaining Decisions
[[traceline]]

## Meeting Goal

Resolve the undefined items from the design doc so Marius can begin plugin cleanup and Christoph can build the tutorial with a locked scope.

1. **Repo model locked** — shared plugin repo vs per-user forks decided
2. **Rollout plan agreed** — criteria for 1-2 → 10 user expansion
3. **Feedback mechanism decided** — voice notes: must-have or defer

## Pre-Read

- [Developer Position Beta — Plugin Scope & Delivery](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope) (design doc with resolved decisions + undefined markers)
- [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services) (the experience we're shipping)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Where beta user issues live determines everything downstream

GitHub is backend-only — users never see it directly. But every system component (commit linker, onboarding, issue-comment) needs a target repo. For beta, users WILL interact with GitHub Issues (reading specs, tracking files).

- Shared plugin repo: all beta users' issues visible to each other and us
- Per-user forks: independent, more setup, users don't see each other
- Users WILL see GitHub for beta (reading specs, tracking files)
- Long-term: CLI + own UI abstracts GitHub away entirely
- Core tension: support visibility vs user privacy

### 2. The expansion criteria from 2 to 10 users aren't defined

Start with 1-2, trickle to 10. But the trigger for expansion affects how much polish the first version needs.

- Time-based (expand after 1 week regardless) vs signal-based (first 2 complete a full cycle) vs feeling-based (we agree it's ready)
- Who are the first 2: Christoph + one external, or two externals?
- Beta user selection criteria: developers only? Claude Code experience required?
- Google Doc exists for the list — outreach plan undefined

### 3. Voice note feedback may not be needed for the first 2 users

Text-based flag-for-improvement ships. Voice notes add friction reduction but require infrastructure (WhatsApp, Telegram, or Fireflies).

- Is this a must-have for beta launch or a week-2 addition?
- The first 2 users are close collaborators — text may be enough
- Building voice infrastructure before we know if 2 users even need it might be premature

## Meeting Format

- **Type:** Discovery
- **Expectation:** Christoph has read the design doc (Pre-Read above)
- **Outcome:** All 3 undefined markers resolved → design doc updated

## Related

- **Issue:** [CCI#504: Cloud Code Operating Launch](https://github.com/DaveX2001/claude-code-improvements/issues/504)
- **Design Doc:** [Developer Position Beta — Plugin Scope & Delivery](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

