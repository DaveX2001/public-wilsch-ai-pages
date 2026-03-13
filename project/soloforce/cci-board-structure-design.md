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

### 3. Two-Level Hierarchy

The board has two structural levels:

| Level | Type | Lifecycle | Purpose |
|-------|------|-----------|---------|
| **Level 1** | Position epic | Permanent (never closes) | Collects observations as they arrive |
| **Level 2** | Release epic (sub-issue) | Time-bound (created → worked → closed) | Groups observations selected for a work cycle |

**Flow:**
1. Observations accumulate on the position epic (Level 1) as comments
2. When ready to work: create a release epic as a sub-issue of the position epic (Level 2)
3. Transfer relevant observations from position epic to release epic
4. Work the release epic to completion, then close it
5. Remove transferred observations from the position epic

State tracking is implicit — no marking mechanism needed:
- **In position epic** = unaddressed (backlog)
- **In release epic** = being worked on (in-progress)
- **Release epic closed** = done

**Accumulation threshold:** A release epic is not created for a single observation. Observations accumulate on the position epic like atmospheric pressure building. Each new observation adds context that makes the others more actionable — the AI gets a richer evidence base for diagnosis. When enough observations cluster around a coherent concern, the pressure crosses a threshold and a release epic forms to discharge them. The more observations, the better the fix — because explaining behavior problems is hard for humans, but pattern-matching across multiple evidence sources is where AI excels.

**Core principle — output vs. behavior:** AI text output is non-deterministic (the same greeting may use seven different phrasings). AI behavior is deterministic (the act of greeting always happens). If wrong behavior is 100% consistent, the inverse must hold: a fix can make right behavior 100% consistent. Observations capture behavior, not output. Multiple observations of the same wrong behavior confirm the pattern is behavioral — not output variance. This is why accumulation matters: one observation might be noise, but a cluster of observations pointing at the same behavior is a confirmed fix target. This is the bread and butter of the improve-system cycle.

Artifact grouping (which commands, skills, or protocols are affected) happens during release planning, not as a structural level. This keeps the hierarchy flat at two levels.

**Transfer mechanism:** When a release epic is created, the System Engineer selects a cluster of observations from the position epic and batch-transfers them: re-post each observation as a comment on the release epic, then delete the original from the position epic.

This keeps the position epic clean — only unaddressed observations remain. The release epic stays open until the improve-system B→C loop verifies all observations in the cluster are resolved. No partial closure: if one observation in the release isn't fixed, the cycle continues.

The System Engineer names the release epic after the concern the cluster addresses. This framing — a short title describing the behavioral pattern — is the SE's only addition beyond the observations themselves. The release epic then routes to execution — for instruction artifacts, this is `/improve-system` at theme level (Session B reads deduplicated conversations, diagnoses, fixes). See [/improve-system architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) Theme-Level Workflow.

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

The CCI improvement cycle follows a position pipeline that mirrors CI/CD for code: observations accumulate, cluster, get designed, get implemented, and deploy through stability stages.

**Partially defined:** The pipeline — observations → SE monitors pressure → release epic → `/improve-system` at theme level → organic verification — was surfaced across two extraction passes. For instruction artifacts, conversations are the design material (not design docs or tracking.md). The JA design doc step does not apply — the SE runs `/improve-system` Session B directly against deduplicated conversations. See [/improve-system architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) for the theme-level workflow. **Remaining open questions:** how the SE's "pressure threshold" judgment works in practice, and how stability gates between deployment stages (Marius R&D → team → product) are formalized. → Meeting agenda topic.

### 7. Operator Epics

Position epics collect observations about AI position behavior — how the Developer AI, JA AI, or SE AI behaves. Operator epics collect observations about how a human operator works with their AI. The distinction matters because operators have a natural forcing function that position epics lack: the operator's daily work quality depends on the system working correctly.

The operator epic (#635 DaveX2001) is David's single collection point. He posts all observations here regardless of which position artifact is affected. This is deliberate — routing to the correct position epic at capture time creates friction for the operator. David should focus on flagging deviations, not classifying them. The System Engineer reviews operator observations during grooming Step 4 and routes them to the appropriate position artifact at release epic creation time.

**Why operator epics create urgency:** In deliverable-tracking, client meetings force work through milestones. In CCI, the operator's friction is the forcing function. When David flags "the AI skipped `make staging`," that's urgent because his next session will hit the same problem. The better the system, the better David performs, the less review Marius needs — directly advancing the March plan goal (Marius = manager only by March 31).

**Undefined:** The connection between operator epic release epics (#646, #647) and position epic release epics (#604, #616). An operator observation may describe a problem ("AI didn't push"), but the fix lives in a position artifact (Developer AI instructions). Whether release epics should form under the operator epic (where the observation lives) or under the position epic (where the fix belongs) is unresolved. → Next extraction pass.

### 8. Board Infrastructure

The CCI board (GitHub Project P3) requires retrofit to match the ILR v2 column model. Current state (2026-03-13):

| Aspect | Current | Target |
|--------|---------|--------|
| **Columns** | Backlog → Todo → In Progress → Review → Done | Backlog → Working → Review → Done |
| **Milestones** | None (zero exist) | Aligned to a forcing function cadence (undefined) |
| **Issues** | 35 open, all in Backlog, all unmilestoned | Release epic sub-issues milestoned and flowing |
| **Position epics** | Visible on board (backlog) | Invisible — not tracked for throughput |

Position epics are observation collectors, not board-tracked work items. They should not appear in Sprint or Grooming views. Only release epics (Commitment Board equivalent) and their sub-issues (Work Board equivalent) need board visibility. The position epic is the CCI equivalent of a client label in deliverable-tracking — a grouping axis, not a work item.

The deliverable-tracking board retrofit (#904) established the pattern: ILR v2 defines columns, milestones tied to forcing functions, daily grooming fills and prioritizes. The same pattern applies to CCI, but the forcing function source differs — deliverable-tracking uses client meetings, CCI's forcing function is undefined.

**Undefined:** CCI milestone strategy — what forcing function drives CCI milestones? Candidates include: weekly internal sprint (same as deliverable-tracking), operator friction pull ("what unblocks David?"), or Traceline deployment cadence. → Next extraction pass.

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
