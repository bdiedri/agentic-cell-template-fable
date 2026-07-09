# Create Next Action Plan

Use this skill to recommend the next concrete project step after reviewing the current repo state.

## Purpose

Convert the current project state into a clear, actionable next-step plan.

## Invocation

Use this skill when the user invokes `/create-next-action-plan`, asks “what next,” or asks how to proceed after a PR, decision, or phase.

## Core Behavior

Sequence within this skill is at your discretion — the lists below define what must be true, not choreography.

Required inputs:

- The `/docs` control-plane files (project brief, strategy and scope, decision log, assumptions log, open questions, agent team design, workflow design)
- `/context/source_artifact_index.md` and `/context/extracted`, if available

Required outputs:

- See "Expected Outputs"

Invariants:

- The current project stage, completed work, and unresolved blockers are assessed before anything is recommended.
- The recommended next action is concrete, with its required inputs, expected outputs, and type (skill invocation, agent team design, source ingestion, artifact generation, implementation, review, or research).
- `/docs/next_action_plan.md` is updated.

## Response Format

When presenting the next action plan to the user, summarize in plain language.

Use this format:

1. What is the current situation?
2. What decision or action is needed next?
3. Why does it matter?
4. What are the options?
5. What do you recommend?
6. What is the risk if we do not decide?
7. What files or IDs support this?

Avoid ID-heavy summaries. IDs belong in the traceability section, not the headline.

## Next Step Types

Potential next step types include:

- Initialize project
- Upload source context
- Ingest context
- Surface decisions
- Design agent team
- Create project-specific agents
- Generate planning artifact
- Generate design artifact
- Generate data artifact
- Run research
- Build implementation artifact
- Review artifact
- Prepare customer-facing output
- Pause for human decision

## Guardrails

- Do not skip blocker decisions.
- Do not begin implementation unless the decision log allows it.
- Do not recommend web research unless it is necessary and authorization is explicit or requested.
- Keep the next step small enough for one reviewable PR.
- Prefer sequencing over large multi-phase tasks.

## Expected Outputs

Create or update:

- `/docs/next_action_plan.md`
- `/docs/decision_log.md`, if new decisions are identified
- `/docs/open_questions.md`, if new questions are identified

## Completion Criteria

This skill is complete when:

1. The current stage is clear.
2. The next action is specific.
3. Required inputs are listed.
4. Expected outputs are listed.
5. Blockers are identified.
6. A recommended Claude Code prompt or skill invocation is provided.
7. A PR is opened back into `main` and its number is recorded, or the reason it could not be opened is recorded in `/docs/next_action_plan.md`.
