---
publish: true
---

# Brilliant Labs — Client Discovery Call
[[brilliant-labs-client-call]]

## Meeting Goal

Discover what the client wants to build with Brilliant Labs glasses and assess whether Wilsch AI can deliver it.

1. **Use case clarity** — understand the specific customization the client envisions
2. **Feasibility signal** — determine which customization layer is needed and whether it fits Wilsch AI's capabilities
3. **Commercial anchor** — agree on next steps (scope document, pricing estimate, or polite decline)

## Pre-Read

- [Design Doc: Software Customization](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/brilliant-labs/design-doc-software-customization) — platform overview, customization layers, team fit assessment
- [Brilliant Labs Developer Docs](https://brilliant.xyz/pages/developers) — the open-source SDK the client referenced

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. The client's vision for the glasses
⏱️ 10 min

The client asked about "tweaking the software" without specifying what for. The platform supports four customization layers — from simple display overlays to full AI agent backends. The scope difference between these is 10x.

- The glasses can run on-device Lua scripts, companion phone/desktop apps, or custom AI backends
- Frame requires a connected phone at all times; Halo (pre-order) can run AI locally
- Community projects range from navigation HUDs to real-time translation to hotdog classifiers

**To resolve:** The specific use case the client has in mind — what they want the glasses to do that the stock software doesn't.

### 2. Hardware status and product choice
⏱️ 5 min

The client mentioned both Frame and Halo without preference. Frame is available now ($349); Halo is pre-order only with on-device AI. The development approach differs significantly between the two.

- Frame: all compute happens on the connected phone/laptop — the glasses are a display + sensor peripheral
- Halo: ARM Cortex-M55 + NPU enables on-device AI inference without cloud dependency
- Both are $349, but Halo's shipping timeline is uncertain

**To resolve:** Whether the client already owns either product and which one they intend to use.

### 3. The client's technical involvement
⏱️ 5 min

The customization delivery model depends on whether the client can maintain and extend the work, or needs a turnkey handoff. This affects scope, pricing, and ongoing commitment.

- The platform's open-source SDK is accessible to developers comfortable with Python or Flutter
- On-device Lua scripting has a learning curve but is well-documented
- Firmware work requires specialized embedded expertise regardless

**To resolve:** The client's own technical capability and whether they expect a one-time delivery or ongoing support.

### 4. Timeline and urgency
⏱️ 3 min

The client's message had no timeline indication. Halo isn't shipping yet, which may constrain when development can meaningfully start if that's the target product.

- Frame development can start immediately (product is shipping)
- Halo development requires pre-order hardware or SDK-only prototyping
- AI-assisted development compresses delivery timelines significantly

**To resolve:** When the client needs the customization ready and whether there's an external deadline driving the timeline.

### 5. Budget expectations and engagement model
⏱️ 7 min

The client asked "was würdest du ungefähr verlangen?" — an open question with no anchor. Wilsch AI's model is hourly or retainer, not fixed-price project work.

- Hourly or retainer engagement — AI-assisted development means fewer hours per deliverable
- The client's budget expectation is unknown — need a range signal before quoting
- Simple companion app work is materially different from custom AI backend development

**To resolve:** The client's budget range and whether an hourly/retainer model fits their expectations.

## Meeting Format

- **Type:** Discovery call
- **Duration:** ~30 min
- **Expectation:** Marius reads the design doc Pre-Read before the call. Client comes with their use case idea — no preparation needed from their side.
- **Outcome:** Either a scoped follow-up (Wilsch AI drafts a proposal) or a clear "not a fit" signal

## Related

- **Issue:** [#1279 Lead: Brilliant Labs Glasses — Software Customization](https://github.com/DaveX2001/deliverable-tracking/issues/1279)
- **Design Doc:** [Brilliant Labs — Software Customization](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/brilliant-labs/design-doc-software-customization)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

