# Design Agent Team

Use this skill to recommend and, when approved, create the project-specific agent team.

## Purpose

Design a focused set of specialist agents based on the project objective, constraints, source context, desired outputs, and workflow needs.

## Invocation

Use this skill when the user invokes `/design-agent-team` or asks what agents are needed for a project.

## Core Behavior

Sequence within this skill is at your discretion — the lists below define what must be true, not choreography.

Required inputs:

- `/docs/project_brief.md`
- `/docs/product_strategy_and_scope.md`
- `/docs/agent_team_design.md`
- `/docs/workflow_design.md`
- `/docs/decision_log.md`
- `/context/source_artifact_index.md`
- `/context/extracted`, if available

Required outputs:

- See "Expected Outputs"

Invariants:

- The recommended team is focused, with a stated rationale, a create-now-versus-defer call, and identified overlap, redundancy, or missing perspectives.
- `/docs/agent_team_design.md` is updated with each agent's persistence status (and file path, if created).
- No agent file is created without explicit user approval.
- Temporary role-based perspectives are labeled simulated-only and never described as created agents.

## Persistent Agent Creation Rules

Apply the canonical Persistent Agent Team Rules in `CLAUDE.md`: a persistent agent exists only if its Markdown file exists under `/.claude/agents/`, recommending a team never implies the agents exist, and simulated-only perspectives are never called created agents.

When creating agents after user approval:
- Create one file per approved agent under `/.claude/agents/`.
- Use kebab-case filenames, such as `pricing-commercial-model.md`.
- Include valid YAML frontmatter with `name` and `description`, plus `model`, `effort`, and `tools` lines (see the boot agents for the pattern).
- Include Role, When to Use, Responsibilities, Forbidden Responsibilities, Required Inputs, Expected Outputs, Review Checklist, and Human Approval Requirements.
- Update `/docs/agent_team_design.md`: mark each created agent as `Persistent` and record its file path.

If approval is ambiguous, stop and ask the user whether to create persistent agent files or only document the recommended team.

## Agent Design Criteria

Each recommended agent should include:

- Agent name
- Purpose
- When to use
- Responsibilities
- Forbidden responsibilities
- Required inputs
- Expected outputs
- Review checklist
- Human approval needs, if any
- Persistence status: Persistent / Recommended / Simulated Only / Deferred
- Agent file path, if persistent
- Creation approval source, if persistent

## Recommended Agent Categories

Only recommend categories that fit the project. Potential categories include:

- Product Strategy
- Project Management
- Domain SME
- System Architecture
- UX / Design
- Data Engineering
- AI / ML / Recommendation Logic
- Software Engineering
- QA / Testing
- Security / Compliance
- Pricing / Commercial Model
- Acquisition / Contracting
- Competitive Benchmarking
- Research
- Documentation
- Executive Communication
- Critical Review / Devil’s Advocate

## Guardrails

- Do not mimic a human org chart unnecessarily.
- Prefer fewer agents with sharper responsibilities.
- Do not create overlapping agents without justification.
- Do not create project-specific agents before user approval.
- Do not begin project execution.
- Surface missing context as open questions.

## Expected Outputs

Create or update:

- `/docs/agent_team_design.md`
- `/docs/decision_log.md`, if agent creation needs approval
- `/docs/open_questions.md`, if context is missing
- Optional: `/.claude/agents/[agent-name].md` only after explicit approval for persistent agent creation
- Updated `/docs/agent_team_design.md` showing each agent’s persistence status and file path, if created

## Completion Criteria

This skill is complete when:

1. A recommended agent team is documented.
2. Each agent has a clear rationale.
3. Agent creation decisions are surfaced.
4. The user knows which agents should be created next.
5. A PR is opened back into `main` and its number is recorded, or the reason it could not be opened is recorded in `/docs/next_action_plan.md`.
