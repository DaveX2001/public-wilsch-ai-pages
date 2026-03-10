---
publish: true
---

# Developer Operations Manual - Wilsch AI Services

[[life-vision]]

Operations Manual for the Developer position at Wilsch AI Services. Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 15 (Management Strategy) + E-Myth Operations Manual Guide (MG-0080).

**Position:** Developer

**Reports To:** Dev Lead

**Version:** v3 (Draft - 2026-02-06)

---

## How to Use This Manual

This is your "owner's manual" for the Developer position. It tells you:
- **How your day flows** (Morning Start → Work → End of Day)
- **What tools to use** (skills guide you through the methodology)
- **Why things work this way** (logic behind each process)

**Read the referenced skills once** to understand WHY they exist. The skills guide you through the HOW via prompts.

---

## Section 1-3: Company-Wide (TBD)

*To be created: Company Story, Products & Services, Policies*

For now, reference:
- [Strategic Objective](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services)
- [Organization Chart](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- [Primary Aim](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/primary-aim-life-vision)

---

## Section 4: Position

[Developer Position Agreement](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-position-agreement-wilsch-ai-services)

---

## Section 5: Systems (Developer Workflow)

### Morning Start

**Your "package in the mailbox":** GitHub Projects → Deliverable Tracking → Mohamed view

**Purpose:** Start each day with clear direction — no guessing what to work on.

---

#### Flow

1. **Open your view:** [Deliverable Tracking Board](https://github.com/orgs/DaveX2001/projects/1/views/7)
   - Filter: `assignee:mo-adel007 -status:Backlog,Done`
   - You see: To-Do, In Progress, Review

2. **Pick work:**
   - IF In Progress has items → **Continue and finish** (get things done)
   - ELSE pick **TOP item from To-Do** (grooming already sorted it)
   - IF nothing in To-Do → Flag to Marius

3. **Check issue type:**
   - `maker/spec-design` → Path A
   - `maker/spec-implement` → Path B

4. **(Optional) Reference yesterday's grooming transcript** if you need context on why this item is priority

---

#### Logic

**Why finish In Progress first:** Context switching is expensive. Completing work > starting new work.

**Why To-Do is already sorted:** Grooming physically reorders items — top = highest priority. You don't need to guess or ask. The system delivers the priority.

**Why transcripts are available:** If you forget what was discussed in grooming, the Fireflies transcript is your reference. You shouldn't need it often, but it's there.

---

### Path A: Spec-Design Issue

**Purpose:** Transform unclear requirements into clear, implementable specification.

**Closing criteria:** DoD + AC created in a new spec-implement issue.

**Why this exists:** You can't build what you don't understand. Spec-design forces thinking BEFORE building — cheap to change a design doc, expensive to change implementation.

---

#### Flow

**Step 1: Pick and Onboard**
```
/onboarding → select spec-design issue
```
[`/onboarding` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/onboarding.md)
AI loads issue context. You see: What, Why, Notes.

---

**Step 1b: Find the Source Material**

The issue body is a **summary**, not the requirement. Before rubber-duck, find the original source:

| Source Type | Where to Look |
|-------------|---------------|
| **Fireflies transcript** | Meeting where the requirement was discussed — search by date or topic |
| **Email / Ticket** | Client email or TANSS ticket linked in issue Notes |
| **Design doc** | Parent spec-design issue or hippocampus document |

**Why this matters:** ACs need context. A bullet point like "add 2FA" doesn't tell you WHY (GDPR compliance, customer data sensitivity) or HOW the client expects it (Google Authenticator specifically). The transcript does. Skipping this step = writing ACs from summaries = missing context = rework.

**Rule:** If the issue has a transcript or email reference, read it before `/rubber-duck`.

---

**Step 2: Externalize Thinking**
```
/rubber-duck
```
[`/rubber-duck` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/rubber-duck.md)
- Think out loud — AI asks questions to clarify your understanding
- Source material from Step 1b feeds your thinking
- Goal: Shared understanding between you and AI
- AI signals **Confidence ✓** when ambiguities resolved
- If stuck → ask Marius during office hours

---

**Step 3: Define ACs**
```
/ac-create (when Confidence ✓)
```
[`/ac-create` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/skills/ac-create/SKILL.md)

**Two concepts:**

| Concept | Question |
|---------|----------|
| **DoD (Definition of Done)** | "Does it exist?" — what to build |
| **AC (Acceptance Criteria)** | "Does it work?" — behavioral automated testing |

Parallel gates, not traced. Both must pass independently. Full methodology: [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) + [AC DoD Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework)

**Your job:** Judge the 4 Refinement Gate checks:
1. Observable Output — "Can I actually SEE this work?"
2. Test Data — "Do we have what we need to test it?"
3. Design Questions — "Are there decisions not documented?"
4. Blocking Dependencies — "What needs to happen before verification?"

The skill guides you through. You judge.

---

**Step 4: Read and Confirm**
- New spec-implement issue is created
- **READ THE ENTIRE ISSUE + ACs**
- 💡 **Tip:** Use Speechify extension to read it aloud — hearing + reading helps catch gaps
- Ask: "Do I understand this? Is this stable?"
- ACs are now LOCKED — changes = new spec-design session

---

**Step 5: Review (Training Phase)**
- Add `review` label to the **spec-design** issue (spec-implement issue already exists from Step 3)
- Marius reviews the ACs on the new spec-implement issue
- If approved → close spec-design issue ✅
- If changes needed → back to Step 2

**Office Hours (Training Phase):** During the 10-25 issue training period, Marius is available on Google Meet (camera off, voice off by default). Unmute/share screen when questions arise. Reference: [Strategic Objective - Training Method](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/strategic-objective-wilsch-ai-services#training-method)

---

#### Logic

**Why DoD and AC are separate:**
- DoD tells you what to BUILD
- AC gives you behavioral automated testing — backpressure so AI can self-verify
- You can build something (DoD ✓) that doesn't work correctly (AC ✗)

**Why Refinement Gate exists:**
Last opportunity to catch problems before implementation. Cheap to fix here, expensive during implementation.

**Why training review exists:**
During 10-25 issues, Marius reviews ACs before implementation to catch misunderstandings early. Later, this moves to grooming. Eventually, trust is established and only finished work is reviewed.

---

### Path B: Spec-Implement Issue

**Purpose:** Build what was specified in DoD + AC, then verify it works.

**Two sessions** (fresh context between them prevents bias):

| Session | Purpose | Ends at |
|---------|---------|---------|
| **Implementation** | Build the thing | Task Complete (code pushed) |
| **Verification** | Prove it works | `review` label added |

Reference: [Three-Session Model](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model)

---

#### Implementation Session

---

#### Flow

**Step 1: Pick and Onboard**
```
/onboarding → select spec-implement issue
```
[`/onboarding` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/onboarding.md)

**What AI loads:**
- Issue body (What, Why, Notes)
- **DoD + ACs from tracking file** — primes AI's understanding
- **verification.jsonl** — what's been verified (pass/fail/pending/nothing)
- Last 20 comments (temporal context)

**What you see:** AI suggests a **next step** based on all context. Together, these sources always tell you where you are in a task:

| Source | What it tells you | How to read it |
|--------|-------------------|----------------|
| **DoD checkboxes** | What's built | `[x]` = built, `[ ]` = not built |
| **verification.jsonl** | What's verified | `passed`/`failed`/`pending` = tested, **no entry = never tested** |
| **Comments** | What happened, what's next | Newest = current state |
| **Issue body** | Original requirement | Static context |

**Key:** If verification.jsonl has no line for an AC, that AC has never been verified — the file simply has no entry for it. This is different from `pending` (which means verification was *attempted* but blocked).

**Options during onboarding:**

| Option | When to use |
|--------|-------------|
| **React Frontend README** | Working on React frontend — provides best practices |
| **FastAPI Backend README** | Working on Python backend — provides best practices |
| **Full investigation** | Compare DoDs against codebase — use if you don't remember what's implemented |
| **Skip** | Context is fresh, want to move fast |

**Trade-off:** Latency vs Precision.

---

**Step 2: (Optional) Clarify**
```
/rubber-duck (if needed)
```
[`/rubber-duck` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/rubber-duck.md)

**When to use (including but not limited to):**
- Picking up after a while — need to reconnect
- Unsure about implementation approach
- Assumptions feel shaky — need to externalize thinking

**When to skip:**
- Moving from spec-design right away — context is fresh
- Path forward is obvious

**Key principle:**
> Don't build on UNCLARIFIED assumptions. If you can't judge whether something is right or wrong, there's no point building it.

---

**Step 3: Clarity Workflow**

**What it is:** The structured opposite of rubber-duck.

| Rubber-duck | Clarity Workflow |
|-------------|------------------|
| Free-form externalized thinking | Takes your thinking → outputs a plan |
| You drive | AI drives, resolves ambiguities |

**You invoke** three phases (these are user-invoked commands — AI won't start them on its own):

| Phase | Command | Question |
|-------|---------|----------|
| **Requirements-Clarity** | [`/requirements-clarity`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/requirements-clarity.md) | "Is the WHAT clear?" |
| **Implementation-Clarity** | [`/implementation-clarity`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/implementation-clarity.md) | "Is the HOW clear?" |
| **Evaluation-Clarity** | [`/evaluation-clarity`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/evaluation-clarity.md) | "How do we VERIFY?" |

**Skills vs Commands — important distinction:**

| Type | Invoked by | Example |
|------|-----------|---------|
| **Command** (user-invoked) | You type it | `/requirements-clarity`, `/rubber-duck` |
| **Skill** (AI-invoked) | AI discovers during clarity phases | [Worktree (GTR)](https://github.com/MariusWilsch/claude-code-team-plugin) |

Skills are discovered via a [discovery script](https://github.com/MariusWilsch/claude-code-team-plugin) that runs during clarity phases. Skills discovered during clarity are executed during Execute.

**Who AI asks for what:**

| Ambiguity type | Who AI asks |
|----------------|-------------|
| Code structure | Codebase (grep/glob) |
| User preference | You (AskUserQuestion) |
| API docs / external | Internet / MCP |

**Why sub-agent for research:** Avoids polluting context with 78-90k tokens of search results. Returns focused summary.

**Positional arguments:**
```
/requirements-clarity let's do pass one
```
Focuses AI on specific pass. Optional — context from rubber-duck is usually sufficient.

---

**Step 4: Execute**

**Before Execute — ExitPlanMode judgment:**

After Evaluation-Clarity, AI calls ExitPlanMode. Before approving, ask:

> "Do I feel confident this plan will give us an answer to move this issue forward?"

- **Yes** → Approve, let Claude execute
- **No** → Don't approve, clarify first

**First action after approval — Worktree setup:**

AI creates a [worktree via GTR](https://github.com/coderabbitai/git-worktree-runner) (AI-invoked skill, discovered during implementation-clarity). This:
- Creates an isolated branch (`issue-{N}`) in a sibling directory
- Copies `.env*` files automatically
- Creates a draft PR with issue linking
- Enables parallel development (multiple issues = multiple worktrees)

**Why worktree:** Don't develop on main. Each issue gets its own isolated environment. Parallel sessions can run simultaneously because each works in its own worktree. Skill reference: [Worktree Skill](https://github.com/MariusWilsch/claude-code-team-plugin)

**During Execute:**
- Execute is autonomous — let Claude work
- Work in **passes** (from DoD dependency analysis)
- Sanity checks happen before Task Complete
- If sanity fails → backpressure → AI tries to fix

**Task Complete:** AI signals when plan is complete and sanity checks pass.

**Valid outcomes:**
- ✅ Pass implemented, sanity checks pass
- ✅ "This approach doesn't work" — documented learning is valid
- ❌ Confusion without documentation

---

**Step 5: Leave Handoff (optional but recommended)**
```
/issue-comment
```
[`/issue-comment` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/issue-comment.md)

After Task Complete, leave an actionable note on the issue. The [issue-comment command](https://github.com/MariusWilsch/claude-code-team-plugin) collects:
1. **Context** (2-3 sentences — what happened)
2. **Source** (conversation path — required)

**Philosophy:** Keep comments short and actionable. Focus on "what's the next step," not stateful dumps. The issue-commit-linker already links commits to the issue timeline, but those aren't proper comments. `/issue-comment` gives you a free-form, human-readable handoff.

**Why source links matter:** Future sessions read the source to get full depth. The comment is a pointer, not a copy. Short comment + deep source > long comment with stale context.

---

#### Logic

**Why Clarity Workflow:** AI resolves its OWN ambiguities before executing. You don't think for AI — AI investigates and asks when stuck.

**Why commands are user-invoked:** Clarity phases require human judgment about when to start each phase. AI is aware of them from CLAUDE.md but won't initiate them — you drive the timing.

**Why worktree is AI-invoked:** Worktree creation is mechanical (no judgment needed) and always happens right after ExitPlanMode. The discovery script surfaces it during implementation-clarity so AI knows to use it.

**Why sanity checks are backpressure:** Task isn't complete unless sanity passes. This gives AI feedback to self-correct.

**Why issue-comment at end:** The issue-commit-linker captures commit-level context automatically. `/issue-comment` captures your thinking about what comes next — something commits can't express.

---

#### Verification Session

**Purpose:** Prove that what was built actually works against the spec. Then deploy, test, and hand off.

**Why a separate session:** Fresh context prevents confirmation bias. The AI that built the code shouldn't verify it with that implementation context still loaded. Research shows 40-60% quality improvement from separate implementation/verification agents. Reference: [Three-Session Model](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model)

**Session ends at:** `review` label added (or at 60-70% context — continue in new session)

---

**Step 1: Pick and Onboard**
```
/onboarding → select same spec-implement issue
```
[`/onboarding` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/onboarding.md)

Same as Implementation Session. AI loads all context sources (DoD, ACs, verification.jsonl, comments, issue body) and suggests next step.

---

**Step 2: (Optional) Clarify**
```
/rubber-duck (if needed)
```
[`/rubber-duck` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/rubber-duck.md)

**When to use (including but not limited to):**
- Unsure about what was built (long gap since implementation)
- Need to reconnect with the intent behind the ACs

**When to skip:**
- Coming right from implementation — context is fresh
- ACs are clear and straightforward

**Note:** Without `/rubber-duck`, AI behaves more like a standard chat model — it responds but doesn't proactively probe for ambiguities. Rubber-duck mode continuously asks questions and drives toward confidence.

---

**Step 3: Rebase Staging (best practice)**

Before verifying, rebase staging into the worktree to test against fresh state:
```bash
git fetch origin staging && git rebase origin/staging
```

**Why:** Your worktree may have branched off days ago. Other work may have merged to staging since then. Rebasing ensures AC verification tests against reality — not a stale snapshot. Especially critical with parallel worktrees.

*(Gap: Not yet integrated into AC Verify skill — manual step for now)*

---

**Step 4: AC Verify**
```
/ac-verify
```
[`/ac-verify` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/ac-verify.md)

The core of the Verify Session. AC Verify is **behavioral testing against the spec** — not a code review.

**What it does:**
1. **Code review as INPUT** — reads code to know what to test (not the output)
2. **Layer detection** — parses each AC's Given-When-Then to determine tool:

| Layer | Tool | Trigger keywords |
|-------|------|------------------|
| **UI** | Chrome DevTools | click, see, display, button, form |
| **API** | curl / Bash | endpoint, request, response, POST |
| **Database** | Supabase MCP | record, stored, query, persisted |

3. **Executes GWT steps** — runs each Given-When-Then against the running system
4. **7 Principles** — always check console + network, verify negatives, capture preconditions
5. **Records traces** — writes structured observations to `verification.jsonl`

**Result handling:**

| Result | Action |
|--------|--------|
| **Passed** | Record to verification.jsonl |
| **Failed (quick fix)** | Fix in-session (container down, config missing), retry |
| **Failed (major)** | Record failure → back to Implementation Session |
| **Pending** | Blocker documented (missing API keys, env not configured) |

**What AC Verify does NOT do:**
- Can't catch things outside the spec (specs have blind spots)
- Can't judge subjective quality ("Does this look nice?" isn't a Given-When-Then)
- Can't replace human witness — even if all ACs pass, human still needs to see it

**The `verification.jsonl` is precedent, not proof.** It captures not just what happened but *why* (the `interpretation` field). Future sessions read these traces to understand baseline behavior and catch regressions. Full schema: [AC Verification Decision Trace](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-verification-decision-trace)

---

**Step 5: PR Review**

After AC Verify passes, run PR review (currently PR Toolkit or code review from Anthropic plugins — may change).

AI proposes fixes. **You decide** which are worth fixing — not everything needs to be addressed.

**(Optional) Fix critical issues:** If PR review surfaces critical problems and context allows, run another clarity phase to fix them in-session.

---

**Step 6: Merge**
```
/merge
```
[`/merge` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/merge.md)

Merge to staging branch. The worktree's draft PR (created during Implementation Session) is used.

**If at 60-70% context:** Stop here. Deploy + smoke + human witness in a new session.

---

**Step 7: Deploy to Staging**

#### Server Directory Convention

On **multi-user servers** (multiple deployers), projects live in a shared path — not user home directories:

```
/home/shared/{project}/     ← canonical project path
├── .env                    ← server-specific config
├── docker-compose.*.yml    ← compose files
└── Makefile                ← deployment interface
```

**Why:** User home checkouts (`/home/marius/...`, `/home/david/...`) cause container name conflicts, permission issues, and stale Makefiles. One canonical path eliminates duplication.

**Permissions:** Create a project-specific Unix group (e.g., `rohdex`), add all deployers, set the directory with setgid so new files inherit the group:

```bash
sudo groupadd {project}
sudo usermod -aG {project} {user1} {user2}
sudo mkdir -p /home/shared/{project}
sudo chown root:{project} /home/shared/{project}
sudo chmod 2775 /home/shared/{project}   # setgid
```

**Single-user servers** (e.g., RDX-APP-01 with one deployer): keep `~/projects/{project}/` — no shared path needed.

**Migrating existing projects:**
1. Create group + `/home/shared/{project}/` (permissions above)
2. Clone repo into shared path, copy `.env` from old checkout
3. Stop containers from old checkout
4. Remove stale checkouts (all user home copies)
5. Start containers from new path, verify `/health`
6. Update Makefile `STAGING_DIR` / `PROD_DIR` to point to shared path

---

Identify which deployment pattern(s) apply, then execute:

| Project type | Pattern | Key command |
|-------------|---------|-------------|
| Backend on VPS | Docker Compose | `ssh {SERVER}` → `cd /home/shared/{project}` → `make staging` |
| Frontend on Vercel | Vercel Hobby | Push to `staging` branch (GHA auto-deploys) |
| Needs subdomain | Caddy | `sudo caddy validate` → `sudo systemctl reload caddy` |

**Combining patterns:** Deploy services first (Docker Compose / Vercel), then configure Caddy last (it needs a running target to proxy to).

**Docker Compose Backend:**
```bash
ssh {SERVER}
cd /home/shared/{project}
make staging
# Makefile runs git pull + docker build + docker compose up locally on the server
# Verify: curl health endpoint (Makefile prints result on success)
```
→ Full pattern: [Docker Compose Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/docker-compose-backend-deployment-design)

**Vercel Hobby Multi-Dev:**
```bash
git push origin staging  # GHA deploys automatically via deploy token
# Verify: check GHA run status in GitHub Actions tab
```
→ Full pattern: [Vercel Hobby Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/vercel-hobby-multi-dev-deploy)

**Caddy Subdomain (if new subdomain needed):**
```bash
ssh {SERVER}
sudo nano /etc/caddy/conf.d/{SERVICE}.conf
sudo caddy validate --config /etc/caddy/Caddyfile
sudo systemctl reload caddy
# Verify: curl -I https://{SERVICE}.wilsch-deployment.com
```
→ Full pattern: [Caddy Subdomain Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/caddy-subdomain-routing-design)

---

**Step 8: Smoke Test**

Run the project's `SMOKE-TEST.md` checklist.

**What a smoke test is:** Every application has a core flow. The smoke test checks: "Does the critical path still work?"

| Project | Core Flow |
|---------|-----------|
| Paul Faceless YT | Video generation |
| IITR | RAG retrieval |

**Not comprehensive** — just "did we break the thing that matters most?" Keep under 5 minutes.

**Why mandatory:** Code breaks in unexpected ways. Smoke test is cheap insurance. The spec tested individual ACs; smoke test tests the integrated system.

PASS → continue. FAIL → investigate before proceeding.

---

**Step 9: Human Witness — Technical (Developer)**

Click through the feature on staging. Freeform discovery — no checklist, no script.

**What you're looking for:**
- Things that don't feel right (UX, timing, layout)
- Behaviors the spec didn't anticipate
- Edge cases nobody thought to specify
- "It works but something's off" moments

**If issues found:** Comment on GitHub issue → fix in new Implementation Session.

**If nothing wrong:** Proceed.

**Why human witness exists:** ACs test what was specified. Humans catch what wasn't. "Does this look nice?" can't be a Given-When-Then. You seeing it and having accountability for it is the point.

---

**Step 10: Add `review` Label + Handoff**

```
/issue-comment
```
[`/issue-comment` source](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/issue-comment.md)

Add `review` label to the issue. This is the handoff signal — Marius is notified.

Leave an issue comment with context and source (same as Implementation Session Step 5).

*(Gap: For sub-issues in epics — remove parent link, add to board, move to review status. Not yet automated.)*

---

**Step 11: Human Witness — Business (Marius, async)**

Marius reviews on staging with a business perspective: "Is this what I wanted?"

Same freeform process, different eyes. Issues found → comment on issue → fix in new session.

If approved → merge to production. Marius is the final gatekeeper.

---

#### Logic

**Why separate sessions (Implementation vs Verification):** Confirmation bias. The AI that built code will seek confirmation it works, not genuine evidence. Fresh context = honest testing.

**Why rebase before AC Verify:** Test against reality. Parallel worktrees mean your branch may be days old. Rebasing catches integration conflicts before they surprise you at merge time.

**Why AC Verify before PR review:** Behavioral evidence first, code style second. Knowing something *works* is more valuable than knowing it's *clean*.

**Why smoke test after deploy:** ACs test individual features. Smoke test tests the integrated system. A feature can pass all ACs but break the core flow through unexpected side effects.

**Why human witness is NOT backpressure:** ACs and smoke tests are automated feedback (backpressure) — agents can self-correct. Human witness is fundamentally different — it catches what automation *can't anticipate*. This layer can never be fully automated.

**Why sequential testing:** Human attention is the bottleneck — by design. Testing grabs the Developer's attention, forcing them to witness their own work. That's the accountability mechanism.

**Why `verification.jsonl` exists:** Born from the context graph idea. Structured traces become institutional memory. Future sessions know: what was tested, how it was interpreted, what the baseline behavior looks like. When something breaks, you can compare against historical traces.

**The bigger vision:** This entire framework (separate sessions, structured verification, decision traces) builds toward autonomous coding. The human witness layer is what keeps it honest as AI does more of the work.

---

### End of Day

**Purpose:** Leave a clean handoff so Marius can review asynchronously.

**Ritual:**

1. **For completed work:**
   - Add `review` label to issue
   - Run `/issue-comment` with status summary
   - Marius reviews next morning

2. **For In Progress work:**
   - Commit + push current state
   - No special action needed (stays In Progress)

**Why this matters:** Marius can start reviewing your completed work without waiting for a sync.

---

### Review

**Purpose:** Quality gates that catch problems at different levels before code reaches production.

**Two review types, two hats:**

---

#### Review Type 1: Spec-Design Quality (Dev Lead)

**When:** After Path A Step 4 (Read and Confirm) — you add `review` label to the spec-design issue.

**What happens (async):**
1. Dev Lead reads your DoD + AC
2. "Does this design make sense?"
3. Checks process: "Was the machine (rubber-duck → ac-create) smooth?"
4. Discussion in grooming or async — designs are cheap to change

**Outcomes:**
- Approved → spec-design issue closes, spec-implement proceeds
- Not approved → back to Step 2 (rubber-duck), iterate

**Training phase (10-25 issues):** Every spec-design reviewed.
**Post-training:** Moves to grooming spot checks, eventually trust-based.

---

#### Review Type 2: Business Witness (VP/Delivery)

**When:** After Verification Session Step 10 — you add `review` label to the spec-implement issue.

**What happens (async):**
1. VP/Delivery opens feature on staging
2. Human witness (business): "Is this what I wanted?"
3. Freeform discovery — no checklist

**Outcomes:**
- Issues found → comment on issue → back to Implementation Session
- Approved → merge to production

**This never goes away** — it IS the accountability mechanism.

---

#### Logic

**Why two review types:** Type 1 catches design misunderstandings BEFORE expensive implementation (cheap to fix). Type 2 ensures the built thing serves business intent AFTER implementation (expensive to fix, but necessary).

**Why async:** Developer doesn't wait for review. Pick up next work. Review happens on Dev Lead's / VP's schedule.

**Why review exists even when trust is established:** Spec-quality review (Type 1) can eventually become trust-based. Business witness (Type 2) never goes away — it catches what specs can't anticipate.

---

### Blocked/Stuck (TBD)

*What to do when you're stuck or blocked during work.*

**To be defined:** Escalation path, try-before-asking threshold, documentation requirements.

**Current guideline:** From Position Agreement - "Try at least 3 different approaches (with AI assistance) before escalating design issues to Solution Architect."

---

## Gaps to Fill (Future Sessions)

### Skill/Tool Gaps
- [ ] **Rebase before AC Verify** — integrate into AC Verify skill (currently manual step)
- [ ] **Sub-issue board management** — when sub-issue completes review: remove parent link → add to board → move to review status (automation via GitHub Action or agent)

### Process Gaps
- [ ] Blocked/Stuck escalation protocol
- [ ] Grooming from Mohamed's perspective
- [ ] Priority visibility in GitHub

### Infrastructure Gaps
- [x] ~~Staging environment setup~~ — filled by [Deployment Pattern Standardization](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-pattern-standardization-design) (Step 7 routing table)
- [x] ~~Deploy to staging procedure~~ — filled by Step 7 deployment patterns (Docker Compose, Vercel Hobby, Caddy)
- [ ] Smoke test checklists (project-specific, per core flow)

### Company-Wide Gaps
- [ ] Company sections (1-3): Story, Products & Services, Policies

---

## Reference Documents

| Document | Purpose |
|----------|---------|
| [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) | Master routing — which path, which stage |
| [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) | Testing pyramid — AC → Smoke → Human Witness |
| [AC DoD Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) | Acceptance criteria methodology |
| [AC Verification Decision Trace](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-verification-decision-trace) | verification.jsonl schema and design rationale |
| [Three-Session Model](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/three-session-model) | Design / Implementation / Verification separation |
| [GROOMING.md](https://github.com/DaveX2001/deliverable-tracking/blob/main/GROOMING.md) | Daily sync process |

### Skills and Commands (Source: [claude-code-team-plugin](https://github.com/MariusWilsch/claude-code-team-plugin))

| Tool | Type | Used in |
|------|------|---------|
| [Worktree (GTR)](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/skills/worktree/SKILL.md) | AI-invoked skill | Implementation Step 4 |
| [`/ac-verify`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/ac-verify.md) | User-invoked command | Verification Step 4 |
| [`/ac-create`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/skills/ac-create/SKILL.md) | AI-invoked skill | Path A Step 3 |
| [`/issue-comment`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/issue-comment.md) | User-invoked command | Implementation Step 5, Verification Step 10 |
| [`/requirements-clarity`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/requirements-clarity.md) | User-invoked command | Implementation Step 3 |
| [`/implementation-clarity`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/implementation-clarity.md) | User-invoked command | Implementation Step 3 |
| [`/evaluation-clarity`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/evaluation-clarity.md) | User-invoked command | Implementation Step 3 |
| [`/merge`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/merge.md) | User-invoked command | Verification Step 6 |
| [`/rubber-duck`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/rubber-duck.md) | User-invoked command | Implementation Step 2, Verification Step 2 |
| [`/onboarding`](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/onboarding.md) | User-invoked command | Implementation Step 1, Verification Step 1 |

---

## Source

- E-Myth Management Strategy interview (2026-02-05) — Morning Start, Path A, Path B Implementation Session
- E-Myth Management Strategy interview (2026-02-06) — Path B Implementation Session corrections + Verification Session
- Framework: Michael Gerber, *The E-Myth Revisited*, Chapter 15
- Template: E-Myth Operations Manual Guide (MG-0080)
