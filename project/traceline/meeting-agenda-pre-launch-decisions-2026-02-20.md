---
publish: true
---

# Beta Launch — Pre-Launch Decisions
[[traceline]]

## Meeting Goal

Lock the remaining decisions for Feb 23 launch. Near-term items are launch-blocking; long-term items shape post-beta trajectory but don't block Monday.

1. **Positioning name locked** — concept is articulated, name isn't (README needs it)
2. **Feedback notification stance** — close the loop or leave open for beta?
3. **Beta check-in cadence** — structured feedback meetings with beta users (2 then 10)
4. **Client conversation data** — consent model + technical extraction mechanism for remote Loop 2

## Pre-Read

- [Developer Position Beta — Plugin Scope & Delivery](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope) (design doc, updated 2026-02-20)
- [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The concept is clear but the name isn't locked for the README
⏱️ 15 min

"AI doesn't need more tech people. It needs organizational people." The system treats AI like an employee — position agreements, training, standards, accountability. Developer position makes AI-assisted development deterministic (Issues → Code). Problem frame: rogue developer → organizational standards. "Traceline" has reservations from Christoph ("es verkörpert nicht was wir tun").

- "Assistant" has no differentiation, "interaction patterns" too academic, "behavioral scripts" too clinical
- The actual value: consistency and the ability to improve when things go wrong
- Each position = a product with clear input/output boundaries (JA: Transcripts → Issues, Developer: Issues → Code)
- Reference context: [Sourcegraph — Revenge of the Junior Developer](https://sourcegraph.com/blog/revenge-of-the-junior-developer)
- Roland delivered a "Builder-Operator" persona and README revision — an [extraction pass on these artifacts is in the works](https://github.com/DaveX2001/claude-code-improvements/issues/504#issuecomment-3932976545) and will likely drive this topic forward before the meeting

**To resolve:** A name or descriptor that conveys "organizational standards for AI" without being boring or too meta — needed for the README vision section before launch.

### 2. Users submit feedback but never hear back
⏱️ 5 min

`/feedback` routes to CCI directly. Users see a confirmation but not how feedback is triaged or resolved. For beta with 2 users, the Discord channel provides a personal communication layer. At 10 users, the absence of a feedback loop could feel like a black hole.

- Options: no notification (trust-based), automated "fixed in release X", manual follow-up via Discord
- Cost vs. signal: automation adds engineering, manual doesn't scale

**To resolve:** Whether feedback resolution notifications are a beta feature or a post-beta improvement.

### 3. Two feedback loops serve different purposes — the check-in loop is how the company learns
⏱️ 10 min

Traceline has two distinct feedback loops, and they should not be conflated:

**Loop 1 — Product/company (check-in transcripts):** Structured conversations with beta users produce transcripts. These transcripts become JA source material — the extraction pass system turns them into design doc updates, positioning refinements, and product decisions. This is how the company learns what to build. Precedent: Lovable's weekly "what's working well / what's working bad" meetings.

**Loop 2 — Tool (CCI board):** `/feedback` captures in-session friction. Routes to CCI. System Engineer fixes the underlying pattern. This is how the tool itself improves.

Loop 1 is strategic (what to build). Loop 2 is operational (how the tool behaves). Both compound, but through different mechanisms.

- First 2 users (David, Dylan): close enough for 1:1 check-ins
- At 10 users: group session, individual, or mixed?
- Cadence: weekly, biweekly, or on-demand?
- Check-in transcripts are the highest-value input for the JA extraction system — they contain user language, pain points, and success patterns that no async feedback mechanism can capture

**To resolve:** The format and cadence of structured feedback meetings for all beta users — starting with 2, scaling to 10.

### 4. Conversation data lives on the client's machine — we need consent AND a way to get it
⏱️ 15 min

Two connected problems, one topic. Internal AI behavior maintenance is solved — the [improve-system architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) runs a 3-session workflow (capture → diagnose → verify) using conversation paths. But that workflow depends on reading the conversation. When a beta user hits an issue, their conversation data is on their machine, not ours.

**Consent (CAN we collect it):**
- IP sensitivity is real — cybersecurity client's data cannot be in a shared space
- Beta users are trusted collaborators — consent may be simpler now, but needs a model for scale
- The right to collect/use conversation data could differentiate pricing tiers (Michael Burry model: we improve via your data vs. self-serve)
- "Works towards your way of working, not our way of thinking — while you can subscribe to ours"

**Technical extraction (HOW do we get it):**
- Internally: `/flag-for-improvement` captures the conversation path → we read it locally. On a client's machine, we can't.
- `/feedback` already captures some context — but is it enough, or do we need the full conversation path?
- Possible mechanisms: conversation upload via `/feedback`, conversation store pattern (already built for internal use — [CCI#504](https://github.com/DaveX2001/claude-code-improvements/issues/504)), structured log export
- Connected to Loop 2: without a way to pull client conversation data, the improve-system architecture doesn't work remotely
- Active development pattern: [CCI#574 — Conversation Index: Associate Claude Code sessions with GitHub issues](https://github.com/DaveX2001/claude-code-improvements/issues/574) — this is the technical mechanism being built for internal use that would need to extend to clients

**To resolve:** The consent model for beta AND the technical mechanism that gets conversation data off the client's machine so Loop 2 can function remotely.

## Meeting Format

- **Type:** Working session
- **Expectation:** Christoph has read the updated design doc
- **Outcome:** Topics 1-3 resolved before launch. Topic 4: consent model agreed for beta + technical extraction approach decided (conversation store pattern vs. alternatives).

## Related

- **Issue:** [CCI#504: Cloud Code Operating Launch](https://github.com/DaveX2001/claude-code-improvements/issues/504)
- **Design Doc:** [Developer Position Beta — Plugin Scope & Delivery](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope)
- **Previous Agendas:**
  - [Final Scope Decisions (2026-02-17)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-final-scope-decisions-2026-02-17)
  - [Beta Launch Scope (2026-02-16)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-beta-launch-scope-2026-02-16)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

