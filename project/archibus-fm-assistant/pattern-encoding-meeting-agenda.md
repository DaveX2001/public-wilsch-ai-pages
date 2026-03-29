---
publish: true
---

# Pattern Encoding Meeting Agenda - Assets Block
[[client-archibus]]

**Purpose:** Apply Pattern Encoding methodology to Assets block
**Duration:** ~65 minutes
**Attendees:** Marius (facilitator), Rein, Ian, Ali, Mujahid

**Related:** <img src="pattern-encoding-methodology.png" width="100%" alt="Pattern Encoding Infographic">

---

## Section 1: Methodology Introduction (3 min)

### What you SAY (External)

**1.1 Open with the image (30 sec)**
- Share screen: Pattern Encoding infographic
- *"Before we dive in, I want to show you how we'll structure today's session"*

**1.2 Walk through the methodology (1.5 min)**
- Point to Step 1 (Pain):
  *"First, we identify pain points. Ian said last meeting: 'The biggest challenge is to enter the data.' There are 5 different ways to create an asset in the system - users don't know which is right, and most lead to data quality issues."*
- Point to Step 2 (Workflow):
  *"Which workflow? Asset creation."*
- Point to Step 3 (Best Practice):
  *"What's the right way? There IS a correct way - the one that ensures data quality. FM experts know it. We capture that pattern."*
- Point to Step 4 (Encode):
  *"AI learns the one right way. User just says 'create this asset' - AI handles navigation, ensures correct fields, enforces best practice. Data quality problem solved."*

**1.3 Set the scope (1 min)**
- *"Today we're applying this to the Assets block only"*
- *"We'll repeat this methodology for Contracts, Works, Costs in future sessions"*
- *"Goal: Come out with 3-4 encoded patterns for Assets"*

---

## Section 2: AI Capabilities Recap (2 min)

### What you SAY (External)

**2.1 What AI should do (High-level principles)**

| Principle | Description |
|-----------|-------------|
| **Navigator/Guide** | Reduce navigation complexity - AI knows where things are |
| **Hybrid Model** | Chat + visual UI together (see: `adr-novnc-docker-hybrid-environment.md`) |
| **Best Practice Enforcer** | Ensure correct way, every time |
| **Platform-Agnostic** | Works at Mendeleev FM level, not hardcoded to one software |
| **Prediction/Analytics** | Budget forecasting, asset status reporting (Smart Insight product) |

**2.2 What AI should NOT do (Boundaries)**

| Boundary | Reason |
|----------|--------|
| Replace visual UI entirely | Some things need forms/visual |
| Handle everything in chat | Complex inputs need hybrid |
| Be software-specific | Defeats platform value |

*"Check duplicates" and "route to team" are examples of Best Practice Enforcer - patterns we'll encode today.*

---

## Section 3: Pain Points Discovery (15 min)

### What you SAY (External)

**3.1 Frame the question (1 min)**
- *"Now we apply Pattern Encoding to Assets. Step 1: Pain Points."*
- *"Where does it hurt in Assets? What frustrates you or your clients?"*

**3.2 Facilitated discussion (10 min)**
- Share screen with Word/Notes open
- Type pain points as FM experts mention them
- Simple bullet list format

**3.3 Prioritize (4 min)**
- *"Which 3-4 of these hurt the MOST?"*
- Quick verbal consensus
- Output: Ranked list of top 3-4 pain points

### Internal prep (don't say, just know)

- Let FM experts talk, you capture
- Ask "Why is this painful?" to deepen
- Don't solve yet - just identify
- Expected (from Dec 11): data quality, navigation, duplicates

---

## Section 4: Workflow Mapping (20 min)

### What you SAY (External)

**4.1 Connect pain to workflow (2 min)**
- *"Now Step 2: Which workflows connect to these pain points?"*
- Point to the list from Section 3 (their words, not yours)

**4.2 Map each pain point (15 min)**
- For each pain they identified: *"Where does this happen? What workflow?"*
- You ask, they answer, you capture

**4.3 Confirm mapping (3 min)**
- Read back the Pain → Workflow mapping
- *"These are the workflows we'll focus on. Correct?"*

### Internal prep (don't say, just know)

- You know from Dec 11 they'll likely mention data quality → asset creation
- But let THEM say it - don't lead

---

## Section 5: Best Practice Extraction (20 min)

### What you SAY (External)

**5.1 Frame (2 min)**
- *"Step 3: What's the best practice for each workflow?"*
- *"Walk me through how this should be done right."*

**5.2 Listen and capture (15 min)**
- Let FM experts explain
- Ask clarifying: *"And then what happens?"*
- Capture their words in Word

**5.3 Validate (3 min)**
- *"If AI follows these steps, does that work?"*

### Internal prep (don't say, just know)

- Deeper probes if they get stuck:
  - "What do experts know that novices miss?"
  - "What's the common mistake?"
- Expected: They'll describe step-by-step (verify exists → select type → fill fields → assign location)
- You translate their explanation into encodable pattern later

---

## Section 6: Wrap-up (5 min)

### What you SAY (External)

**6.1 Summarize outputs (2 min)**
- *"Let me read back what we captured today:"*
- Pain points identified
- Workflows connected
- Best practices for each

**6.2 Confirm encoding scope (2 min)**
- *"These patterns will be encoded into the AI."*
- *"Rein will build the APIs, I'll build the AI logic."*
- *"Any concerns before we proceed?"*

**6.3 Next meeting preview (1 min)**
- *"We'll repeat this methodology for [Contracts/Works] next."*
- *"Thanks everyone."*

### Internal prep (don't say, just know)

- Have clear action items ready:
  - You: Translate patterns to AI tool specs
  - Rein: API requirements for each workflow
  - FM experts: Available for clarification if needed
- This meeting is about WHAT to encode, not HOW to implement

---

## Pattern Encoding Methodology Summary

```
Pain Point → Workflow → Best Practice Solution
           ↓
    Encoded into AI
           ↓
    Solved once, works everywhere (via Bruce BAM connectors)
```

**Meeting questions:**
1. *"What's the pain?"* (data quality, navigation complexity, duplicates)
2. *"What workflow does this live in?"* (asset creation, work request, cost entry)
3. *"What's the best practice way to solve it?"* (the pattern to encode)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

