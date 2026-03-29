# PERIAN Session Decisions (2025-12-21)
[[traceline]]

---
publish: true
status: partial
source: 2-hour working session with Christoph
transcript: yce-egwc-yhc-transcript-2025-12-21T17-11-55.997Z.srt
---

## 1. Target Customer

**Decision:** Developers working alone or in very small teams (2-10 people)

**Why developers:**
- Company-wide Claude Code solutions don't exist - everyone builds their own local setup
- Claude Code / CLI adoption is far lower than expected
- Tools are "cutting-edge" only on personal level, not organizational
- Teams lack shared, persistent workflows
- Developers as GTM wedge because:
  - We know them (we ARE them)
  - Fast feedback loops
  - Binary success criteria (code works or doesn't)
  - Narrow, controlled ecosystem (Git, IDE, terminal)

**NOT initially:**
- Enterprise (too slow feedback, too many stakeholders)
- Creative/Marketing (too subjective, long feedback loops)
- Individual hobbyist coders (don't pay, high support)

**Later expansion:** Research, Financial Analysts (structured output, binary quality)

---

## 2. Core Job to Be Done

**Job:** Frictionless code production

**Positioning:** We compete on least friction, not best AI.

**Key insight:** "Worse at beginning → best after use"

**Metaphor:** "We don't build an opinionated framework. It becomes YOUR opinionated framework."

**What this means:**
- The deliverable IS code
- We don't compete on "better AI" - we compete on better execution through interaction
- Core metric: "Teach AI once, never again"

**User pain points we address:**
- "It keeps forgetting stuff"
- "I have to re-explain everything"
- "Every setup is fragile"
- "CLI friction is too high"

**What we offer:**
- AI adapts over time
- No initialization ritual
- No claude.md tinkering required
- Only feedback needed from user

---

## 3. Core Hypotheses

### H1: Fast Feedback Loops > Model Intelligence

**Statement:**
> Fast feedback loops that build YOUR context + AI that adapts to that context beats smarter models with generic knowledge. The user just flags—the system engineers the context.

**What we DON'T do:**
- Wait for smarter models from Anthropic/OpenAI
- Compete on model capability
- Require users to learn Claude Code internals (hooks, skills, protocols)

**What we DO:**
- Build a system where user feedback rapidly improves the AI's context
- Create a "playbook" (skills, hooks, protocols) that adapts to YOUR patterns
- Make the AI able to apply that context at the right time
- Abstract away complexity: user flags → system creates appropriate artifact

**What USER doesn't need to know:**
- How to write skills, hooks, protocols
- Claude.md syntax
- CLI internals
- Which extension type to use

**What SYSTEM handles:**
- Flag → appropriate artifact translation
- Context routing to right extension type
- Progressive improvement without user expertise

**Why it wins - The UV/pip Example:**

Smart Model + Generic Context (FAILS):
```
Session 1: AI uses pip → Error
Session 2: You explain uv → AI uses uv pip (still wrong)
Session 47: "WHY ARE YOU STILL USING PIP"
Result: You're the teacher. Forever.
```

Dumb Model + YOUR Context + Adaptation (WINS):
```
Session 1: AI uses pip → You /flag-for-improvement
System: Creates hook blocking pip
Session 2: AI tries pip → Hook blocks → AI uses uv run
Session 47: AI automatically uses uv run
Result: You taught once. Done.
```

**Reference:** [Agentic Context Engineering (ACE)](https://cobusgreyling.medium.com/agentic-context-engineering-5021cc06751c)

---

### H2: Scale AI for Test-Time Compute

**Statement:**
> We are the Scale AI of AI adoption - but for test-time compute, not pre-training. Users pay us while generating data that improves inference-time context.

**The Distinction:**

| | Scale AI | PERIAN |
|---|----------|--------|
| **Target** | Pre-training | Test-time compute |
| **Output** | Labeled datasets | Context/skills/playbooks |
| **When applied** | Before deployment | During inference |
| **Improves** | Model weights | Model context |

**What we DON'T do (test-time compute framing):**
- Hope the model "figures it out" at inference
- Rely on generic prompting strategies
- Accept static context that doesn't improve

**What we DO:**
- Engineer context that improves at inference time
- Build skills/hooks/playbooks used during test-time
- Users pay → we get better at context engineering
- Each company benefits from continuously improving system

**Why it wins:**
```
Generic inference: Same context → same limitations forever
PERIAN inference: Evolving context → better outcomes over time
```

Users generate improvement data while doing their actual work - not as a separate task.

**Sources:**
- [Scale AI Wikipedia](https://en.wikipedia.org/wiki/Scale_AI)
- [Test-Time Compute - HuggingFace](https://huggingface.co/blog/Kseniase/testtimecompute)
- [RAND - Test-Time Compute](https://www.rand.org/pubs/commentary/2025/03/when-ai-takes-time-to-think-implications-of-test-time.html)

---

### H3: Binary Feedback > Complex Learning

**Statement:**
> Binary signal + context extraction beats complex labeling. The conversation transcript IS the labeled data.

**The Problem:** Traditional improvement requires users to LABEL what went wrong:
- "Rate 1-10"
- "Explain the issue"
- "Describe expected behavior"

**Our Solution:** Binary signal + context extraction:
```
Session A: User works → issue occurs → /flag-for-improvement
Session B: System reads Session A conversation → diagnoses → fixes
Session C: User verifies
```

**What we DON'T do:**
- Ask users to explain the problem in detail
- Require structured feedback forms
- Make users do the labeling work

**What we DO:**
- Binary signal: "this needs fixing"
- Conversation context IS the label
- System extracts the "what/why" from the session

**Why it wins:**

| Approach | User effort | Data quality |
|----------|-------------|--------------|
| Complex labeling | High (user explains) | Inconsistent (depends on user) |
| Binary + context | Low (one click) | High (full conversation captured) |

**Key insight:** The conversation transcript IS the labeled data. User just points at it.

**Reference:** [Three-Session Instruction Artifact Improvement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-instruction-artifact-improvement)

---

## 4. Our Wedge

Three angles for the same core differentiator:

| Angle | Statement |
|-------|-----------|
| **Flywheel** | We are Scale AI for test-time compute—users pay while generating data that makes their AI better at inference, not training. |
| **Adaptation** | A dumb model with YOUR context beats a smart model without it—we build the system that creates YOUR context with minimal input. You don't need to know how to improve it. |
| **Friction** | Flag once, never explain—users point at problems, the system reads the conversation and fixes itself. You flag, we engineer. |

---

## 5. Open Items (Requires Separate Session)

These topics were discussed but require dedicated reasoning with fresh context:

### Session Flow
- Two-way learning: AI adapts to you + AI teaches you
- Execution Mode vs Improvement Mode
- Verification mechanism (how to know fix worked?)
- Replay mechanism for testing improvements

### Skills vs Plans
- Plans = figuring out WHAT to do
- Skills = executing HOW to do it
- Promotion rule: when does plan become skill?
- System handles promotion, not user

### Tech Stack
- Claude Code vs TweakCC vs Open Code
- API billing vs subscription (Claude Max)
- Anthropic Agent SDK dependency concern
- Model flexibility long-term

### MVP Scope
- What's in-scope / out-of-scope
- Hypothesis validation approach
- Build vs validation plan

### Next 30 Days
- Concrete build plan
- Validation milestones

---

## Session Metadata

**Date:** 2025-12-21
**Duration:** ~2 hours
**Participants:** Marius, Christoph
**Transcript:** `yce-egwc-yhc-transcript-2025-12-21T17-11-55.997Z.srt` (Google Drive)
**Conversation Path:** `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-WILSCH-AI-INTERNAL--soloforce/29abd834-c4d0-44ab-864d-d6fdf68a77e5.jsonl`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

