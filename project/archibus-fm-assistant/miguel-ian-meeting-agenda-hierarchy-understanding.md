---
publish: true
---

# Meeting Agenda: Step 1 - Hierarchy Understanding
[[client-archibus]]

## Meeting Goal

By the end of this session, we should have:

1. **Process clarity** - How you determine hierarchy today
2. **Variation handling** - How to choose between hierarchy options
3. **Challenge awareness** - Potential issues in this process

## Pre-Read

Please review the design document before the meeting:

[Chain 1B: AI Bulk Data Entry - Design Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)

---

## Discussion Topics

### 1. Context Recap

We're building AI-assisted bulk data entry. The 3-step process is:
1. **Understand hierarchy** ← This meeting
2. Map input schema to Bruce BEM schema
3. Fill level-by-level

This meeting focuses on Step 1: how does AI understand which hierarchy a client uses?

### 2. Current Process

> "Create in a kind of screen all these cards and you put there the cards floor, building, room, suite - all the ones that exist in our type of assets - and you invite the user first to organize their own hierarchy."

How have you determined hierarchy in past implementations?

### 3. Handling Variations

The hierarchy options are defined (9-level max: Campus → Site → Complex → Property → Building → Floor → Stairwell → Suite → Room), but clients use different subsets.

We want to understand:
- How AI should present hierarchy choices
- What makes one option "right" for a client vs another
- What signals indicate which levels a client uses

### 4. Potential Challenges

What challenges exist in determining hierarchy?

---

## Meeting Format

- **Type:** Discovery session
- **Expectation:** One-off — we gather your input, then proceed independently with Rein
- **Outcome:** Enough understanding to design AI's hierarchy capture flow

---

## Related

- **Issue:** [#373 - AI Bulk Data Entry Prototype](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Schema Reference:** [Asset Import Schema (SharePoint)](https://aschs-my.sharepoint.com/:x:/g/personal/rein_suurvali_asc-hs_com/IQCZWnERRmHxRb3r4bb3BZzRAfjwreav4FEP85btLlaVWNI)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

