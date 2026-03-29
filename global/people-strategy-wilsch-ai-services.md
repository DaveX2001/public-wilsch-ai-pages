---
publish: true
---

# People Strategy - Wilsch AI Services

[[life-vision]]

How we communicate the idea behind Wilsch AI Services and create an environment where the right people thrive. Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 16.

**Scope:** The game, its rules, who it's for, and how we communicate it. Builds on all previous components.

---

## The Idea

**You don't engineer code. You engineer AI behavior.**

Everyone has access to a context window. What you *do* with that context window is what differentiates. We find progressively better ways for AI and humans to work together — making each context window produce maximum value.

**The promise:** Whenever you think AI could do something, this is your way to figure out if it could. The system makes imagination tangible. Possibility spaces become real.

**The reframe:** Claude gets better with every version — a better Formula 1 driver each release. We don't focus on the driver. We build the best Formula 1 car. The game is making the car better, not driving it faster.

**The company is one feedback loop.** Use the system → find friction → flag it → it gets improved → you get the better version → repeat. Each cycle makes everyone more capable. Each cycle unlocks possibility spaces that didn't exist before — things you didn't even know were possible until the previous friction was resolved.

> Like leveling up in a game. Level one feels complete. Then you level up, and there are things you didn't know existed. Each level is cooler than the last, because everything below already works.

---

## The Game

Two games run in parallel. Both feed each other.

| Game | Who Plays | What It Produces | Feeds |
|------|-----------|-----------------|-------|
| **Execution Game** | VP/Delivery + sub-positions (Developer, Architect, Dev Lead) | Delivered work for clients | Friction signals → Improvement Game |
| **Improvement Game** | System Engineer + COO | Better system for everyone | Better tools → Execution Game |

**The Execution Game:** Path A (spec-design) and Path B (spec-implement). Taking unclear requirements and turning them into working, deployed solutions. Operational.

**The Improvement Game:** Flag friction → diagnose → fix → release → everyone gets the better version. This is the company's core USP. The [claude-code-team-plugin](https://github.com/MariusWilsch/claude-code-team-plugin) is where the game materializes — each release is a completed cycle.

**How they connect:** Execution generates friction. Friction feeds improvement. Improvement makes execution better. The loop never stops.

**Everyone participates at different levels:**
- **Developer:** Uses the system, generates friction through use, gets better versions
- **System Engineer:** Takes friction signals, resolves them, releases improvements
- **The company as organism:** Progressively finds better ways to collaborate with AI

---

## Rules of the Game

Adapted from Gerber's 8 rules to Wilsch AI context.

### 1. The game comes first; what people do, second.

The improvement loop is more important than any single issue. The system's progressive improvement IS the business strategy. Operational work serves the game, not the other way around.

### 2. Never create a game you're unwilling to play yourself.

The founder plays all three games simultaneously:
- **Execution Game:** Delivers client work through the system daily
- **Improvement Game:** Flags friction, improves the system, releases better versions
- **Handoff (meta-game):** Building the infrastructure for others to play both games independently — the current cycle's focus

This isn't historical. It's present tense. The system was built by playing it, and it continues to be played. The goal is for others to play all three games too — not just the execution game. That's the whole point of the People Strategy: find people who want to play, not people who want to work.

### 3. Specific ways of winning without ending the game.

Each cycle through the loop is a win: flag → improve → release → better version. The game has infinite levels. Each level unlocks cooler friction than the last, because everything below already works.

### 4. Change the tactics, not the strategy.

Skills, hooks, and tools change constantly (tactics). The BDP loop — Innovation → Quantification → Orchestration — is sacrosanct (strategy). The pluggable architecture enables this: new skills auto-discover, new protocols auto-load, new hooks connect. Improve without reinventing.

### 5. Never expect the game to be self-sustaining.

The game materializes through visible artifacts:
- **Flag for improvement** lands in [CCI repo](https://github.com/DaveX2001/claude-code-improvements) — signal generated
- **Plugin release** ships — signal resolved, cycle completes
- **Developer gets better version** — they feel the loop working

These are the reminders. Every flag says "the game is alive." Every release says "the loop worked."

### 6. The game must make sense.

**Technical logic:** AI output is non-deterministic but AI behavior is highly deterministic. If wrong behavior is predictable, right behavior must be achievable. The system proves this daily.

**Economic logic:** Everyone has access to a context window. What you do with it differentiates. We extract maximum economic value from test-time compute.

**Human logic:** If you want purpose and growth, this can be it. The company offers an environment where curiosity is the operating principle. *"If you want this to be your purpose, it can be."*

**Market logic:** AI models improve every version (better drivers). The bottleneck is the system around them (the car). Companies that build better cars win.

### 7. Fun from time to time.

Rewards that enhance capability: additional terminals, better hardware, Claude Code seats, books that sharpen thinking. Things that help play the game better.

General approach: humor and lightness in daily interaction. The work is serious; the atmosphere doesn't need to be.

### 8. If you can't think of a good game, steal one.

Not applicable — this game was built from first principles through the BDP loop.

---

## Who It's For / Not For

### The Right Fit

| Signal | What It Looks Like |
|--------|-------------------|
| **Asks questions** | Reads the design docs and challenges them. Doesn't accept things at face value. "What I say may even be wrong." |
| **Curious about AI behavior** | Lights up about *how* AI works, not just *what* it produces. Reads AI output carefully. |
| **Drives own learning** | "I want to try this." Uses the system to trial-and-error quickly. Doesn't wait to be taught. |
| **Flags friction** | Notices when something doesn't work and says so. Uses `/flag-for-improvement` naturally. |
| **Imagines forward** | "Could I do this too?" Sees current friction as future possibility. |

**Cultural anchor:** Warren Berger's *[A More Beautiful Question](https://amorebeautifulquestion.com/)* — the power of inquiry to spark breakthrough ideas. If someone embodies this book, they're in the game.

### The Wrong Fit

| Signal | What It Looks Like |
|--------|-------------------|
| **Wants stability** | Uncomfortable with the system changing. Prefers things to stay the same. |
| **Pure executor** | Wants a task list, completes it, clocks out. No curiosity about why things work. |
| **Doesn't ask questions** | Reads docs and accepts everything. No challenge, no probing. |
| **Glances over AI output** | Treats AI as a black box. Not interested in the behavior behind the output. |

---

## The Initiation

How the game gets communicated to new hires. Adapted from Gerber's structured hiring process.

### Step 1: Read the Design Docs

The "scripted presentation" — self-service, not a speech.

**Reading order:**
1. Strategic Objective (what the business does and why)
2. Organization Chart (the structure)
3. People Strategy (this document — the game)
4. Developer Operations Manual (how the work flows)
5. Position Agreement (your accountabilities)

*Note: Primary Aim is the founder's personal document. Not shared with new hires.*

### Step 2: PrinciplesYou Assessment

[PrinciplesYou](https://principlesyou.com/) personality assessment (Ray Dalio). Results fed to AI over time to build operator fit profiles. Currently exploratory — data collection phase.

### Step 3: Questions Meeting (THE Test)

Individual meeting to discuss what they read. This is the single most important step.

**The test is simple:** Did they come with questions? Not polite questions. Real questions — about the design docs, about the principles, about how things work and why. How they assessed these documents and what they ask about them tells you everything.

**Pass:** They probe, challenge, wonder aloud. They say things like "Why does this work this way?" or "Could I do this too?" or "What if we tried...?" They engaged with the material and formed their own view.

**Fail:** They read it, accepted it, and have nothing to say. No intrigue. No friction with the ideas. They're waiting to be told what to do.

**The framing:** *"You don't engineer code here. You engineer the behavior of AI systems. Building the machine that builds the machine. If that's interesting to you, you're in the right place."*

### Step 4: Jump Into Issues

Training method from Strategic Objective kicks in:
- Google Meet together (camera off, voice off by default)
- Operator does issues themselves; comes to founder with friction
- Founder is the "improved system" during this phase — helps with concepts
- Up to 25 issues runway; should click by 10

### Step 5: Observe Signals

During the training period, watch for:
- Are they flagging for improvement? (feeding the loop)
- Are they reading AI output carefully? (caring about behavior)
- Are they asking "could I do this too?" (imagination + will)
- Are they trying 3 approaches before escalating? (driving own learning)

---

## Management System Hierarchy

Four layers that define how people operate within the game.

| Layer | Question | Current State | Reference |
|-------|----------|---------------|-----------|
| **How We Do It Here** | What's the work? | ✅ Documented | [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services) |
| **How We Recruit, Hire, and Train** | How do people join? | ⚠️ Partial | Training method in [Strategic Objective](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services); Initiation (this doc); hiring/recruiting TBD |
| **How We Manage It Here** | How do we oversee? | ⚠️ Partial | Grooming, review labels, [Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services); management system TBD |
| **How We Change It Here** | How does the system improve? | ⚠️ Partial | [/improve-system Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture); deeper formalization TBD |

---

## Related Documents

- [Primary Aim - Life Vision](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)
- [Strategic Objective - Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Organization Chart - Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- [Organizational Strategy Lifecycle](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organizational-strategy-lifecycle)
- [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services)
- [Developer Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services)
- [/improve-system Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture)

---

## Source

- E-Myth People Strategy interview (2026-02-06)
- Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 16
- Cultural reference: Warren Berger, *A More Beautiful Question*
- Assessment tool: [PrinciplesYou](https://principlesyou.com/) (Ray Dalio)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

