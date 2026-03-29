---
publish: true
---

# FMM Doha - AI Feature Scope Alignment
[[client-archibus]]

**Attendees:** Mujahid, Marius

---

## Meeting Goal

Align on what AI features to include in the FMM Doha quotation AND clarify the 3 unclear MoSCoW items.

By the end of this session, we should agree on:
1. Which features go in the FMM proposal
2. Resolution of the 3 ambiguous requirements
3. Next step (Rein meeting for API feasibility)

---

## Pre-Read

Please review this document before the meeting:

[FMM Feature Scope Classification](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/fmm-doha-feature-scope-classification)

---

## Discussion Topics

### 1. Context Recap

Brief reminder of FMM scope and constraints:
- **Use case:** Soft services / cleaning at Doha Airport
- **Core workflow:** Report problem → ticket → assign → execute → complete
- **User:** CAFM operator (single point person)
- **Timeline:** End of March (~2 months)

### 2. Classification Walkthrough

Present the 5 categories from the classification document:

| Category | Count | What it means |
|----------|-------|---------------|
| **Implemented** | 1 | Works today - chat-based work requests |
| **Tangent** | 5 | Buildable within 2 months (tracked under #605, #373) |
| **Smart Insight** | 10 | Future direction - analytics layer not yet defined |
| **Untouched** | 2 | Different domain, beyond current scope |
| **Unclear** | 3 | Need your input to classify |

### 3. Clarify Unclear Items

Three requirements need clarification:

**MH3: Auto-Population of Mandatory Fields**
> Do you mean during **bulk import** (AI fills trade/criticality/PM when importing client's Excel - same pattern as #373) or during **ongoing creation** (AI suggests these when someone manually creates a single asset)?

**SH1: Duplicate Asset Detection**
> Do you mean during **bulk import** (detect "AHU-01" and "Air Handling Unit 1" as same asset before inserting) or **ongoing scan** (periodically check live database for duplicates created over time)?

**CH1: Manufacturer & Model Intelligence**
> Do you mean during **bulk onboarding** (parse spec sheets once to fill all assets) or **ongoing inference** (suggest manufacturer from similar existing assets)?

### 4. Quotation Scope Agreement

Based on classification and clarifications:
- **Definitely in:** Implemented (MH8) + Tangent items
- **Not in 2-month scope:** Smart Insight, Untouched
- **Depends on your answer:** The 3 unclear items

**Question:** Does this scope make sense for the FMM quotation?

---

## Decision

1. **Scope:** Which items are included in the FMM quotation?
2. **Unclear items:** Bulk vs ongoing for MH3, SH1, CH1?
3. **Next step:** Confirm Rein meeting for API feasibility

---

## Meeting Format

- **Type:** Working session - review and decide
- **Duration:** ~45 minutes
- **Expectation:** Review the classification doc beforehand

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

