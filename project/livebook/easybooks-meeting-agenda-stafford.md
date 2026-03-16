---
publish: true
---

# EasyBooks — SA-Stafford Alignment Meeting
[[client-stafford]]

## Meeting Goal

Lock V1 product scope by resolving the 4 configuration choices that determine how the EasyBooks core pipeline is positioned. The core build (Plug → Opera → Shakespeare) is constant — these choices shape the product around it.

1. **V1 audience path** — which of 5 audience clusters is the V1 target
2. **V1 revenue stream** — which of 10 streams funds V1
3. **V1 product configuration** — template, voice style, output format
4. **Delivery medium** — web app, mobile app, or API — determines app layer scope

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

### 4. Delivery medium — web app, mobile app, or API
⏱️ 10 min

Stafford's two technical documents (EB Business Master, March Technical Lifecycle Plan) describe 4 engines with database schemas, layer architectures, and phase plans — all backend/engine specifications. Neither document specifies how users access the system: web app, mobile app, desktop app, or API-only. The older UX docs (My NS Family Book) have mockups but pre-date the EasyBooks 4-engine architecture.

- The EB Business Master describes what the engines DO, not how users interact with them
- The March Technical Plan has developer-ready specs per engine but zero frontend specs
- The app layer (landing page, audio recorder, interview UI, book viewer) is required for a working product
- App complexity scales with audience path: operator dashboard (Path A) vs user-facing app (Path B) vs full platform (Path C)
- This is significant build scope beyond the engine specs that must be accounted for in the 30-day commitment

**To resolve:** The delivery medium for V1 — determines app layer build scope and whether additional frontend development is needed beyond the AI agents.

## Meeting Format

- **Type:** SA review + Stafford alignment
- **Expectation:** SA reviews design doc v0.3 holistically (Speechify), brings commercial position. Stafford needs to see the 5 audience clusters and 10 revenue streams — his choices drive everything downstream.
- **Outcome:** V1 scope locked (Topics 1-4) → engagement scope defined

## Related

- **Issue:** [#1078 — JA Design Doc Pass 1](https://github.com/DaveX2001/deliverable-tracking/issues/1078)
- **Epic:** [#874 — Livebook Pre-Sales](https://github.com/DaveX2001/deliverable-tracking/issues/874)
- **Design Doc:** [EasyBooks Design Doc v0.3](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/livebook/easybooks-design-doc)
