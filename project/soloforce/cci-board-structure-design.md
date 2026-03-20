---
publish: true
---

# CCI Board Structure - Position Interaction Model
[[wilsch-ai-internal]]

Design doc for how the Claude Code Improvements board is structured around positions as the organizing axis, replacing flat issue sprawl with position epics and observation-based evidence collection.

---

## Problem Statement

The CCI board lacks an organizing principle. Issues are created flat — one per observation — without hierarchy or position association. Over 4 triage sessions (210 issues), this produced sprawl where related observations scattered across standalone issues. Patterns are invisible. Prioritization by position is impossible. The current tooling (flag-for-improvement) always creates new issues, reinforcing the flat structure.

**Preconditions:**
- Multiple positions (Developer, System Engineer, JA) submit to the same CCI board
- Observations arrive continuously from sessions, transcripts, and reviews
- No structural rules exist for grouping or routing observations
- flag-for-improvement has no "comment on existing issue" mode

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Every CCI observation has a position home and implicit state through the release cycle |
| **Success** | New observations route to position epics via AI-suggested routing. Existing labeled issues converted to observation comments. flag-for-improvement supports comment-on-epic mode. |
| **Done test** | Can I write a meeting agenda with open design questions about CCI board structure? If NO → design is complete |

---

## Approach

### 1. Organizing Axis

Deliverable-tracking organizes by **project** — each client gets a label, issues group under it. CCI organizes by **position** — each E-Myth position gets a long-lived epic, observations group under it.

Position is the organizing axis of CCI. Every observation, every improvement, every release ties back to which position it serves. The triage sessions (4 sessions, 210 issues) created position labels with a position × scope pattern (`traceline/developer`, `internal/system-engineer`, etc.) that served the triage pass as a classification tool.

**Three position epics** map 1:1 to position agreements: Developer, System Engineer, Junior Architect. The scope dimension (traceline vs internal) is not a structural axis — it is a deployment stage. All observations land on position epics without scope differentiation. The distinction between "traceline-relevant" and "internal R&D" emerges when the System Engineer selects observations for a release epic and when fixes are judged stable enough to deploy downstream (R&D → team → Traceline product). Scope is a stability judgment made at deployment time, not a label applied at capture time.

**Routing rule:** Observations route to the position epic that owns the affected artifact. A JA protocol defect routes to the JA epic — even if the System Engineer will fix it — because the artifact belongs to the JA position. The Position Agreements define the accountability boundaries that govern this routing.

### 2. Observation Model

Observations are evidence captured as comments on position epics. The model adapts the surface-agnostic pattern from the Evidence Chain System (#851):

```
Free-form observation → AI resolves evidence + suggests position epic → User confirms → Comment posted
```

Observations are not issues — they have no state. They are context that makes behavior problems visible to AI. The System Engineer PA (Standard #2) established the principle: "Evidence must be captured and connected to an issue at flagging time." The position epic IS that issue.

An observation has two parts:
- **Context (the hook):** What was observed — the entry point that tells the System Engineer where to look in the source. Always problem-based, never solution-based. The source conversation contains noise; the hook cuts through it by naming the specific behavior problem. Without a problem-framed hook, the source is a pond with no indication of what fish to catch.
- **Source (the pond):** Conversation path, transcript URL, or commit link — the full evidence where the AI pinpoints the problematic behavior and, through thinking traces and tool usage patterns, diagnoses WHY it happened. The source gives the AI a first-person view of its own reasoning — something the human cannot express from their third-person perspective.

This format was validated live during the extraction session — an output format observation was posted to CCI #589 as a comment instead of creating a new issue.

**Format standard.** Each observation comment follows a three-part structure that mirrors the `/issue-comment` pattern:

```
## Observation: [short problem name]
[1-2 sentences — the problem frame not found in the source itself.
The hook adds semantic meaning the source doesn't carry: naming the
behavior problem so the System Engineer knows what to look for.]

## Source
[conversation path — auto-resolved from current session]

## Origin
[traceline/beta-user | internal/team | internal/marius — auto-resolved
from session context]
```

**Constraints:**
- One observation = one source. If the same behavior appears in three conversations, that is three observations. The accumulation on the position epic IS the pattern signal.
- The hook names the behavior problem — not which standard was violated. Standard mapping is diagnosis work (System Engineer), not capture work (observer). The problem name implicitly signals the domain.
- Problem-based enforcement happens through prompt guidance in the capture skill. The AI is always the formatter — whether the observer is a beta user, a team member, or Marius. The skill prompt contains examples of good hooks (behavior-naming) and bad hooks (solution-proposing).
- The origin field gives the System Engineer the prioritization signal: observations from Traceline users carry product urgency; internal observations follow the normal accumulation cycle.

**Undefined:** The origin auto-resolution mechanism — how the capture skill determines whether an observation comes from a Traceline user, a team member, or Marius. Candidates: GitHub username (github.actor from workflow dispatch), project context (soloforce = internal, plugin = traceline), or explicit user input. → Meeting agenda topic.

**Undefined:** Observation routing UX — when multiple targets exist (position epic, release milestone themes, tooling issues), how does the AI narrow candidates and present options? The two-question protocol (destination + classification) establishes the logic, but the practical "which of these 5 issues?" experience needs design. Evidence: #623 (Witness Skill), #648 (Witness Ceremony Release), #605 (Dev Lead Position Epic) — all valid targets for the same observation domain.

**Undefined:** Post-mortem as release epic creation trigger — a second path to release creation alongside atmospheric pressure. At milestone end, a structured post-mortem discussion surfaces project-specific learnings that become release epics. This is a different methodology from observation accumulation and requires its own design thinking. Evidence: [Grooming + Shutdown Ritual 2026-03-17](https://app.fireflies.ai/view/01KKXKGJB20Q1K0XDHXMH77BVM) — ITA post-mortem discussion revealed decomposition and spike gaps.

### 3. Three-Level Hierarchy

The board has three structural levels:

| Level | Primitive | Lifecycle | Purpose |
|-------|-----------|-----------|---------|
| **Level 1** | Position epic (issue) | Permanent (never closes) | Collects observations as they arrive |
| **Level 2** | Release (milestone) | Time-bound (touchpoint date) | Groups themed work for a work cycle |
| **Level 3** | Theme (milestoned issue) | Short-lived (Working → Review → Done) | Unit of work — Session B fix + Session C verification |

**Flow:**
1. Observations accumulate on the position epic (Level 1) as comments
2. When ready to work: SE creates a release milestone and theme issues from observation clusters
3. Transfer relevant observations from position epic to theme issues as comments
4. Work each theme issue through the fix-and-verify cycle (Session B → Session C)
5. Theme issue closes when verified. Milestone closes when all themes close.

State tracking maps to existing board primitives:
- **On position epic** = unaddressed (backlog)
- **Theme issue milestoned** = being worked (flows through Work Board columns)
- **Theme issue closed** = verified done
- **Milestone closed** = release complete

**Accumulation threshold:** A release epic is not created for a single observation. Observations accumulate on the position epic like atmospheric pressure building. Each new observation adds context that makes the others more actionable — the AI gets a richer evidence base for diagnosis. When enough observations cluster around a coherent concern, the pressure crosses a threshold and a release epic forms to discharge them. The more observations, the better the fix — because explaining behavior problems is hard for humans, but pattern-matching across multiple evidence sources is where AI excels.

**Core principle — output vs. behavior:** AI text output is non-deterministic (the same greeting may use seven different phrasings). AI behavior is deterministic (the act of greeting always happens). If wrong behavior is 100% consistent, the inverse must hold: a fix can make right behavior 100% consistent. Observations capture behavior, not output. Multiple observations of the same wrong behavior confirm the pattern is behavioral — not output variance. This is why accumulation matters: one observation might be noise, but a cluster of observations pointing at the same behavior is a confirmed fix target. This is the bread and butter of the improve-system cycle.

Artifact grouping (which commands, skills, or protocols are affected) happens during release planning, not as a structural level. This keeps the hierarchy flat at two levels.

**Transfer mechanism:** When a release milestone is created, the System Engineer selects a cluster of observations from the position epic and transfers them to the corresponding theme issues: re-post each observation as a comment on its theme issue, then delete the original from the position epic. This keeps the position epic clean — only unaddressed observations remain.

**Position epic hygiene:** Position epics contain observations only — no extraction pass handoffs, session links, commit references, or work context. These artifacts belong on theme issues or in the conversation store. This separation ensures the position epic remains a clean signal of unaddressed behavioral patterns.

The System Engineer names the release milestone after the concern the cluster addresses (e.g., `[CCI-DEV] Developer Release 1`). Theme issues within the milestone are named after specific behavioral patterns. Each theme routes to execution — for instruction artifacts, this is `/improve-system` at theme level (Session B reads deduplicated conversations, diagnoses, fixes). See [/improve-system architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) Theme-Level Workflow.

### 4. Routing

When an observation is captured, routing determines which position epic receives it.

**Mechanism:** AI suggests, user confirms.
- The AI knows the active session's position (from onboarding context)
- Default route = current position's epic
- AI can suggest a different position epic based on observation content
- User confirms or overrides

This replaces the current model where flag-for-improvement creates standalone issues with no position association. The routing decision is lightweight — a handful of position epics, not 200+ open issues to search through.

### 5. Tooling Evolution

The primary tooling change replaces flag-for-improvement's capture function with a dedicated observation skill. The skill uses Claude Code's `context: fork` mechanism — when invoked, it spawns an isolated subagent that formats and posts the observation while the main conversation continues unblocked. Zero flow interruption.

**Capture interaction:**
1. User describes the behavior problem in natural language
2. AI recognizes the trigger and invokes the observation skill
3. Forked subagent auto-resolves: source (current session path), origin (session context), routing (problem → artifact → position epic)
4. Subagent formats observation (hook + source + origin) and posts as comment on the target position epic
5. Main conversation continues — user sees confirmation when the background agent completes

**Trigger mechanism (experiment):** The skill's discovery description contains trigger phrases. Whether the AI reliably catches these triggers mid-session is unproven — Track B (position epic creation + first real observations) will produce the evidence. If triggers prove unreliable, fallback is explicit invocation.

**Standalone issue creation** remains available for genuinely new concerns that don't belong to any position epic. This is a separate path — not the default.

**PA standard (new):** When a new position is created, a corresponding CCI position epic must be created immediately. This is a standard in the Position Agreement, not a separate tooling concern.

**Migration:** Existing labeled CCI issues are converted to observation comments on their position epics in a one-time pass. This is manual work driven by this conversation, not automated tooling.

### 6. Deployment Pipeline

The CCI improvement cycle follows a two-path model that mirrors the developer's Path A (spec-design) / Path B (spec-implement) split:

**Path A — Observation Collection.** Observations accumulate on position epics as comments. Themes do not exist at this stage — observations are unstructured evidence. The SE monitors accumulation, looking for clusters of observations that point at the same behavioral pattern. When enough observations cluster around a coherent concern, the SE creates a release epic and transfers the cluster. The transfer is the moment observations become structured into themes.

**Path B — Fix Cycle.** The release epic drives the fix-and-verify loop:

1. **Theme clustering** — SE names themes from transferred observations
2. **Session B** — `/improve-system` diagnoses from deduplicated conversations, applies fix. Session B comment posted on release epic with commit link. See [/improve-system architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) for the theme-level workflow.
3. **Session C** — Organic verification. The fixed behavior gets triggered naturally in real work sessions. The user — not the AI — is the observer. The AI does not know it is being verified (observer effect would compromise organic verification). When the user notices fixed behavior working (or still failing), they record a Session C marker on the theme issue.

   **Session C closing heuristic:** A theme is closeable when:
   - At least **1 organic Session C pass** is recorded (explicit evidence the fix works)
   - **No new observations** of the same behavioral pattern appear on the position epic since the Session B fix

   The observation protocol already captures bad behavior continuously. If the fix didn't work, new observations of the same pattern will land on the position epic — the absence of new bad observations is itself a passive "no fail" signal. One explicit pass plus observation stream silence = theme closeable.

4. **Theme closing** — SE reviews themes meeting the closing heuristic and closes the theme issue. The disposition comment records the Session C evidence and observation silence period.

5. **Release closing** — All theme issues in the milestone closed → SE closes the milestone. The milestone's progress bar (X/Y themes) provides at-a-glance status.

**Session C capture ceremony (needs spike):** A post-session prompt in the Claude Launcher asks the user about behavioral observations after each Claude session. The launcher survives Claude's exit (`subprocess.run` instead of `execvpe`), checks open release milestones, and presents a terminal prompt. Implementation details (config file vs dynamic lookup, UX) require a spike before building. See `~/.claude/lib/claude_launcher.py`.

**Observation routing and classification:** Every observation carries a session type marker:
- **Session A** (default) — new behavioral failure. Routes to position epic.
- **Session B** — diagnosis and fix. The git commit IS the Session B artifact (commit message contains diagnosis, fix, and issue reference). No separate observation format — `/improve-system` produces the commit directly.
- **Session C** — verification pass or fail on a known fix. Routes to theme issue.

The observation protocol asks two questions at capture time: (1) destination — AI suggests a target, user confirms or provides issue number; (2) classification — Session A or Session C. Session B is auto-detected from `/improve-system` context. The session type marker appears in the observation format:

```
**Session A** / **Session C: PASS ✅** / **Session C: FAIL ❌**

**Hook:** [behavior description]
**Source:** 🗒️ Session {id}
```

While a release milestone is open, new observations of the same behavior route to the theme issue as Session C evidence. After the milestone closes, new observations route to the position epic as Session A evidence for potential future clustering.

For instruction artifacts, conversations are the design material (not design docs or tracking.md). The JA design doc step does not apply — the SE runs `/improve-system` Session B directly against deduplicated conversations.

#### Forcing Function

CCI is internal work. It lacks the natural forcing function that drives deliverable-tracking (client meeting dates with financial and social consequences). Analysis against the [Stakes Visibility framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/stakes-visibility-forcing-function) — five drivers scored for CCI under two models:

| Driver | Founder-as-SE (Current) | Dedicated SE (Target) |
|--------|------------------------|----------------------|
| Financial Incentives | ⚠️ Indirect — capacity improvement not quantifiable | ✅ Direct — SE employment depends on performance |
| Social Consequences | ❌ Absent — no external stakeholder | ✅ Present — manager reviews SE work |
| Structural Clarity | ⚠️ Emerging — position epics exist, procedures undocumented | ✅ Clear — ops manual + milestones define expectations |
| Visibility | ⚠️ Solvable — board retrofit pending | ✅ Board shows SE progress to manager |
| Ownership | ⚠️ Competing hats — CCI loses to client work | ✅ Sole accountability — CCI IS the job |

Under the founder model, only one driver is partially active. Under the dedicated SE model, all five activate. The forcing function for CCI is the same mechanism that drives deliverable-tracking: an employment relationship where the manager is the client. The founder sets milestones, reviews output, and holds the SE accountable — structurally identical to how external clients create pressure for project work.

**Organizational transition (April 2026 hypothesis):** The current Developer (David) moves into the SE role. A new hire takes over Junior Architect and Developer positions. David's lived experience as a developer — having felt the system's friction firsthand — provides the domain knowledge for SE work. This transition also serves as a scaling proof: if a second person can drive system improvements, the SE role is validated beyond the founder, following the same proof pattern as the Developer role.

**Forcing function ranking — strongest to weakest:**
1. **Ownership** (foundation) — without a dedicated person, nothing else matters. This is why CCI doesn't move under the founder model.
2. **Structural clarity** (enabler) — the SE Operations Manual + defined milestones make the other drivers effective. Without documented procedures, the SE depends on founder guidance, recreating the bottleneck.
3. **Social consequences** (active pressure) — the manager reviews SE work against documented standards. This driver is limited by the manager's ability to judge SE output quality — the ops manual compensates by providing objective standards to evaluate against.
4. **Visibility** (amplifier) — the board makes SE progress or stagnation observable, strengthening social pressure.
5. **Financial incentives** (baseline) — present in any employment relationship, not differentiating.

**Interim model:** The founder holds the SE role ad hoc — while doing client work across multiple terminals, 1-2 terminal sessions are allocated to position/CCI work interleaved with project work. There is no dedicated time block. Signal amplification (observation counts visible during grooming, pass counters on release epics, dependency flags between release epics) exists but is not what drives the decision. In practice, the founder instinctively notices what needs attention throughout the day and responds — reactive, gut-feel allocation rather than forward-looking milestone planning. This is the opposite of how deliverable-tracking operates, where durable structure (milestones → client meetings → forward planning) creates a forcing function. CCI under the founder model has no equivalent durable structure — the same person operates in two fundamentally different modes depending on whether the work has an external forcing function.

**Target model:** A dedicated System Engineer whose sole accountability is the improvement loop. The [SE Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services) defines the work listing. The SE Operations Manual (this design doc's end deliverable) defines the procedures. The manager (founder) serves as the SE's client — setting priorities via a regular 1:1 cadence (weekly or biweekly), reviewing output against PA standards, and holding the SE accountable through the same milestone structure used in deliverable-tracking.

**Pressure threshold:** SE judgment, developed through practice. No formula — the SE learns when observation accumulation is sufficient for a release epic through experience. The "atmospheric pressure" metaphor describes the principle (accumulation → threshold → discharge); the SE's judgment is the mechanism. The E-Myth BDP Loop (Innovation → Quantification → Orchestration) applies: the SE develops the judgment through doing, measures outcomes, and eventually codifies patterns in the ops manual.

**Milestone strategy:** CCI milestones align to the SE's 1:1 cadence with the manager. Weekly or biweekly review sessions serve as the "client meeting" equivalent — the SE presents status, the manager sets priorities, and milestones scope the work between reviews. This is the same structure as deliverable-tracking milestones tied to client sync meetings.

**Undefined:** Stability gates — how fixes move between deployment stages (SE environment → team → Traceline product). This requires a behavioral testing infrastructure equivalent to what automated tests provide for code — a systematic way to verify AI behavior changes before and after deployment. This is a separate project from the board structure design, connected to Session C verification methodology. → Separate workstream.

### 7. Operator Epics

Position epics collect observations about AI position behavior — how the Developer AI, JA AI, or SE AI behaves. Operator epics collect observations about how a human operator works with their AI. The distinction matters because operators have a natural forcing function that position epics lack: the operator's daily work quality depends on the system working correctly.

The operator epic (#635 DaveX2001) is David's single collection point. He posts all observations here regardless of which position artifact is affected. This is deliberate — routing to the correct position epic at capture time creates friction for the operator. David should focus on flagging deviations, not classifying them. During grooming Step 4, the SE and operator review observations together to determine the root cause: is the deviation a human issue (operator doesn't yet understand the system), an AI issue (instructions are wrong), or both? This joint triage determines where the fix belongs.

**Why operator epics create urgency:** In deliverable-tracking, client meetings force work through milestones. In CCI, the operator's friction is the forcing function. When David flags "the AI skipped `make staging`," that's urgent because his next session will hit the same problem. The better the system, the better David performs, the less review Marius needs — directly advancing the March plan goal (Marius = manager only by March 31).

**Domain separation:** Operator epics and position epics collect different categories of observation. Operator epics capture human behavior — how the operator works with the system. Position epics capture AI behavior — how the AI positions behave. Release epics form where the observations accumulate: human behavior release epics on operator epics (e.g., training topics, process adjustments), AI behavior release epics on position epics (e.g., instruction artifact fixes).

When an operator flags a problem ("AI didn't push"), the SE triages root cause during joint review: human issue (operator doesn't yet understand the system), AI issue (instruction artifact defect), or both. Human issues stay on the operator epic. AI issues route to the position epic that owns the affected artifact. The triage determines which domain the observation belongs to — the domain determines where the release epic forms.

### 8. Board Infrastructure

The CCI board (GitHub Project P3) is dissolved. Its structural function is replaced by the existing two-board architecture from deliverable-tracking:

| Level | Primitive | Board | Lifecycle |
|-------|-----------|-------|-----------|
| **Position epic** | Issue | Commitments Board | Permanent (never closes) |
| **Release** | Milestone | — (groups theme issues) | Time-bound (touchpoint date) |
| **Theme** | Issue (milestoned) | Work Board | Short-lived (Working → Review → Done) |

**Why dissolve P3:** Position epics are long-lived commitments — the same category as client epics on the Commitments Board. Keeping them on a separate CCI board hides them from the capacity signal ("how many positions are we actively improving?"). The Commitments Board already separates long-lived commitments from short-lived work. A separate board creates a place nobody looks at — the same visibility problem as unmilestoned backlog items.

**Release as milestone:** Each release is a GitHub milestone on `DaveX2001/claude-code-improvements` (e.g., `[CCI-DEV] Developer Release 1`). Multiple release milestones can coexist — the SE picks which themes to work on. The milestone's touchpoint date is the SE's 1:1 cadence with the manager, following the same pattern as deliverable-tracking milestones tied to client syncs. The [ILR milestone lifecycle](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) applies directly: CREATION → FILLING → ACTIVE → TOUCHPOINT → CLOSING.

**Theme issues on Work Board:** Theme issues from CCI release milestones flow through the same Work Board columns (Backlog → Working → Review → Done) alongside deliverable-tracking sub-issues. CCI themes carry position labels for filtering. The Sprint view groups them by milestone — CCI milestones appear alongside client milestones. This is the same board, same grooming ceremony, same columns. No separate CCI workflow.

**Decomposition discipline:** Theme issues follow the same starting-point principle as deliverable-tracking sub-issues: create only the themes that can be worked now. Fix one theme, verify (Session C), then create the next. Do not create all themes upfront if the first theme's fix might invalidate assumptions in later themes. This principle is universal — it applies to both CCI themes and deliverable-tracking sub-issues. See [ILR: Starting-Point Sub-Issues](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router).

**Migration from P3:** Existing CCI issues fall into three categories:
- Position epics → move to Commitments Board
- Release epic issues (#604, #616) → convert to milestones, their sub-issues become milestoned theme issues
- Standalone CCI issues → triage during grooming (milestone or close)

**Undefined:** Spike issue format — timebox validation ("can it do X?") before decomposing further. Referenced in ITA post-mortem discussion but format not yet designed. The spike serves as the validation gate between decomposition batches.

---

## Source

- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/530036f4-94ab-419c-8100-745707068c77.jsonl
- **Session (extraction pass 2):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/bf020ba2-f183-4f52-93ac-3ad32b4a4b41.jsonl
- **Position Agreements:** [Developer PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services), [SE PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services), [JA PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/junior-architect-position-agreement-wilsch-ai-services)
- **Traceline Beta Scope:** [Developer Position Beta](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope)
- **Evidence Chain System:** DaveX2001/deliverable-tracking#851
- **JA Lifecycle Violations:** DaveX2001/claude-code-improvements#589
- **Flag-for-improvement gap:** DaveX2001/claude-code-improvements#363
- **CCI Triage Sessions:** DaveX2001/deliverable-tracking#819 (comments contain 4 session summaries)
- **Live validation:** CCI #589 comment (observation format success case)
- **Session (behavioral verification):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/823e5e47-f275-4f14-add7-48674d8328d9.jsonl
- **Session (extraction pass 3 — operator epics, board infrastructure, forcing function):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/37dd29a5-764c-4b9f-9f93-1c8913659419.jsonl
- **Board Retrofit Reference:** DaveX2001/deliverable-tracking#904 (closed — established ILR v2 pattern)
- **Operator Epic:** DaveX2001/claude-code-improvements#635 (full comment history analyzed)
- **ILR v2:** [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router)
- **Monthly Plan:** [Monthly Plan Feb 23 – Mar 31](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/monthly-plan-2026-02-23-to-2026-03-31)
- **GROOMING.md:** DaveX2001/deliverable-tracking/GROOMING.md (Step 4: Operator Observations Review)
- **Session (extraction pass 4 — deployment pipeline, forcing function, closing model):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/fd39d236-f976-468e-9e54-fad00dabab59.jsonl
- **Stakes Visibility:** [Stakes Visibility: Forcing Function](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/stakes-visibility-forcing-function) — five-driver framework for task prioritization
- **SE Position Agreement:** [System Engineer PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/system-engineer-position-agreement-wilsch-ai-services) — result statement, work listing, standards
- **Developer Operations Manual:** [Developer Ops Manual](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services) — Path A/B closing model reference
- **Live evidence:** #615 theme-by-theme review in this session — closing ceremony gap surfaced empirically
- **Session (extraction pass 5 — forcing function mechanism, David-as-SE hypothesis, driver ranking, milestone cadence):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/e8d8d641-093a-4e43-87a7-4290f40f9a28.jsonl
- **JA Position Epic (#589):** Observations analyzed for forcing function grounding — 20 most recent comments reviewed
- **Session (extraction pass 6 — board lifecycle, release-as-milestone, Session C closing, observation classification):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/c4c32879-9c6a-4d54-b915-2663e554b5c0.jsonl
- **Grooming + Shutdown Ritual (2026-03-17):** [Transcript](https://app.fireflies.ai/view/01KKXKGJB20Q1K0XDHXMH77BVM) — primary source for board lifecycle, decomposition discipline, spike concept, post-mortem ceremony
- **JA Release Epic (#604):** 20 observations reviewed for JA behavioral calibration (RESOLVE probing discipline)
- **Ship with Confidence:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) — reference for "when is done" parallel
- **Improve-System Architecture:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) — Session A/B/C model reference
- **Issue Lifecycle Router:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) — milestone lifecycle, starting-point sub-issues, decomposition discipline
- **Claude Code Hooks:** [Official docs](https://code.claude.com/docs/en/hooks) — SessionEnd/Stop hook feasibility for Session C ceremony
- **Claude Launcher:** `~/.claude/lib/claude_launcher.py` — post-session Session C prompt mechanism
