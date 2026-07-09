# Design Agent Team

Use this skill to recommend and, when approved, create the project-specific agent team.

## Purpose

Design a focused set of specialist agents based on the project objective, constraints, source context, desired outputs, and workflow needs.

## Invocation

Use this skill when the user invokes `/design-agent-team` or asks what agents are needed for a project.

## Core Behavior

When this skill is invoked:

1. Review:
   - `/docs/project_brief.md`
   - `/docs/product_strategy_and_scope.md`
   - `/docs/agent_team_design.md`
   - `/docs/workflow_design.md`
   - `/docs/decision_log.md`
   - `/context/source_artifact_index.md`
   - `/context/extracted`, if available
2. Determine what specialist perspectives are needed.
3. Recommend a focused project-specific agent team.
4. Explain why each agent is needed.
5. Identify which agents should be created now versus deferred.
6. Identify overlap, redundancy, or missing perspectives.
7. Update `/docs/agent_team_design.md`.
8. Do not create agent files unless the user explicitly approves persistent agent creation.
9. If approved, create one Markdown definition file per persistent agent under `/.claude/agents`.
10. After creating files, update `/docs/agent_team_design.md` with each agent’s status and file path.
11. If agents are only used as perspectives during analysis, label them as simulated-only and do not imply they were created.

## Persistent Agent Creation Rules

This skill must distinguish between recommending an agent team and creating a persistent agent team.

A persistent agent exists only if there is a corresponding Markdown file under `/.claude/agents/`.

When recommending agents:
- Update `/docs/agent_team_design.md`.
- Mark each agent as `Recommended`.
- Do not imply the agent exists as a persistent subagent.

When creating agents after user approval:
- Create one file per approved agent under `/.claude/agents/`.
- Use kebab-case filenames, such as `pricing-commercial-model.md`.
- Include valid YAML frontmatter with `name` and `description`.
- Include Role, When to Use, Responsibilities, Forbidden Responsibilities, Required Inputs, Expected Outputs, Review Checklist, and Human Approval Requirements.
- Update `/docs/agent_team_design.md` and mark each created agent as `Persistent`.
- Include the file path for each persistent agent.

When using temporary role-based analysis:
- Mark the role as `Simulated Only`.
- Do not call it a created agent.
- Do not add it to the persistent agent list unless the user approves creation.

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
- Devil’s Advocate / Red Team

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
5. A PR is opened back into `main`.
