---
publish: true
---

# Meeting Agenda: Step 3 - Insertion API Design
[[client-archibus]]

## Meeting Goal

Establish shared understanding of how the insertion API will work. By the end of this session, we should have:

1. **API design clarity** - What the insertion endpoint accepts and returns
2. **Error response design** - Information needed for backpressure/self-correction
3. **Rollback approach** - How to handle failures mid-import

## Pre-Read

Please review the design document before the meeting:

[Chain 1B: AI Bulk Data Entry - Design Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)

---

## Discussion Topics

Starting points for discussion, not limited to these:

### 1. Context Recap

Step 3 in the 3-step bulk data entry process:
1. Understand hierarchy (Step 1)
2. Map input schema (Step 2)
3. **Fill level-by-level** (Step 3) ← This meeting

Step 3 populates background data level-by-level (parents before children).

### 2. Insertion API

How the insertion endpoint works.

### 3. Error Responses (Backpressure)

From [Don't Waste Your Backpressure](https://banay.me/dont-waste-your-backpressure/): good error messages enable automated self-correction.

How error information supports self-correction.

### 4. Checkpoint/Rollback

How to handle failures mid-import.

### 5. Challenges

Challenges in building this insertion flow.

---

## Meeting Format

- **Type:** Design alignment
- **Expectation:** Ongoing collaboration - this is the first of regular syncs as the API develops
- **Outcome:** Enough understanding to create Step 3 design doc

---

## Related

- **Issue:** [#373 - AI Bulk Data Entry Prototype](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Schema Reference:** [Asset Import Schema (SharePoint)](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

