---
publish: true
---

# AI Work Track Prioritization - Meeting Agenda
[[client-archibus]]

**Attendees:** All-hands (full team)

---

## Meeting Goal

**Core questions:**
1. For each AI work track: kill, pivot, or continue?
2. Prioritization: what order should we tackle them? (1st, 2nd, 3rd, 4th)

---

## Read More

- [Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
- [Auth Approaches](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/auth-approaches-comparison)
- [Asset Code Generator Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/asset-code-generator-design)
- [NoVNC Hybrid Environment](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/adr-novnc-docker-hybrid-environment)

---

## Discussion Topics

### 1. AI Bulk Data Entry (#373)

**Stage:** Design progressing (core work)

3-step process: understand hierarchy → map input schema → fill level-by-level. Design doc updated with concrete schema.

**Missing:**
- Step 1 (Hierarchy): Methodology for defining client hierarchy + who should I talk to
- Step 2 (Mapping): Real input data samples + FM expert to validate AI's mapping
- Step 3 (Fill): Insertion API

**Decision:** Assign resources for each missing item

### 2. Asset Code Generation (#605)

**Stage:** Design defined, meeting held

AI generates equipment codes from client data. Tangent to #373 — if we continue with bulk entry, #605 becomes a subset of that work.

**Decision:** Kill / Pivot / Continue

### 3. Standalone Authentication

**Stage:** Iframe works, standalone blocked

Current iframe approach works (Bruce BEM passes tokens). Standalone OAuth blocked by Code on Time framework. Approach C (Tiny Auth App) proposed as workaround.

**Decision:** Continue with Approach C / Stay with iframe / Kill standalone

### 4. NoVNC Hybrid Environment (#628)

**Stage:** Prototype ready

AI navigates Chrome visually so users see actions happen. Split-screen wrapper combines chat + VNC view.

**Decision:** Kill / Pivot / Continue

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

