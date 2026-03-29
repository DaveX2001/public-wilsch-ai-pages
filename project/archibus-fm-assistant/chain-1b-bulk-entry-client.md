---
publish: true
---

# Chain 1B: AI Bulk Data Entry - Alignment Document
[[client-archibus]]

## Problem

Partners spend days reformatting client Excel for Bruce onboarding. This is low-margin, repetitive work that holds up implementations. Data must follow strict hierarchy order (site → building → floor → room) due to foreign key dependencies.

## Success Definition

> **AI fills background tables where confident, asks where uncertain, flags what it cannot resolve** - using data validation as the feedback loop for self-correction.

Think of it like spell-check for data structure: red squiggles appear when data doesn't fit, with suggestions for how to fix it.

**Goal:** Speed up the process, not achieve 100% perfection. If we can reduce partner onboarding time, that's success.

## How It Works

<img src="chain-1b-flow.png" width="600" alt="Chain 1B Flow">

1. **Client uploads messy Excel** - inconsistent columns, varying naming conventions
2. **AI maps columns to Bruce schema fields** - fuzzy matching, semantic understanding
3. **Schema accepts or rejects** - with actionable reason for each rejection

When AI is uncertain about a mapping, it asks a clarifying question rather than guessing wrong.

## What the Demo Shows

End-to-end flow demonstrating:

1. **Input:** Messy client Excel (like what Mujahid showed from Qatar Museum)
2. **Processing:** AI validates against Bruce schema, asks clarifying questions
3. **Output:** Data imported into background tables + actionable rejection list for human decisions

The wow factor: "Here's messy data. Watch AI put it into Bruce."

---

*Synthesis of Dec 29 + Dec 30 meetings.*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

