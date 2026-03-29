---
publish: true
---

# Dev Lead Witness & Review System
[[business-identity]]

System design for how the Dev Lead conducts witness testing, review, and quality feedback in multi-operator delivery.

---

## Problem Statement

Ship with Confidence defines three quality layers (sanity check, AC verification, human witness) but does not map who owns each layer, what depth each role performs, or what the output of each layer looks like. Position Agreements reference "human witness" as a deliverable without defining methodology, tooling, or attention requirements.

When a solo operator holds all positions, this works — tacit knowledge about what to test, where the authoritative rubric lives, and whether the codebase is coherent stays unified in one person. When positions are held by different people, that tacit knowledge does not transfer. The result: the Dev Lead absorbs 3-4 hours of deep witness work that should have been the Developer's responsibility, creates ad-hoc tooling during review instead of before, and falls into the technician trap — doing fixes instead of generating feedback.

**Preconditions:**
- Multi-operator delivery (Developer and Dev Lead are different people)
- Ship with Confidence testing pyramid exists and is stable
- Position Agreements for Developer and Dev Lead exist
- At least one project-level test rubric exists (client-delivered or JA-defined)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Dev Lead reviews to generate feedback — never to do fixes. Every quality layer has an explicit owner, methodology, and output format. |
| **Success** | Dev Lead spot-checks using developer-prepared materials (witness report + rubric cards + spot-check recommendations). Developer deep-witnesses before adding `review` label. |
| **Done test** | Can the Dev Lead complete a spot-check witness using only what the Developer prepared? If the Dev Lead is building scripts, investigating infrastructure, or fixing code — the system is failing. |

---

## Approach

### Part 1: Quality Layer Ownership

Ship with Confidence defines three quality layers. Each layer has a different owner, depth, and output format.

| Layer | Owner | Depth | Output |
|-------|-------|-------|--------|
| **Sanity Check** | Developer | Does it run? Does it not crash? | Pass/fail in session |
| **AC Verification** | Developer (AI) | Is the system state correct? Objective, automatable. | `verification.jsonl` — machine-readable trace |
| **Human Witness (Deep)** | Developer | Does the experience work? AI-guided teaching session, one step at a time. | Tour in PR body, Trace as PR comments |
| **Human Witness (Spot-check)** | Dev Lead | Quick validation using developer's witness Tour in PR. | Issue comment: findings routed back to developer |

**Key distinction — Verification vs Validation:** AC verification catches *specified* failures (system state checks via automation). Human witness catches *unspecified* failures (experience quality via human judgment). These are different layers, not different depths of the same thing. See [AC-DoD Framework § 1](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) for the three-gate model.

**Witness session mechanism (`/witness` skill):** After implementation, the AI derives a guided tour from three sources: ACs in tracking.md, implementation context (URLs, credentials, surfaces), and project-level test rubrics (from the epic body, data artifacts, or issue comments). The AI curates — it selects the most revealing test cases, explains why it picked them, and links to the full rubric for additional coverage.

The tour is structured as a natural experience flow, not per-AC tests. Each tour step annotates which ACs it covers, providing traceability without forcing the human to experience the feature as a checklist. Infrastructure ACs (document counts, config values, file existence) stay machine-only in verification.jsonl — the witness tour covers only experiential ACs where human judgment adds value.

No design-time witness hints are needed. The ACs themselves serve as the source — the AI derives concrete steps post-implementation because URLs, paths, and credentials only stabilize after the feature is built.

The AI presents steps one-by-one to the human. The human executes each step, reports what they see, and the AI records findings as PR comments (Trace). The developer runs the witness session for deep coverage. The Dev Lead follows the same Tour in the PR for spot-check (pick 2-3 steps based on judgment, verify on staging).

**The teaching test (Feynman principle):** The AI that built the feature must prove understanding by teaching the human to experience it. The witness session is not a test plan — it is a teaching demonstration. If the AI's instructions don't match what the human sees, either the implementation or the AI's understanding has gaps. This separation also future-proofs to AI-to-AI validation: swap the human executor for a second AI in a fresh context (no implementation bias).

**Attention mode:** Human witness requires single-window focus. Implementation and spec work can be multi-terminal. The Dev Lead cannot review while doing other work — same constraint as spec-design review.

**Spot-check methodology:** The Dev Lead opens the PR and follows the developer's witness Tour. Pick 2-3 steps based on the Dev Lead's own judgment — no curated recommendations needed from the developer. Execute on staging, form judgment. The Tour IS the prepared material — no separate artifact needed. If the Tour is missing or incomplete, that itself is the signal: the developer didn't witness their work.

**Ceremony checks (fail-fast cascade):** Before witness begins, the Dev Lead walks through a pre-flight checklist. The /witness skill presents each check; the human decides pass or fail. A failed check is itself a finding — it may block witness or become the primary feedback item.

| # | Check | Gate |
|---|-------|------|
| 1 | PR exists (on the code repo, open — not draft) | Hard — no PR → no witness |
| 2 | Spec review completed (ACs in tracking.md) | Hard — no spec → no tour |
| 3 | Witness report posted (developer ran deep witness) | Hard — no witness → nothing to spot-check |
| 4 | Deployed to staging | Hard — nothing to witness |
| 5 | Implementation conversations linked | Soft — limits teaching depth |
| 6 | tracking.md in the code repo (co-located with implementation) | Soft — absence is the finding |

**Tour examples (feature-type determines shape):**

*UI feature (#757 — Foundation + CMS + Deploy):*
1. Open `http://localhost:3000` → page rendered by Vike (AC1)
2. Open `http://VPS_IP:{port}/admin` → Payload CMS login screen (AC2)
3. Log in → 4 collections in sidebar: projects, memberships, content, legal (AC4)
4. Create a project entry → save → visible in list
5. Open frontend → project rendered from CMS data (AC5)

*RAG pipeline (#797 — Masterfragen Expansion):*
1. Open OpenWebUI on staging → chat interface ready
2. Ask: "Welche englischsprachigen Zertifikate bieten Sie an?" → answer with certificate info (AC2)
3. Ask: "Was kostet ein Datenschutz-Audit?" → answer with €790 + DS-Kit Plus (AC3)

The UI feature produces a 5-step tour through pages and data flow. The RAG pipeline produces a 3-step tour through questions and answers. Infrastructure ACs (doc counts, CSV updates) have no tour step — they stay in verification.jsonl.

### Part 2: Test Infrastructure Ownership

Test infrastructure splits across three positions. Each position owns a different aspect. Escalation flows upward: Developer → Dev Lead → JA.

| Position | Owns | Example (IITR #789 — illustrative, not exhaustive) |
|----------|------|---------------|
| **JA** | **Defines** — rubric in design doc: what to test, success metrics, expected outcomes, fidelity requirements ("test tool must mirror production pipeline") | 29 test questions, accuracy targets, source references ✅ |
| **Dev Lead** | **Provisions** — authoritative version deployed before developer starts. Deduplicates competing versions. Verifies accessibility. Periodic fidelity check. | 4 competing rubric versions, no single source designated ❌ |
| **Developer** | **Executes** — builds test harness, runs tests, performs deep witness against authoritative rubric. Signals issues upward. | Built harness, ran against wrong rubric version ⚠️ |

**Issue-level vs project-level:** The Developer creates ACs for each issue (how to verify THIS implementation). The JA designs the project-level test rubric (how to verify the PROJECT works as a whole). The Developer should not be expected to figure out the test rubric design — that is a spec-design artifact.

**Two distinct things:** The test *rubric* (data — questions + expected answers) and the test *infrastructure* (code — harness, debug scripts, faithful pipeline mirror). Both follow the same three-way split. The JA defines what to test and what "faithful" means. The Dev Lead provisions the authoritative data and verifies tool fidelity. The Developer builds and runs the tools.

**Single source of truth:** For every project with a test rubric, there is exactly ONE authoritative version. The Dev Lead designates it before delegation. All other copies (e.g., server files, Drive exports, hippocampus snapshots) derive from it. When the authoritative version changes, the Dev Lead is responsible for propagating the update.

**Escalation chain:** When something is wrong with the rubric or tooling:
- **Developer** finds rubric/tool issue during execution → signals **Dev Lead** via issue comment
- **Dev Lead** finds design gap during provisioning or fidelity check → signals **JA** via issue comment
- **JA** updates design doc → cycle restarts

**Undefined:** Operational provisioning steps — what "designate and deploy the authoritative version" looks like as a concrete procedure per project type.

### Part 3: Feedback-Not-Fixes

The Dev Lead reviews to generate feedback. Never to do fixes.

When the Dev Lead spot-checks and finds an issue, the output is an issue comment describing what was found — not a code change, not a hotfix, not a script built ad-hoc. That comment routes back to the Developer's workstream. The Developer fixes. The Dev Lead re-checks.

**The E-Myth trap:** The moment the Dev Lead starts fixing, they have fallen back into the technician role. The solo operator's instinct is "faster to fix it myself." In multi-operator delivery, this instinct destroys the system — the Developer never learns, the Dev Lead burns hours on work that isn't their accountability, and the feedback loop that makes each iteration more effective never forms.

**The cycle:**
1. Developer does deep witness → posts witness report as issue comment
2. Dev Lead spot-checks using developer-prepared materials → finds issues
3. Dev Lead posts findings as issue comment (feedback, not fixes)
4. Developer receives feedback → fixes → re-runs witness → updates comment
5. Dev Lead re-checks → approves or generates more feedback

**When the Dev Lead IS the solo operator:** This principle still applies conceptually. When wearing the Dev Lead hat, generate feedback (observations, issue comments). When wearing the Developer hat, do the fixes. The hat-switch is the discipline — even for one person, separating review from execution prevents the "quick fix" that introduces new debt.

**Undefined:** Structure and routing mechanism for feedback comments — what a feedback comment contains, how it gets assigned back to the Developer's workstream.

### Part 4: Communication Standards

Two principles ensure the Dev Lead has visibility without needing conversation log access.

**Witness Reports:** After deep witness, the Developer communicates findings and spot-check recommendations via issue comment. This is the handoff artifact — without it, the Dev Lead rediscovers what to test from scratch.

**Deviation Signals:** When the Developer goes off-plan (adds data, changes approach, creates unspecified infrastructure), they signal the deviation. Going off-plan is fine — initiative is valued. Invisible deviation is the failure. The communication medium and exact format are TBD — the principle is: the Dev Lead learns about deviations before the review, not during it.

**Witness report format:** The witness report is the Tour + Trace structure in tracking.md (see Part 6). The tour provides the actionable steps; the trace provides the historical record. Together they replace ad-hoc exploration with structured spot-check material. Exact visual format is being calibrated empirically.

**Undefined:** Deviation signal format — how the Developer communicates off-plan changes. The principle (signal before review, not during) is stable; the medium and structure are TBD.

### Part 5: Codebase Coherence Audit

Before delegating an epic to a Developer, the Dev Lead verifies the codebase is in a consistent state.

**Developer boundary:** The Developer operates at issue-level — ACs, implementation within design doc scope, issue-level test harness. The Developer is NOT responsible for project-level concerns: codebase coherence, test rubric design, cross-issue architectural consistency, or inherited technical debt. Those are Dev Lead and JA concerns respectively.

**Principle:** The Dev Lead is the bridge between the design spec and the codebase. If the codebase is messy, undocumented, or internally contradictory, that is the Dev Lead's problem to surface — either cleaning it up or flagging it as a precondition before the Developer starts.

**Examples (not limited to):** Dead config nothing reads, conflicting mechanisms where only one is active, repo vs deployment drift.

**Trigger:** Per epic, before first sub-issue assignment. Especially when a project transitions from solo-operated to multi-operated code.

**Undefined:** Exact checklist, frequency, and depth — routed to [#807](https://github.com/DaveX2001/deliverable-tracking/issues/807).

### Part 6: Witness Skill Operational Design

The `/witness` skill is a Claude Code skill that operationalizes the Feynman teaching test from Part 1. The AI constructs a guided tour from implementation context and presents it step-by-step to the human. The tour proves the AI's understanding — gaps in the teaching reveal gaps in the implementation.

**Source architecture:** The skill runs in a separate session (cold view — no implementation memory) and builds its understanding from three sources in progressive order:

| Source | Provides | Access |
|--------|----------|--------|
| **tracking.md** | WHAT to verify — ACs define the spec boundary | Direct file read |
| **Project index** | WHERE + PROJECT CONTEXT — URLs, design docs, test rubrics, deployment surfaces | `project-index.md` via GitHub API |
| **Implementation conversations** | HOW + DECISIONS — credentials, deployment specifics, implementation reasoning | Conversation-reader with targeted queries, session paths from issue comments or conversation index (#986) |

Each layer adds teaching depth. tracking.md gives the spec. The project index gives the project world view. The conversations give the builder's understanding — enabling Feynman-quality teaching rather than spec-level checking.

**Cold view pattern:** The skill runs in a separate session from implementation, mirroring ac-verify's two-session model. Three reasons:
1. **Bias removal** — the AI that built a feature unconsciously skips what it "knows works"
2. **AI-to-AI path** — cold witness is already the architecture for spawning a separate AI to witness another AI's work
3. **Context window economy** — implementation sessions consume context; fresh sessions start clean

The preparation phase is visible (what was read, what was queried, what gaps remain) — same as ac-verify's pre-verification code review. The witness steps are the output.

**Tour construction:** The tour derives from business promises — the human-visible capabilities the issue delivers — not from ACs. "What can a human DO after this issue?" organizes the tour; ACs annotate each step for traceability but do not determine the sequence.

The tour is constructed once and reused. Subsequent ceremonies follow the existing tour and produce a new trace — the tour is a stable launchpad, not rebuilt each time. The layer (UI, API, CLI, test harness) changes the medium but not the shape.

**Three-beat witness step (mandatory):** Every step follows three beats:
1. **Teach** — GWT acceptance criteria as verbose context (why this step matters)
2. **Ground** — reproducible action: CLI command, URL, API call, or screenshot (depends on step type — the principle is reproducibility, not the medium)
3. **Witness** — compact Action/Expected prompt for the human to verify

The AI presents all three beats for each step. The human reads the teaching context, sees the grounding evidence, then executes the action and reports what they witness.

**Undefined:** Business promise derivation method — how to extract business promises from issue context and design docs. To be defined from #757 empirical data (next extraction pass).

**Witness step format:** Each step follows the three-beat structure (teach → ground → witness). The written format in the PR body:

```
**Step N: {Step Title}** (AC references)
Given {precondition}, When {action}, Then {expected outcome}.

$ {CLI command or reproducible action}
→ {expected output or URL}

**Action:** {What the human does}
**Expected:** {What they should see}
```

The GWT context teaches why. The grounding proves the AI checked. The Action/Expected prompts the human to verify. The artifact type varies by feature layer: staging URLs, API payloads, test reports, CLI output, file content (see [AC-DoD Framework § 4](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) for layer taxonomy). When no witnessable artifact exists, record a witness failure (see back-pressure below).

**What gets witnessed vs what stays machine-only:** The Tour derives from business promises, not from a classification heuristic. If a step is in the Tour (derived from "what can a human DO?"), it gets witnessed. If it's not in the Tour, it stays in verification.jsonl. The developer may suggest which ACs are "low witness value," but the Dev Lead decides — the developer's classification is input, not authority.

At the issue level, the AI also references relevant items from the project-level test rubric — "if you want to test more, here are relevant test cases from the full rubric." The AI curates 2-3 rubric items and links to the rest, proving it sees the full project context.

**Output structure — Tour and Trace:** The witness artifacts live in the PR, not in tracking.md. The PR is the Dev Lead's natural entry point when the `review` label appears — everything needed for review is co-located there. tracking.md remains the machine layer (ac-verify reads it programmatically). The Dev Lead never needs to open tracking.md.

*Tour (PR body):* The replayable launchpad. ACs listed up top, then three-beat steps with reproducible grounding. Any human — developer or Dev Lead — can follow the steps to experience the feature. Written once by the /witness skill, reused for subsequent ceremonies.

*Trace (PR comments):* What was actually witnessed during each ceremony. Each ceremony run produces a new PR comment recording the human's observations and whether they matched expectations. The trace is "verification.jsonl made for humans" — structured evidence of what was experienced.

*Findings (issue comments):* When the Dev Lead discovers a mismatch, the finding is posted as an issue comment — not on the PR. The PR stays clean as the evidence package. The issue is the lifecycle thread where feedback routes back to the developer.

The PR becomes structurally necessary: no PR → no place for the witness Tour → ceremony cannot proceed. This makes the PR a hard gate not just procedurally but architecturally.

**Example (#757 — Foundation + CMS + Deploy Pipeline):**

*Tour:*

1. Open `https://klimafolgenschutz-cms.wilsch-deployment.com/admin` → Payload CMS login (AC2)
2. Log in with admin credentials → 4 collections visible in sidebar (AC4)
3. Create a new project entry → fill title + description → save (AC5)
4. Open `https://klimafolgenschutz.com` → project rendered from CMS data (AC5)
5. Push a commit to main → verify deploy pipeline fires: `gh run list` (AC3)

*Trace:*

| Step | Witnessed | Result |
|------|-----------|--------|
| 1 | Login form rendered, Payload branding visible | ✅ |
| 2 | 4 collections in sidebar, all accessible | ✅ |
| 3 | Project created, appears in list view | ✅ |
| 4 | Frontend shows project tile with title + image | ✅ |
| 5 | Deploy workflow not found — no `production.yaml` in `.github/workflows/` | ❌ |

Step 5's mismatch becomes feedback routed back to the developer. The Dev Lead doesn't fix it — they post the finding as an issue comment.

**Mismatch handling (Middle Ground):** When the human's experience doesn't match the AI's guidance:

| Mismatch type | Action | Trace |
|---------------|--------|-------|
| Quick fix | Fix in witness session, retry step | Recorded — fix visible |
| Big failure | Record, route back to implementation session | Recorded — discrepancy noted |
| Always | Record regardless | Dev Lead sees full history |

**Back-pressure: missing artifacts.** When the skill cannot construct a concrete step because no witnessable artifact exists (no staging URL, no test report, no deployed feature), this is a **witness test failure** — recorded in tracking.md like any other mismatch. The failure routes to two possible causes:

1. **Implementation gap** — the spec defined the artifact, but the Developer didn't produce it. Route: back to Developer.
2. **Spec design gap** — the spec never defined which artifacts the implementation should produce. Route: back to JA for a spec design pass defining the artifact creation strategy.

This raises the question of whether spec design should anticipate which artifacts the implementation produces — defining artifact expectations at design time rather than discovering gaps at witness time.

**Undefined:** Artifact anticipation in spec design — should the JA define expected witnessable artifacts (staging URLs, test reports, deployment surfaces) during design? This would be a major change to the JA protocol but could prevent witness failures from spec gaps. Currently, artifacts emerge organically from implementation. The witness failure back-pressure may be sufficient to surface gaps without adding design-time overhead.

Don't waste the back-pressure — suppressing the gap (skipping the step, guessing) wastes the diagnostic signal.

**Dev Lead integration:**

| Mode | How it works |
|------|--------------|
| **Self-directed** | Dev Lead opens the PR, reads the Tour (body), cherry-picks 2-3 steps, tests on staging, posts findings as issue comments. |
| **AI-guided** | Dev Lead runs `/witness --spot-check`. AI presents steps from the Tour one-by-one. Dev Lead executes each step and reports. AI records the Trace as PR comments. |

The PR is the Dev Lead's entry point. Open the PR → read the Tour → spot-check → post findings on the issue. No navigation to tracking.md required.

**Authority model (mandatory):** The AI presents ONE step at a time. The human decides: pass, fail, skip, or investigate further. The AI NEVER auto-advances to the next step without the human's explicit signal. Batching steps or presenting findings as a summary destroys the collaborative witness experience — the Dev Lead needs to form judgment AT each step, not after reading a report.

**Dependencies:**

| Dependency | Classification | Without it |
|-----------|----------------|------------|
| **PR** | Hard gate | No PR → no place for Tour/Trace → ceremony cannot proceed |
| **tracking.md with ACs** | Hard gate | Cannot construct tour — nothing to derive steps from |
| **Project index** | Essential | Tour is spec-level only — no project understanding, no Feynman depth |
| **Conversation index (#986)** | Being built | Cannot query implementation sessions for deep context — tour is project-aware but shallow on implementation specifics |

**Undefined:** Project-level regression witness — how `/witness` connects to Ship with Confidence smoke testing at the project level (vs issue-level witness defined here). Routed to Ship with Confidence methodology.

**Undefined:** Design doc template mandate for test rubric section — the JA should always include a test rubric section in design docs, feeding the witness tour via the project index. Currently not enforced in the design doc template.

---

## Source

- **Post-mortem:** [#789 IITR DS-Kit Navigation](https://github.com/DaveX2001/deliverable-tracking/issues/789) — first multi-operator delivery, 3-4 hour witness pain
- **Observations:** [#605 Dev Lead Position Epic](https://github.com/DaveX2001/claude-code-improvements/issues/605) — 20+ observations clustered into this system
- **Position Agreements:** [Dev Lead PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/dev-lead-position-agreement-wilsch-ai-services) | [Developer PA](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services)
- **Methodology:** [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence)
- **Session (initial):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/344de871-d0b0-47b8-a44c-a01f726b24fc.jsonl
- **Session (Feynman Test extraction):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/4b8d4a93-6ccb-4b4f-ac6a-4b8500619905.jsonl
- **Session (Ceremony + witness tour extraction):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/83d4d2fe-f380-41ee-90e1-82ad154feca5.jsonl
- **Session (Witness skill operational design):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/7ea9fbcf-1f68-43f0-9d84-f843ea2a1175.jsonl
- **Session (Empirical #849 ceremony — first witness test):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-klimafolgenschutz-website/92fcc982-ab32-406a-b2ed-1c09e085c821.jsonl
- **Session (Empirical #757 ceremony — second witness test):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-klimafolgenschutz-website/9428d73a-196f-453e-8d23-73e905325d03.jsonl
- **Session (Part 6 redesign from empirical data — pass 1):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/e9b37733-feca-4a2b-9d62-4a61f74f5962.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

