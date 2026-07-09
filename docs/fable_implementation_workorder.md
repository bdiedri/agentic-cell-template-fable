# Fable Implementation Work Order — agentic-cell-template-fable

> ## STOP — read before acting
> This file is a **spec, not a "go" signal.** Do not implement all of it at once.
>
> - Implement **Batch 1 only.** Then open a PR into `main` and **stop.** Do not merge it. Do not start Batch 2 until a human approves that PR. Repeat for each later batch only when a human names it.
> - If a batch has not been named for this run, assume **Batch 1**.
> - You decide *how* within a batch; the item order is not choreography.
> - Ask only load-bearing questions you cannot responsibly infer. Log inferred choices as assumptions.
> - Where this file conflicts with existing repo files or the prior `pilot-*` handoff files, **this file wins** — edit those files to match, do not duplicate them.
> - In your PR description, list each item in the batch with the artifact that satisfies it, and flag anything you were unsure about rather than asserting it.

## What this is

An approved, reconciled change plan for this template repo. It merges two independent audits: the external assessment and your own `Fable_5_Fit_Audit.docx` (keep that file in the repo as `docs/fable-audit.md` — this work order references its finding numbers, e.g. 5.2, so the trail stays intact).

This is a spec of **what to change and why**, with acceptance criteria. **You decide how.** Sequence work within a batch as you see fit. Do not treat the ordering inside a batch as choreography.

## How to run it (global rules)

- **One batch = one PR.** Implement a batch on its own branch, open a PR into `main`, and stop. Do not start the next batch until the human approves the prior PR.
- **Do not merge your own PRs.** Opening the PR is where your turn ends.
- **Ask only load-bearing questions** you cannot responsibly infer. Record inferred choices as assumptions. Do not block on trivia.
- **Precedence:** where this work order conflicts with existing `CLAUDE.md` rules or with the prior handoff files (the four `pilot-*` agents and the `run-parallel-pilots` skill already in the repo), **this work order wins** — edit those files to match it rather than duplicating them.
- **Preserve, do not strip** (see "Guardrails to keep" at the end). Several audit findings recommend relaxing controls; the accepted relaxations are listed explicitly in Batch 3. Anything not listed there stays.

## Inputs to read first

`CLAUDE.md`, `docs/fable-audit.md`, the prior handoff files (`.claude/agents/pilot-*.md`, `.claude/skills/run-parallel-pilots/SKILL.md`), and `CLAUDE_md_additions.md` if present (its Blocks 1–4 are early drafts of Batch 1 items — reconcile, don't duplicate).

---

## Batch 1 — Additive safety + routing controls (lowest risk: these only add guards)

**1.1 (audit 3.1) — Model / effort / tools frontmatter on every agent.**
Add to all six boot agents and the four pilot agents. Guidance:
- `source-context`: `model: sonnet`, `effort: medium`; tools omit web tools (no WebSearch/WebFetch) — it is told not to research; make that enforceable.
- `decision-gate`: `model: inherit`, `effort: high`; tools omit `Bash` so it cannot push commits (its job is to stop work, not ship it).
- `project-formation`, `strategic-scoping`, `team-design`, `workflow-architect`: `model: inherit`, `effort: high`.
- Pilot agents keep the frontmatter already specified in their files.
Path-level scoping (e.g. "only edit the three logs") is not a `tools:` capability — enforce it by instruction now, and optionally via `.claude/settings.json` deny rules in item 1.5.
*Acceptance:* every agent has `model`, `effort`, and a `tools` line; verify by loading one edited agent in a fresh session before rolling out (frontmatter keys are supported, but confirm this deployment honors them).

**1.2 (audit 3.2) — Evidence-ground every completion claim.**
Add to `CLAUDE.md`: "Any claim that work is complete must cite its artifact — commit SHA, PR number/URL, or file paths. A claim without an artifact is a plan, not progress." Amend every skill's completion criteria from "A PR is opened back into main" to "A PR is opened and its number is recorded, **or** the reason it could not be opened is recorded in `next_action_plan.md`."
*Acceptance:* `CLAUDE.md` carries the rule; no skill asserts an unverifiable completion state with no honest exit.

**1.3 (audit 2.3) — Standing authorization.**
Add to `CLAUDE.md` gate section: "An approval recorded in `/docs/decision_log.md` with status Approved is standing authorization for the described work — do not re-request it."
*Acceptance:* the rule exists and is referenced from the gate section.

**1.4 (audit 3.5) — Blocked-at-gate landing protocol.**
Add to `CLAUDE.md`: when blocked at an unapproved gate mid-run — record the open question, push work-in-progress as a **draft** PR, update `next_action_plan.md`, append a worklog entry (1.6), and end the turn. Never abandon in-flight work to "stop."
*Acceptance:* the protocol is stated once, canonically, and `decision-gate.md` points to it.

**1.5 (audit 3.4) — Permission allowlist. REVIEW THIS ONE MOST CAREFULLY.**
Add `.claude/settings.json` allowing read-only commands, `git add/commit/push`, and writes scoped to `/docs`, `/context/extracted`, `/data`. **Scope tightly** — do not allowlist broad `Bash`. This is the one Batch 1 item that removes a human prompt from real commands, so keep it narrow, or defer it to after your first supervised pilot run if you'd rather not grant it yet.
*Acceptance:* allowlist is present and scoped to the directories above; no wildcard shell allow.

**1.6 (audit 3.3) — Session worklog (supersedes the earlier `run_notes.md` idea).**
Add `/docs/worklog.md`, append-only: date, session purpose, branch, commits/PRs with numbers, state left behind, surprises, and an optional one-line Lesson. Add a `CLAUDE.md` rule that every session appends an entry before ending. This doubles as the evidence store for 1.2. Do **not** also create `run_notes.md` — fold its "lessons" purpose into the worklog entry.
*Acceptance:* `worklog.md` exists with a template entry; `CLAUDE.md` requires an entry per session.

*Batch 1 done when:* PR opened, all six items present, and one edited agent has been loaded in a fresh session to confirm frontmatter is honored.

---

## Batch 2 — Pilot orchestration layer (the structural batch — review hard)

Start from the four `pilot-*` files already in the repo and **modify them** per the findings below; do not create parallel copies.

**2.1 (audit 5.1) — Pilot charter.**
Add `/docs/pilot/pilot_charter.md` defining, before any run: the two teams, run mode (`comparison` vs `throughput`), each team's distinct mandate, isolation mechanics (2.5), budget, and end conditions.

**2.2 (audit 5.2) — Per-team namespaces + team-prefixed IDs. SUPERSEDES the prior pilot files' paths.**
Change team outputs from `/docs/pilots/team-a/` to `/docs/team-a/` and `/docs/team-b/`, each with their **own** logs and **team-prefixed IDs** (`A-D-001`, `B-D-001`). Root `/docs` logs are written by the **orchestrator only**. Update `pilot-team-a.md`, `pilot-team-b.md`, and the `run-parallel-pilots` skill accordingly.
*Acceptance:* no shared log is writable by a team; IDs cannot collide across teams.

**2.3 (audit 5.3) — Branch-rule exception + integration branches. SUPERSEDES "PR into main" for teams.**
Add one paragraph to `CLAUDE.md` branch rules: "Exception: when a pilot workflow is active per `/docs/pilot/`, teams branch from and PR into their designated integration branch (`pilot/team-a`, `pilot/team-b`); only the orchestrator merges integration branches toward `main`, at checkpoints." Change the teams' and skill's "open a PR into main" to "into the designated integration branch."
*Acceptance:* the charter's branch topology no longer contradicts `CLAUDE.md`.

**2.4 (audit 5.4) — Pre-record gate approvals in the charter.**
The charter must contain scoped, Approved decision-log entries (e.g. "implementation of the pilot within scope is approved for both teams") so an autonomous launch against the empty decision log does not stall at a gate. Relies on 1.3.
*Acceptance:* launching the pilot does not immediately hit an unapproved gate.

**2.5 (audit 5.6) — Document isolation mechanics in the charter.**
State it explicitly: teams-as-worktrees for a single-session pilot, teams-as-sessions with team branches for a multi-session pilot; either way root `/docs` is orchestrator-only.

**2.6 (audit 5.5) — Comparison rubric + blinding.**
Add `/docs/pilot/comparison_rubric.md` (criteria, weights, evidence required per criterion), written and approved **before** launch. The verifier reviews one report at a time and never sees the other team's output; the final comparison masks team identity where feasible. Refine `pilot-verifier.md` to reference the rubric.
*Acceptance:* rubric is pre-registered; verifier cannot cross-contaminate.

*Batch 2 done when:* PR opened; a dry-run of `/run-parallel-pilots` on a throwaway toy pilot produces two independent report stubs in separate namespaces on separate integration branches, with no shared-file writes and no ID collision.

---

## Batch 3 — Relaxations (judgment zone — accept only what's listed)

**Accept as written:**
- **3.1 (audit 1.1)** Replace the 23-question script in `initialize-project` with an *information checklist*: answer what you can infer, log those as assumptions, and batch only genuinely unanswerable items into ≤2 question rounds of ≤4 each, blockers first. (Subsumes `CLAUDE_md_additions.md` Block 5.)
- **3.2 (audit 1.2)** Scope the 6-part decision format to material decisions (things that belong in `decision_log.md`); allow trivial choices to be one sentence with the recommendation inline. Keep the format mandatory in the log files.
- **3.3 (audit 2.4)** Rename the "Devil's Advocate / Red Team" category to "Critical Review / Devil's Advocate."
- **3.4 (audit 2.2)** Add one line: a simulated perspective is a summarized viewpoint with a stated conclusion, not a dramatized dialogue or a transcript of deliberation.
- **3.5 (audit 4.2)** Note in `plain-language-review`'s header that it is for auditing accumulated/legacy artifacts, not a routine pass over fresh output.

**Accept with modification (do NOT strip the guard):**
- **3.6 (audit 4.1)** Consolidate the agent-persistence rule to **one** canonical statement in `CLAUDE.md`; replace the paraphrases in `README.md` and the two skills with a reference to it; keep the status column in `agent_team_design.md`; **keep** `verify-agent-team`. Do not delete the guard — this template is reused across models, and a cheaper model still needs it.
- **3.7 (audit 1.3)** Convert skills' "Core Behavior" to "Required inputs / outputs / invariants" and add "sequence is at your discretion." **Do not** add "skip reads for files already in context" — on long runs a summarized file can masquerade as present; re-read when in doubt.

**Hold / defer (do not implement now):**
- **audit 4.3 (mega-tables → per-entry files)** — the per-team namespacing in 2.2 already removes most of the parallel-write conflict this was aimed at. It's a large, high-churn migration on speculation, and you yourself flagged uncertainty about external tooling reading the tables. Defer until a real pilot run shows table merge conflicts actually bite.
- **audit 3.6 general `/verify-phase` skill** — the pilot-specific verifier (2.6) covers the pilot. A general phase-verification skill is worth building later; not now.

*Batch 3 done when:* PR opened; the listed items are in; the held items are explicitly left untouched.

---

## Guardrails to keep (do not remove in any batch)

The decision / assumption / open-question control plane; the source-authority model; the human approval gates; the distinction between facts, assumptions, inferences, and recommendations; the canonical agent-persistence rule; and the evidence-grounding rule added in 1.2. Where a finding's rationale is "I (Fable) don't have that failure mode," treat that as a reason to keep the guard for other models, not to delete it.

## Decisions still owned by the human

- Run mode + each team's distinct mandate (fill in the charter, 2.1).
- Whether to include the permission allowlist now or defer it (1.5).
- Whether to ever do the table migration (audit 4.3), post-pilot.
