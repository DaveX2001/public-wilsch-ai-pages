---
publish: true
---

# Brilliant Labs Glasses — Software Customization
[[brilliant-labs-software-customization]]

Design doc for scoping and pricing software customization work on Brilliant Labs smart glasses (Frame or Halo) for an inbound sales lead.

---

## Problem Statement

An existing business contact has approached Wilsch AI about customizing the software on Brilliant Labs smart glasses (Frame or Halo). The request is exploratory — the client has identified the hardware platform and its open-source developer ecosystem, but has not yet articulated a specific use case beyond "software tweaks."

The problem to solve before the call: understand the platform's customization surface well enough to (1) ask the right scoping questions, (2) assess feasibility against Wilsch AI's capabilities, and (3) provide a credible pricing anchor.

**Preconditions:**
- Client has NOT specified what customization they want — the call must discover this
- Brilliant Labs offers two products: Frame (shipping, peripheral model) and Halo (pre-order, on-device AI) — the client mentioned both without preference
- The platform is genuinely open source (firmware, hardware designs, SDKs all public)
- Wilsch AI has no prior embedded/firmware experience — app-layer work (Python, Flutter) is within capability; RTOS-level work is not

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Marius enters the client call with enough platform understanding to scope work confidently and provide a pricing anchor |
| **Success** | The call produces: (1) a clear understanding of the client's use case, (2) a feasibility assessment (can we / can't we), (3) a pricing range the client can evaluate |
| **Done test** | Can Marius answer "what would this cost and how long?" without hedging on unknowns we could have researched beforehand? If YES → design is complete |

---

## Approach

### Part 1 — Platform Overview

Brilliant Labs makes two smart glasses products, both fully open source (firmware, hardware designs, SDKs):

| | Frame | Halo |
|--|-------|------|
| **Status** | Shipping | Pre-order only |
| **Compute** | ARM Cortex-M4F, 256KB RAM | ARM Cortex-M55 + Ethos-U55 NPU (46 GOPs) |
| **AI capability** | None on-device — requires host phone/laptop | On-device inference (TFLite INT8 models) |
| **Battery** | ~2-3 hours | ~14 hours |
| **Display** | 640x400 micro-OLED | Color micro-OLED |
| **Dev stack** | Lua (device) + Python/Flutter (host) | Same + Zephyr RTOS + Vibe Mode |
| **Price** | $349 | $349 |

**Key constraint:** Frame is a peripheral — every app requires a connected phone or laptop. Halo is more autonomous but not yet available.

### Part 2 — Customization Layers

Four layers of customization exist, each with different skill requirements:

1. **Lua scripts (on-device):** Display rendering, camera capture, sensor reading, audio recording. Constrained by 256KB RAM. APIs cover display, camera, microphone, IMU, Bluetooth, file system.

2. **Companion apps (Python / Flutter):** The primary development surface. Host app runs on phone/laptop, communicates with glasses over Bluetooth LE. Full SDK available. This is standard app development — within Wilsch AI's core capabilities.

3. **Custom AI backend (Noa fork):** Brilliant Labs' AI agent "Noa" is a Python HTTP server (open source). Custom tools = fork the `noa-assistant` repo and add modules. For AI-related customization, this is the most likely path. Directly within Wilsch AI's expertise (Python + LLM integration).

4. **Firmware (C / Zephyr RTOS):** Deep hardware modification. Requires embedded systems expertise. Outside Wilsch AI's current capabilities — flag as a boundary.

### Part 3 — Team Fit Assessment

| Layer | Wilsch AI Fit | Notes |
|-------|--------------|-------|
| Companion apps (Python/Flutter) | Strong | Core stack match |
| Custom AI backend (Noa fork) | Strong | Python + LLM = bread and butter |
| Lua scripting | Medium | Learnable, but no prior Lua/embedded experience |
| Firmware (C/Zephyr) | Weak | No embedded capability — subcontract or decline |

**Recommendation for the call:** Lead with layers 2-3 (companion apps + custom AI backend). If the client's ask involves firmware, flag honestly and propose partnering with an embedded developer from the Brilliant Labs Discord community.

### Part 4 — Pricing Model

Hourly or retainer model. AI-assisted development significantly compresses delivery hours — price should reflect actual effort, not legacy rate cards.

**Undefined:** The specific use case, scope, and timeline are unknown — pricing cannot be estimated until the call discovers these. See [Meeting Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/brilliant-labs/meeting-agenda-client-call) for the call's discovery questions.

---

## Source

- **WhatsApp:** Client → Marius, 2026-03-25 (forwarded to David)
- **Developer docs:** https://brilliant.xyz/pages/developers
- **Developer Discord:** https://discord.gg/quUNRT6tnG
- **GitHub org:** https://github.com/brilliantlabsAR
- **Noa AI backend:** https://github.com/brilliantlabsAR/noa-assistant
- **Session:** conversation 349ad830-366b-4713-b827-f2897a20fdbf

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

