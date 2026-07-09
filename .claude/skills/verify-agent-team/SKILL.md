# Verify Agent Team

Use this skill to audit whether the project-specific agent team is persistent, recommended only, or simulated.

## Purpose

Prevent confusion between durable project-level subagents and temporary role-based reasoning.

## Invocation

Use this skill when the user invokes `/verify-agent-team` or asks whether the agent team has actually been created.

## Core Behavior

Sequence within this skill is at your discretion — the lists below define what must be true, not choreography.

Required inputs:

- `/docs/agent_team_design.md`
- `/.claude/agents/`

Required outputs:

- See "Expected Outputs"

Invariants:

- Every agent or perspective mentioned in project documentation carries the correct status: Persistent, Recommended, Simulated Only, Deferred, or Retired.
- Persistent status is claimed only where a Markdown agent file actually exists.
- Inconsistencies between project documentation and actual files are flagged.
- A recommendation is made on whether any recommended or simulated-only agents should become persistent.

## Expected Outputs

Create or update:

- `/docs/agent_team_design.md`
- `/docs/open_questions.md`, if human approval is needed
- `/docs/next_action_plan.md`, if agent creation is recommended

## Guardrails

- Do not create new agent files unless the user explicitly approves.
- Do not call an agent persistent unless its file exists.
- Do not treat simulated perspectives as durable agents.
- Preserve existing agent files unless explicitly told to modify them.

## Completion Criteria

This skill is complete when:

1. Existing persistent agents are identified.
2. Recommended agents are identified.
3. Simulated-only perspectives are identified.
4. Inconsistencies are documented.
5. Recommended next action is clear.
6. If files are changed, a PR is opened back into `main` and its number is recorded, or the reason it could not be opened is recorded in `/docs/next_action_plan.md`.
