---
publish: true
---

# EasyBooks — SA-Stafford Alignment Meeting
[[easybooks-meeting-agenda-stafford]]

## Meeting Goal

Lock V1 product scope by resolving the 5 configuration choices that determine how the EasyBooks core pipeline is positioned. The core build (Plug → Opera → Shakespeare) is constant — these choices shape the product around it. Additionally, resolve 3 SA-internal decisions required before the engagement can proceed.

1. **V1 audience path** — which of 5 audience clusters is the V1 target
2. **V1 revenue stream** — which of 10 streams funds V1
3. **V1 product configuration** — template, voice style, output format
4. **Team model transparency** — whether to communicate the AI-augmented delivery model
5. **Commercial terms** — retainer structure and pricing
6. **Proof of capability** — which projects to showcase in the RFP response

## Pre-Read

- [EasyBooks Design Doc v0.2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/easybooks-design-doc) — Part 4 (Delivery), Part 7 (Phasing), Part 9 (Proof)
- [Contract Strategy Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/contract-strategy-retainer-model-design) — Three-Slice Ownership Model
- Stafford's RFP — [#874 comment](https://github.com/DaveX2001/deliverable-tracking/issues/874#issuecomment-2707966123) with 2x docx attachments

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. V1 audience — five clusters, three viable paths
⏱️ 20 min

Stafford's specs describe 9+ audience segments. His transcripts name 4 concrete use cases with zero overlap. The core pipeline (interview → book) is identical across all paths — what changes is the application layer wrapped around the agents.

- Specs list founders, HNWIs, parents, creators, thought leaders, 4 corporate types
- Transcripts show: Network School community, My Hometown, elderly parents, heritage/diaspora
- Path A (Community): captive audience, operator dashboard only — smallest app build
- Path B (Legacy): referral-based, user-facing web app with one flow — medium app build
- Path C (Mass Market): growth engine, full platform with analytics — largest app build
- Paths D-E (Enterprise, Platform): not viable for V1
- Agent work (Opera, Shakespeare) is the same regardless of path — ~1 month

**To resolve:** Which audience path defines V1 — the answer determines how much application development is needed beyond the AI agents.

### 2. V1 revenue stream — ten streams, no prioritization
⏱️ 15 min

Stafford's EB Business Master lists 10 revenue streams with equal weight. The pipeline principle ("extraction free, creation paid") means payment infrastructure only exists at Shakespeare — Plug and Opera are free. Each stream requires different infrastructure; picking 2 vs 5 changes the build significantly.

- 2 Core streams: SaaS subscription, premium book creation fees
- 8 Extension streams: podcast credits, NFT minting/royalties, corporate contracts, creative licensing, consultancy, voice cloning, enterprise licensing
- User journey suggests first dollar = per-book fee (interview free → book paid)
- Subscription is the retention model (book is never done), not the acquisition model
- All 8 Extension streams are out of V1 scope

**To resolve:** Which 1-2 revenue streams fund V1 — determines payment infrastructure complexity and go-to-market pricing.

### 3. V1 product configuration — template, voice, output
⏱️ 10 min

Shakespeare supports 5 book templates, 6 voice styles, and 5 output formats. V1 needs one of each. The choice depends on the audience path selected in Topic 1.

- Templates: memoir, founder story, yearbook, image-heavy, co-authored
- Voice styles: reflective memoir, documentary, founder story, poetic legacy, conversational, premium literary
- Output formats: digital PDF, KDP print PDF, cover PDF, low-res preview, EPUB
- Digital PDF is must-have regardless; KDP print-ready is confirmed V1 scope (Stafford's non-negotiable)

**To resolve:** Which template and voice style match the V1 audience — selection follows directly from Topic 1's outcome.

### 4. AI-augmented team model — reveal or internal
⏱️ 10 min

Stafford does not know that the team operates with AI as a force multiplier. In both transcripts (Feb 18 + Feb 20), he compares our pricing against traditional freelancers ($1,500-$3,000 per build). No mention of Claude Code, force multiplication, or AI-augmented development was made in any conversation.

- The "Fractional AI Department" pitch from #847 positions AI as the competitive advantage — but that pitch was designed for the service model, not necessarily for every client
- Explaining the model differentiates from the $1,500 freelancers he's comparing against
- Not explaining means delivering faster than expected without revealing how

**To resolve:** Whether the AI force multiplier model is part of the Stafford pitch or remains an internal operational advantage.

### 5. Commercial terms — retainer structure and pricing
⏱️ 20 min

No contract exists. Negotiation is ongoing. The Feb 21 position was "tending towards no" (retainer too low at 3.5K). The Feb 22 position shifted to "retainer + space fee for three months." Stafford responded with a formal RFP on Mar 8, suggesting the deal is alive. SA position: start with retainer + milestones, defer success-based fee until relationship is established.

- The design doc deliberately contains no numbers — commercial terms are SA authority
- The Three-Slice Ownership Model applies: Wilsch AI owns the function, Stafford owns the results
- Retainer amount follows scope clarity — determined after V1 scope is locked (Topics 1-3)
- Build cost is bundled in the retainer — not itemized per engine
- Running costs (hosting, LLM inference, storage) are separable

**To resolve:** The commercial structure (retainer amount, milestone definitions, payment terms) that Marius brings to Stafford.

### 6. Proof of capability — project selection for RFP Section D
⏱️ 15 min

Stafford's RFP requests specific examples across four categories: AI-agent systems, growth/automation, document generation, and audio/transcription. Confidentiality constraints apply to most client projects.

- Paul Faceless YouTube is a candidate (content generation + automation pipeline)
- Each example needs: what was built, role, team size, timeline, budget range, what worked/what you'd change
- The design doc has a placeholder table in Part 9 with "TBD — SA selects" entries
- Traceline itself could serve as proof of the methodology

**To resolve:** Which VeloxForce projects are shareable and map to Stafford's four capability categories.

## Meeting Format

- **Type:** SA review + Stafford alignment
- **Expectation:** SA reviews design doc v0.3 holistically (Speechify), brings commercial position. Stafford needs to see the 5 audience clusters and 10 revenue streams — his choices drive everything downstream.
- **Outcome:** V1 scope locked (Topics 1-3) → commercial terms decided (Topics 4-6) → engagement can proceed

## Related

- **Issue:** [#1078 — JA Design Doc Pass 1](https://github.com/DaveX2001/deliverable-tracking/issues/1078)
- **Epic:** [#874 — Livebook Pre-Sales](https://github.com/DaveX2001/deliverable-tracking/issues/874)
- **Design Doc:** [EasyBooks Design Doc v0.3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/easybooks-design-doc)
