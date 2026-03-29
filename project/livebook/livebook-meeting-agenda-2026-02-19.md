---
publish: true
---

# Livebook — First Alignment Meeting
[[client-stafford]]

## Meeting Goal

Align on the V1 product scope and engagement structure for the Livebook platform. This meeting should produce:

1. **Shared product vision** — agreement on what V1 delivers and what it doesn't
2. **Feature scope lock** — must-have features vs. roadmap items for V1
3. **Engagement direction** — enough clarity on terms and timeline to finalize by Friday/weekend

## Pre-Read

- [Livebook Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/livebook-design-doc) — current state of product understanding, including defined elements and open questions

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. V1 feature scope and technical requirements
⏱️ 15 min

The agent architecture (Plug → Opera → Shakespeare) defines WHAT the system does. The V1 feature scope defines HOW MUCH of that ships first. Technical decisions (hosting, languages, integrations) cascade from this.

- Core pipeline: context triggers → adaptive interview → book output
- Existing mockups show: social media import, profile matching, 12 book types
- Technical architecture is premature until feature scope is locked
- Launch signal: World Book Day (April 23) — needs 2-4 weeks PR lead time

**To resolve:** The V1 feature boundary — which elements of the Plug → Opera → Shakespeare pipeline ship first, and the target launch date that drives the timeline backward.

### 2. Product naming
⏱️ 5 min

The platform is currently called "EasyBook" with "NS FamilyBook" as the Network School variant. The working title "Livebook" captures the core idea — a living, growing book — but hasn't been validated externally.

- Current branding: EasyBook / NS FamilyBook
- Working title from design work: Livebook
- Stafford mentioned wanting something about "stories" during initial conversation
- Name affects marketing, domain, and brand positioning

**To resolve:** The product name that will carry through V1 launch and initial marketing.

### 3. Community books and V1 scope
⏱️ 5 min

The NS Family Book, Ladies Lounge book, and My Hometown books are concierge MVPs happening now — manually produced, no platform technology. The Livebook platform is the technology build that will eventually power these.

- NS Family Book: concierge MVP validating demand (pre-tech)
- Ladies Lounge book target: ~Feb 23
- Full NS book target: ~Feb 25-26
- Platform build is separate from concierge efforts

**To resolve:** The relationship between concierge community books and the V1 platform — parallel tracks or sequential dependency.

### 4. Voice cloning V1 scope
⏱️ 10 min

Open-source TTS models (350M parameters, 26 languages) can run on a phone. Voice cloning was discussed as a V1 feature — "put your own voice in there and gift it to a family member." The emotional impact is significant but adds technical complexity.

- Open-source models: free, 26 languages, phone-capable
- 11Labs: high quality but expensive at scale
- Voice cloning creates a fundamentally different emotional experience
- Text-based interviewing delivers the same methodology with less complexity

**To resolve:** Whether voice cloning is a V1 must-have or a post-launch enhancement — balancing emotional impact against delivery speed.

### 5. Book output and fulfillment
⏱️ 10 min

Shakespeare (the book production agent) needs to produce actual books. The output format and fulfillment pipeline are entirely undefined — physical printing, digital-only, or both.

- Original design plans: large A5 printed book, 9 core page templates
- Revenue model discussed: "free book + £9 shipping" with £2-3 profit at scale
- Physical fulfillment requires a printing partner and logistics
- Digital-only is simpler for V1 but loses the tangible artifact hook

**To resolve:** V1 book output format and fulfillment approach — physical, digital, or both.

### 6. Revenue model and pricing
⏱️ 10 min

The principle is clear: interviews are free (builds emotional investment), book output is paid (extraction crystallizes value). The book is never done — continuous extraction maps to a subscription model.

- Subscription discussed: $9.99/month
- Alternative: free book + shipping (volume play, low margin)
- The emotional investment from free interviews IS the sales funnel
- Continuous book growth = recurring revenue justification

**To resolve:** V1 pricing model — subscription, transactional, or hybrid — and the price point that balances accessibility with sustainability.

### 7. Team structure and integration
⏱️ 10 min

Stafford has 2-3 existing MVP developers. Marius brings a delivery methodology with defined positions (architect + developer). The integration approach affects timeline and output quality.

- Existing team: 2-3 devs, built the concierge MVP, can't run a team independently
- Delivery model: one person accountable for the pipeline, scaling through AI-assisted positions
- Integration options: train existing devs on the methodology, or run parallel with handoff later

**To resolve:** How existing developers integrate with the delivery methodology — training, parallel work, or phased handoff.

### 8. Engagement terms
⏱️ 10 min

The engagement starts with product delivery and can expand into broader company systems. Compensation follows a base + success-based model with potential to shift toward more success-based over time.

- Base retainer + success fee structure discussed
- First ~3 months as initial structure, quarterly re-evaluation
- Time commitment as bandwidth allocation, not exclusive hours
- Scope can expand beyond product delivery into company systems

**To resolve:** Engagement terms sufficient to begin work — base amount, success triggers, initial time commitment, and first review point.

### 9. NFT and IP tokenization roadmap
⏱️ 5 min

Story ownership and IP tokenization were discussed with enthusiasm. Marshall (Hydraulic platform) was identified as a potential partner for integrating story ownership via NFTs.

- Marshall not yet contacted
- IP tokenization creates a fundamentally different business model (royalties, story licensing)
- Film/content pipeline (stories → AI films) is a downstream opportunity
- Adds significant complexity if included in V1

**To resolve:** Whether NFT/IP tokenization belongs in the V1 roadmap or is a post-launch strategic initiative.

## Meeting Format

- **Type:** Vision alignment + feature scoping
- **Duration:** ~90 min
- **Expectation:** Review the Design Doc before or during the meeting — it contains the current state of understanding and all open questions
- **Outcome:** Locked V1 feature scope + engagement direction sufficient to finalize terms by Friday/weekend

## Related

- **Design Doc:** [Livebook Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/livebook-design-doc)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

