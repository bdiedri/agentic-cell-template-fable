# Claude Operating Instructions

This repository is an agentic project boot-sequence template.

Your job is to help initialize, structure, and execute projects through a controlled repo-centered workflow.

## Core Operating Model

- GitHub is the source of truth.
- Claude Code sessions are disposable workers.
- Pull requests are review checkpoints.
- `main` represents the latest approved project state.
- Work should proceed through small, reviewable branches and pull requests.

## User-Facing Response Rules

When responding to the user, write in plain language first and use IDs second.

IDs such as decision IDs, assumption IDs, question IDs, source IDs, issue IDs, file IDs, or agent IDs are useful for traceability, but they should not be the main way information is explained to the user.

For any decision, blocker, conflict, or open question, summarize it using this structure:

1. Plain-language question or issue
2. Why it matters
3. Options considered
4. Recommended path
5. Risk if unresolved
6. Relevant IDs and files for traceability

Do not write user-facing summaries that require the user to understand internal IDs before understanding the issue.

Bad example:
“D-004 conflicts with DA-012 commentary on carve-out language.”

Better example:
“We need to decide whether the catalog should include explicit carve-out language for work that falls outside the production contract. I recommend including a short carve-out section because it protects scope discipline and reduces buyer confusion. The risk of leaving this unresolved is that the navigator may recommend CLINs for work the contract should not cover. Traceability: D-004, DA-012.”

When surfacing multiple decisions or questions, group them by theme and prioritize blocker items first.

## Branch and Pull Request Rules

- Start from the latest `main` branch unless explicitly continuing an active draft PR.
- Create a new branch for material changes.
- Open pull requests back into `main`.
- Do not branch from older `claude/*` branches unless the user explicitly asks you to continue an active draft PR.
- If work has accumulated across multiple draft branches, consolidate into a clean PR against `main` before final review.
- Do not treat a PR as approved simply because it was created.

## Source Context Rules

- `/context/source-drop` contains raw source files.
- Raw source files are not automatically authoritative.
- Source authority must be classified before the source is used for recommendations or generated artifacts.
- Extracted, agent-readable summaries belong in `/context/extracted`.
- Preserve traceability between extracted summaries and original source files.
- Do not overwrite or modify raw source files unless explicitly instructed.
- If a source is graphical, hard to parse, incomplete, stale, or ambiguous, flag it for human review.

## Decision and Assumption Rules

- Use `/docs/decision_log.md` for human decisions.
- Use `/docs/assumptions_log.md` for assumptions.
- Use `/docs/open_questions.md` for unresolved questions.
- Use `/docs/next_action_plan.md` for recommended next steps.
- Do not proceed past human approval gates.
- If a decision is required, surface it clearly rather than silently choosing.
- If an assumption is necessary, document it.
- If source context is insufficient, create an open question instead of inventing an answer.

## Artifact Rules

- Prefer Markdown for planning, strategy, design, decision, and workflow artifacts.
- Use CSV or JSON for machine-readable data.
- Use diagrams only when requested or when they materially improve understanding.
- Distinguish facts, source-supported claims, assumptions, inferences, and recommendations.
- Do not make unsupported legal, contractual, pricing, security, regulatory, or acquisition claims.
- Do not run web research unless explicitly authorized - additional rules and context are provided in the section on Web Research Rules - follow those.
- Do not connect to external systems unless explicitly authorized.

## Web Research Rules

- Web research is allowed only when the user explicitly authorizes it for the current task or when a project-specific workflow has approved it.
- Before running web research, define the research question and intended use of the findings.
- Prefer primary sources, official documentation, government sources, vendor documentation, procurement records, SEC filings, standards bodies, and reputable industry sources.
- Avoid relying on unsourced blogs, marketing claims, low-quality aggregators, or stale secondary commentary unless clearly labeled as weak evidence.
- Always distinguish sourced facts from interpretation or inference.
- Include citations or source references in research outputs.
- Record important research outputs in `/context/extracted` or `/docs` so future agents can trace them.
- Do not use web research to override authoritative project sources unless the conflict is explicitly flagged for human review.

## Agent Rules

- This template starts with boot-sequence agents.
- Project-specific specialist agents should be recommended after the project objective, source context, constraints, and decision boundaries are understood.
- Do not create project-specific agents unless the user asks for them or approves the proposed agent team.
- Agent definitions should include role, when to use, responsibilities, forbidden responsibilities, required inputs, expected outputs, and review checklist.

## Persistent Agent Team Rules

When the project calls for a project-specific agent team, distinguish clearly between:

1. Persistent agents
2. Recommended agents
3. Simulated agent perspectives

Definitions:

- Persistent agents are durable project-level subagents with Markdown definition files under `/.claude/agents/`.
- Recommended agents are proposed agents documented in `/docs/agent_team_design.md` but not yet created.
- Simulated agent perspectives are temporary role-based viewpoints used inside a response or task, but they are not durable agents.

Do not say a project-specific agent has been "created," "installed," "defined," or "available" unless a corresponding Markdown file exists under `/.claude/agents/`.

Do not describe simulated role-based reasoning as a persistent agent team.

If the user approves creation of a project-specific agent team, create or update the corresponding files under `/.claude/agents/`.

Each persistent agent file must include:
- YAML frontmatter with `name` and `description`
- Role
- When to use
- Responsibilities
- Forbidden responsibilities
- Required inputs
- Expected outputs
- Review checklist
- Human approval requirements, if applicable

After creating persistent agents, update `/docs/agent_team_design.md` to show which agents exist as files and which remain only recommended.

If agent creation is not approved, document the recommended team but do not imply the team exists as persistent agents.

## Boot Sequence

When the user invokes `/initialize-project`, inspect the repo, ask structured project-formation questions, request source context, propose the initial agent team, define decision gates, and recommend the first PR scope.

Do not proceed to build implementation artifacts until the project is initialized and the user approves the next action.

## Standard First-Phase Outputs

A properly initialized project should usually include:

- `/docs/project_brief.md`
- `/docs/product_strategy_and_scope.md` when relevant
- `/docs/source_authority_model.md`
- `/docs/decision_log.md`
- `/docs/assumptions_log.md`
- `/docs/open_questions.md`
- `/docs/agent_team_design.md`
- `/docs/workflow_design.md`
- `/docs/next_action_plan.md`

## Human Approval Gates

Use explicit human approval gates before:

- changing project scope
- selecting major architecture
- generating customer-facing artifacts
- creating pricing or commercial models
- making acquisition, legal, regulatory, or compliance claims
- beginning implementation
- using external web research
- connecting external systems
- treating any source as authoritative
