# Fable 5 Fit Audit: `agentic-cell-template-fable` Scaffolding

> **Provenance:** Produced 2026-07-09 by Claude Code against the repo at its `Initial commit` state, at the user's request, as a report on how well this template's scaffolding fits the auditing model specifically. The finding numbers below (e.g. 3.2, 5.1) are referenced by `docs/fable_implementation_workorder.md`. This file is a record of the audit as delivered; do not renumber findings.

**Audited:** `CLAUDE.md`, all 6 agent definitions in `.claude/agents/`, all 7 skills in `.claude/skills/`, all 9 `docs/` templates, `context/` scaffolding, `README.md`. Also verified: there was **no** `.claude/settings.json`, no hooks, no `.github/` (no CI, no PR template).

**Overall assessment:** This is a well-designed governance template — the decision/assumption/open-question control plane, source-authority model, and PR-checkpoint discipline are genuinely useful and worth keeping. Its weaknesses for the auditing model specifically are (a) it was clearly hardened against a weaker model's failure modes (hallucinated agents, ID-soup output, silent decisions) and carries duplicated defensive scaffolding a stronger model does not need, and (b) it has almost nothing for the failure modes long autonomous runs *do* have: no model/effort routing, no evidence-grounding of completion claims, no session handoff, no permission pre-approval, and a shared-mutable-log design that breaks under parallel teams. Nothing here would trigger an outright refusal, but several standing prohibitions interact badly with a conservative model's disposition and would cause re-asking stalls in unattended runs.

---

## Category 1 — Instructions that over-specify HOW to think rather than WHAT to produce

### 1.1 The 23-question interrogation script

**File/section:** `.claude/skills/initialize-project/SKILL.md`, "Structured Questions".
**Issue:** The skill enumerates 23 questions in 8 fixed categories, and Core Behavior step 7 forbids proceeding until they're answered. The "ask the minimum useful set" line at the top is immediately undercut by the exhaustive script.
**Why it matters for a strong model:** Most of these can be answered from the repo state, the user's one-line description, and anything in `/context/source-drop` — inference is the strong model's comparative advantage. A scripted interrogation produces a wall of questions the user must answer serially, and it collides with interactive tooling (structured question prompts carry at most 4 questions per call), forcing either a 23-question prose dump or six question rounds. It also fights harness guidance against blocking on questions the user isn't present to answer.
**Severity:** High.
**Recommended change:** Reframe as an *information checklist* ("before writing the brief, you must know: objective, deliverables, definition of done, authoritative sources, pre-made decisions, authorization boundaries"). Instruct: answer what you can from available context, mark those as assumptions in `assumptions_log.md`, and batch only the genuinely unanswerable items into ≤2 question rounds (≤4 questions each), blockers first.

### 1.2 Mandatory 6-part format for every decision, question, or conflict

**File/section:** `CLAUDE.md`, "User-Facing Response Rules"; duplicated in `surface-decisions/SKILL.md` ("User-Facing Summary Requirement") and `create-next-action-plan/SKILL.md` ("Response Format", a 7-question format).
**Issue:** For **any** decision, blocker, conflict, or open question, the model must emit plain-language issue / why it matters / options / recommendation / risk / traceability. `create-next-action-plan` mandates a 7-part format even when the next step is trivially "run `/ingest-context`".
**Why it matters:** Response shape should calibrate to the question; this forces ceremony onto trivial choices and produces the bloated output the template's own plain-language rules are trying to prevent. For the *logs* the structure is right; for *chat responses* it over-specifies form.
**Severity:** Medium.
**Recommended change:** Scope the 6-part format to **material** decisions (anything that belongs in `decision_log.md`); explicitly allow trivial choices to be stated in one sentence with the recommendation inline. Keep the format mandatory in the log files, optional in chat.

### 1.3 Numbered micro-procedures in every skill

**File/section:** All 7 `SKILL.md` files, "Core Behavior" sections (e.g. `ingest-context`: "1. Inspect… 2. Read… 3. Review… 10. Open a PR").
**Issue:** Each skill scripts the exact read order and step sequence rather than stating required inputs, outputs, and invariants.
**Why it matters:** Mostly harmless as defaults, but they encourage wasted reads (every skill re-reads ~10 files even when they're already in context) and anchor the model to a sequence when a better order is obvious. The cost compounds on long runs where context is summarized and the reading list may be re-executed verbatim.
**Severity:** Low.
**Recommended change:** Convert "Core Behavior" to "Required inputs / Required outputs / Invariants," and note that sequence is at the model's discretion.

---

## Category 2 — Refusal risks and reasoning-echo instructions

### 2.1 Verified: no chain-of-thought transcription demands (good — keep it that way)

**File/section:** Whole repo, checked deliberately.
**Finding:** Nothing in this repo instructs the model to echo, transcribe, or explain internal reasoning as response text. The nearest things — "Rationale" and "Options Considered" columns in `docs/decision_log.md`, "Explain why each agent is needed" in `team-design.md` — are decision *documentation*, which is fine and unrelated to internal reasoning. Flagged explicitly as a verified non-issue and a property to preserve: if anyone later adds "show your full reasoning/thinking for each recommendation," that would conflict with how reasoning-mode models surface their reasoning (summarized, not transcribed) and should be phrased as "state your rationale and evidence" instead.
**Severity:** None (informational).

### 2.2 "Simulated agent perspectives … used inside a response"

**File/section:** `CLAUDE.md`, "Persistent Agent Team Rules" (definition 3).
**Issue:** Ambiguous phrasing invites dramatizing internal deliberation as multi-persona dialogue in response text.
**Why it matters:** Rendering simulated perspectives as an in-response debate transcript is verbose and blurs the line between actual reasoning and a rhetorical device — the same existence-confusion the template elsewhere works hard to prevent.
**Severity:** Low.
**Recommended change:** One clarifying sentence: "A simulated perspective is a *summarized viewpoint with a stated conclusion* (e.g., 'From a security standpoint: X, because Y'), not a dramatized dialogue or a transcript of deliberation."

### 2.3 Stacked standing prohibitions with no post-approval release valve

**File/section:** `CLAUDE.md` "Artifact Rules" + "Human Approval Gates"; echoed in every agent's "Forbidden Responsibilities" and every skill's "Guardrails."
**Issue:** The same prohibitions (no pricing/legal/acquisition claims, no web research, no external systems, stop at gates) are restated in ~15 places, but nothing states that an approval, once logged, is *standing authorization*. The template's example domain (CLINs, carve-outs, procurement) means pricing/acquisition content **is** the eventual deliverable.
**Why it matters:** This is the repo's most realistic "refusal-shaped" risk — not a hard refusal, but over-caution loops. A conservative model that sees a prohibition in the active skill's guardrails with no visible link to the approval that lifted it will stop and re-ask — which, unattended, means a stalled run. The gate system already has the answer (the decision log); it just never says the log *is* the authorization.
**Severity:** High (for autonomous runs specifically).
**Recommended change:** Add to the gate section: "An approval recorded in `/docs/decision_log.md` with status Approved is standing authorization for the described work — do not re-request it. When blocked at an unapproved gate mid-run: record the open question, push work-in-progress as a draft PR, update `next_action_plan.md`, and end the turn."

### 2.4 "Devil's Advocate / Red Team" agent category

**File/section:** `.claude/skills/design-agent-team/SKILL.md`, "Recommended Agent Categories".
**Issue:** "Red Team" here means decision red-teaming, but the label is ambiguous with offensive-security red teaming.
**Why it matters:** Models with dual-use safety measures pay attention to security-offense framing. In this repo's context it's clearly benign, but this template gets copied into arbitrary projects — a "Red Team agent" definition inside a security-adjacent project would draw scrutiny it doesn't need.
**Severity:** Low.
**Recommended change:** Rename the category "Critical Review / Devil's Advocate"; if a security-testing agent is ever genuinely wanted, require its definition to state the authorization context explicitly.

### 2.5 No model pins anywhere (cuts both ways)

**File/section:** All agent frontmatter (originally `name` + `description` only); no `settings.json`.
**Finding:** Nothing routes any work to a named model — so nothing can silently route to a fallback or stale pinned model. Good. But it also means there's no way to deliberately route mechanical work to a cheaper model. Covered as 3.1.

---

## Category 3 — Missing controls for long autonomous and parallel-subagent runs

### 3.1 No model / effort / tool assignment on any agent

**File/section:** All 6 files in `.claude/agents/` — frontmatter was `name` + `description` only.
**Issue:** No `model:`, no `tools:`, no effort guidance. Every boot agent inherits everything, including write/push capability. `decision-gate` — whose whole job is *stopping* work — can push commits; `source-context` is told "do not run web research" but has the tools to do it.
**Why it matters:** For an orchestrating model, the cost/quality lever is routing: source indexing is mechanical and should run on a cheaper model at lower effort; scoping and decision-gating are judgment work that should stay on the session model. Without frontmatter, routing is re-decided ad hoc every run, and without `tools:` restrictions the guardrails are purely instructional — a vigilance tax across the whole run instead of enforcement for free.
**Severity:** High.
**Recommended change:** Add `tools:` restrictions (e.g. `decision-gate`: no Bash; `source-context`: no web tools) and a `model:`/effort recommendation per agent (`source-context`: cheaper model, medium effort; judgment agents: inherit session model, high effort).

### 3.2 No evidence-grounding requirement for progress/completion claims

**File/section:** Every skill's "Completion Criteria" ("A PR is opened back into `main`"); no counterpart rule in `CLAUDE.md`.
**Issue:** The template is rigorous about one existence claim (agent files — see 4.1) but requires no evidence for any other claim: "PR opened," "all sources indexed," "logs updated" are all assertable without citation. Worse, completion criteria *require* asserting "a PR is opened" — in a permission-restricted or read-only session that's a criterion the model cannot truthfully satisfy, and the skill gives no honest exit.
**Why it matters:** On long runs, context gets summarized; a claim like "initialization PR opened" survives summarization, but the *evidence* for it doesn't unless it was written down. Grounded claims (PR number, commit SHA, file list) are what let a later session — or a verifier — distinguish done from described. This is the single control most needed before an unattended pilot.
**Severity:** High.
**Recommended change:** Add to `CLAUDE.md`: "Any claim that work is complete must cite its artifact: commit SHA, PR number/URL, or file paths. Claims without artifacts are plans, not progress." Amend every skill's completion criteria: "A PR is opened back into `main` **and its number is recorded**, or the reason it could not be opened is recorded in `next_action_plan.md`."

### 3.3 No cross-session handoff artifact

**File/section:** `CLAUDE.md` "Core Operating Model" ("Claude Code sessions are disposable workers") vs. the `docs/` set — `next_action_plan.md` is forward-looking only.
**Issue:** Sessions are declared disposable, but no file records what each session actually did: branch, commits, PRs, state left behind, anomalies hit.
**Why it matters:** Cross-run memory is exactly what disposable sessions lack. Reconstructing "what did the last session do" from `git log` plus a stale next-action plan is lossy, and it's where duplicate work and branch confusion come from.
**Severity:** Medium (High under the pilot).
**Recommended change:** Add `/docs/worklog.md` (append-only: date, session purpose, branch, commits/PRs with numbers, state left, surprises) and a `CLAUDE.md` rule that every session appends an entry before ending. Doubles as the evidence store for 3.2.

### 3.4 No permission pre-approval for unattended runs

**File/section:** Missing `.claude/settings.json` entirely.
**Issue:** No checked-in permission allowlist, no hooks. Every Bash/write call is subject to interactive prompting depending on session mode.
**Why it matters:** An "autonomous" run that stalls on a permission prompt at hour zero isn't autonomous. For a template whose premise is disposable worker sessions, the allowlist for the known-safe loop (git operations, writes under `/docs`, `/context/extracted`, `/data`) should ship with the repo.
**Severity:** Medium.
**Recommended change:** Check in a tightly scoped `.claude/settings.json` allowing standard read-only commands plus git add/commit/push and writes scoped to the template's output directories.

### 3.5 Gates say "stop" but never say *how* to stop

**File/section:** `CLAUDE.md` "Human Approval Gates"; `decision-gate.md` "Stop work at human approval gates."
**Issue:** No protocol for what a blocked autonomous session does with in-flight work.
**Why it matters:** "Stop" mid-run without a landing procedure means either lost work (session reclaimed, nothing pushed) or gate-creep (the model keeps going because stopping loses work). The template should make the safe action also the work-preserving one.
**Severity:** Medium.
**Recommended change:** The blocked-at-gate protocol from 2.3: draft PR + open question + `next_action_plan.md` + worklog entry + end turn.

### 3.6 No verification step for content, only for agent existence

**File/section:** `.claude/skills/verify-agent-team/SKILL.md` is the only verification skill in the repo.
**Issue:** Nothing verifies that extracted summaries actually trace to sources, that decision-log entries match what responses claimed, or that a finished phase satisfies its definition of done.
**Why it matters:** Independent checker subagents that try to falsify traceability and completion claims are cheap to orchestrate and are the main defense against confident-but-wrong summaries surviving a long run. The template verifies the one thing a weaker model got wrong once, and nothing else.
**Severity:** Medium (High under the pilot — verification is a named pilot role).
**Recommended change:** Add a `/verify-phase` skill: given a phase or PR, spawn independent checkers that (a) sample extracted claims back to raw sources, (b) confirm every completion claim's artifact exists, (c) report CONFIRMED/UNSUPPORTED per claim. Wire it in before any phase-gate approval request.

---

## Category 4 — Scaffolding that assumes a weaker model and could be relaxed

### 4.1 The agent-persistence anti-hallucination apparatus (5+ redundant copies)

**File/section:** `CLAUDE.md` "Persistent Agent Team Rules"; `README.md` "Agent Team Persistence"; `initialize-project/SKILL.md` "Persistent Agent Team Handling"; `design-agent-team/SKILL.md` "Persistent Agent Creation Rules"; the entire `verify-agent-team` skill; `docs/agent_team_design.md` status tables.
**Issue:** The rule "don't say an agent exists unless its file exists" is stated, restated, and given its own audit skill and status taxonomy. This is armor against a specific weaker-model failure: narrating a fictional agent team into existence.
**Why it matters:** A model that checks the filesystem before claiming file state doesn't carry this risk (its risk is 3.2's *work*-completion claims, which get no protection at all). Five paraphrases of one rule invite drift between copies and spend instruction-following budget the Category 3 controls actually need.
**Severity:** Medium.
**Recommended change:** Keep **one** canonical statement in `CLAUDE.md` and the status column in `agent_team_design.md`; replace the paraphrases with references to it. Keep `/verify-agent-team` as an audit tool. Redirect the freed emphasis to evidence-grounding of completion claims.

### 4.2 Plain-language rules as a repair loop

**File/section:** `CLAUDE.md` "User-Facing Response Rules" (with worked bad/good example); `surface-decisions` summary requirement; the entire `plain-language-review` skill.
**Issue:** Three layers of defense against ID-soup output, including a whole skill whose purpose is post-hoc repair of unreadable output.
**Why it matters:** Plain-language-first with IDs in a traceability line is a strong model's default register. The repair skill assumes the generator will fail and a second pass must fix it — with a strong model, prevention is one sentence and the repair loop rarely fires.
**Severity:** Low.
**Recommended change:** Keep the `CLAUDE.md` rule and its example (a good spec of the desired register). Keep `plain-language-review` but note in its header that it's for auditing *accumulated/legacy* artifacts, not a routine pass over fresh output.

### 4.3 Ten-column markdown mega-tables as the control plane

**File/section:** `docs/decision_log.md` (10 columns), `docs/assumptions_log.md` (9), `docs/open_questions.md` (8), `docs/agent_team_design.md`, `context/source_artifact_index.md`.
**Issue:** Wide single-table formats force terse cell content (fighting the plain-language rules), diff horribly, and make every writer contend on the same table rows.
**Why it matters:** Not a comprehension problem but a *coordination* one: single shared tables are the unit of merge conflict when parallel sessions write concurrently (see 5.2), and cramped cells push the real rationale out of the log and into chat, where it evaporates.
**Severity:** Medium.
**Recommended change:** Per-entry sections (`### D-004: <title>` with labeled fields) with a small summary table on top, or one file per entry. Append-only sections merge cleanly; tables don't. *(Deferred by the implementation work order pending evidence from a real pilot run.)*

### 4.4 The interrogation script and micro-procedures

Covered in 1.1 and 1.3 — both are weaker-model assumptions (can't infer, can't sequence) and the fixes there are the relaxations.

---

## Category 5 — Before running a two-team pilot (orchestrator, independent teams, verification, comparison)

### 5.1 No orchestrator exists — the boot agents can't do it

**File/section:** `.claude/agents/` (all six are sequential-phase advisors); `docs/workflow_design.md` (single linear 9-step workflow).
**Issue:** Nothing defines an orchestrator role: team spawning, model/effort routing, isolation boundaries, arbitration, or how team results come back. The workflow template has no concept of concurrent tracks.
**Why it matters:** The pilot would run on improvisation for its most failure-prone component — measuring ad-hoc choices, not a repeatable process. A second run wouldn't be comparable to the first, defeating a comparison pilot.
**Severity:** Blocker (for the pilot; irrelevant otherwise).
**Recommended change:** Add a pilot-orchestrator agent definition in the template's own format plus `docs/pilot/pilot_charter.md` defining teams, isolation, budget, and end conditions **before** the run.

### 5.2 Shared mutable logs + unnamespaced IDs = control-plane corruption under parallelism

**File/section:** `docs/decision_log.md`, `assumptions_log.md`, `open_questions.md`, `next_action_plan.md` (single shared files); ID schemes (`D-001`, `DA-012`) with no allocation rule anywhere.
**Issue:** Two independent teams following the template as written will both write the same log files on diverging branches and both mint `D-001`. Every skill also ends by opening a PR to `main`, forcing parallel teams to merge into the shared trunk *mid-pilot* — destroying the independence the pilot is supposed to compare, or producing conflict storms in the log tables.
**Why it matters:** This is the concrete mechanism by which a parallel run goes wrong: not bad reasoning, but two correct teams corrupting shared state. Traceability — the template's core value — is the first casualty, since colliding IDs make the audit trail ambiguous exactly when the comparison needs it.
**Severity:** Blocker (for the pilot).
**Recommended change:** Per-team namespaces (`/docs/team-a/`, `/docs/team-b/` with their own logs and team-prefixed IDs `A-D-001`); root logs written only by the orchestrator. Teams work on team integration branches with task PRs into their team branch; PRs to `main` only at orchestrator checkpoints.

### 5.3 Branch rules actively forbid the pilot's branch topology

**File/section:** `CLAUDE.md`, "Branch and Pull Request Rules".
**Issue:** A team-branch hierarchy (team branch → task branches → team PRs) violates "start from latest `main`," "do not branch from older `claude/*` branches," and "open pull requests back into `main`" as written.
**Why it matters:** `CLAUDE.md` is binding on the model. During the pilot, every team subagent would be in standing conflict between the charter and the constitution, each resolving it independently and possibly differently.
**Severity:** High.
**Recommended change:** Add one paragraph to the branch rules: "Exception: when a pilot/parallel workflow is active per `/docs/pilot/`, teams branch from and PR into their designated integration branch; only the orchestrator merges integration branches toward `main`."

### 5.4 Unapproved gates will stall both teams at launch

**File/section:** `CLAUDE.md` "Human Approval Gates" combined with the empty `docs/decision_log.md`.
**Issue:** An autonomous pilot launched against an empty decision log hits "do not proceed past human approval gates" almost immediately — twice, once per team.
**Why it matters:** Per 2.3, a conservative model's resolution of an ambiguous gate is to stop. Correct behavior, dead pilot.
**Severity:** Blocker (for the pilot).
**Recommended change:** The pilot charter must pre-record gate approvals as decision-log entries scoped to the pilot, relying on the standing-authorization rule from 2.3.

### 5.5 No verification or comparison protocol

**File/section:** Nothing in the repo (closest is `verify-agent-team`, which audits the wrong thing for this purpose).
**Issue:** The pilot's stated components include verification and comparison; the repo defines neither a rubric, nor who judges, nor how to prevent the judge from knowing which team produced what.
**Why it matters:** If the orchestrator is also the comparer with no pre-committed rubric, the comparison inherits whatever was improvised while watching both teams — anchoring and contamination included. Pre-registration and blinding are what make the judgment usable as a result.
**Severity:** High.
**Recommended change:** `docs/pilot/comparison_rubric.md` written and approved **before** launch (criteria, weights, evidence required per criterion); verification via independent checkers that never see the other team's output; final comparison performed against artifacts with team identity masked where feasible.

### 5.6 No isolation mechanics documented

**File/section:** Nothing in the repo.
**Issue:** Whether teams are separate sessions, separate worktrees, or subagent groups in one session is undefined — and each has different conflict behavior.
**Why it matters:** Worktree isolation exists for parallel file-mutating subagents; sibling sessions need branch isolation instead. The choice changes the whole conflict story from 5.2 and shouldn't be decided implicitly at spawn time.
**Severity:** Medium.
**Recommended change:** One section in the pilot charter: teams-as-worktrees for single-session pilots, teams-as-sessions with team branches for multi-session pilots; either way, root `docs/` is orchestrator-only.

---

## Top 5 changes recommended (as prioritized at audit time)

1. **Add the pilot orchestration layer** (5.1, 5.2, 5.3): pilot charter + orchestrator agent definition, per-team docs namespaces with team-prefixed IDs, team integration branches, and the `CLAUDE.md` branch-rule exception.
2. **Make approvals standing and stopping safe** (2.3, 3.5, 5.4): standing-authorization rule + blocked-at-gate landing protocol.
3. **Evidence-ground all completion claims + add the session worklog** (3.2, 3.3).
4. **Add model / effort / tools frontmatter to the agents** (3.1).
5. **Relax the weaker-model scaffolding** (1.1, 1.2, 4.1, 4.3): interrogation script → information checklist; 6-part format scoped to material decisions; agent-persistence rule deduplicated; log tables → per-entry sections.

## Flagged uncertainties (from the original audit)

- **Agent frontmatter support in this deployment.** `model:`/`tools:`/effort frontmatter keys are supported by Claude Code generally, but this specific managed deployment's handling was not verified at audit time. Test with one agent before relying on it.
- **Default subagent model when unspecified.** Believed to inherit the session model (so "no `model:` field" means no downgrade risk, only no ability to downgrade cheap work). If this platform instead defaults subagents to a smaller model, finding 3.1 escalates.
- **Pilot team mechanics.** Assumed "two independent teams" means parallel subagent groups or sibling sessions; if it means separate repos or human-supervised tracks, findings 5.2/5.6 change shape.
- **Whether any external tooling parses the docs tables.** If something machine-reads the 10-column tables, the per-entry-format recommendation (4.3) needs a compatible summary table preserved.
- **The history behind the agent-persistence apparatus.** Inferred to harden against a specific prior incident. If it's instead a contractual/audit requirement, keep the duplication and disregard the trimming half of 4.1.
