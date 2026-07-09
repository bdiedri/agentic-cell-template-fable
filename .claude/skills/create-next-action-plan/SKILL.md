# Create Next Action Plan

Use this skill to recommend the next concrete project step after reviewing the current repo state.

## Purpose

Convert the current project state into a clear, actionable next-step plan.

## Invocation

Use this skill when the user invokes `/create-next-action-plan`, asks “what next,” or asks how to proceed after a PR, decision, or phase.

## Core Behavior

When this skill is invoked:

1. Review:
   - `/docs/project_brief.md`
   - `/docs/product_strategy_and_scope.md`
   - `/docs/decision_log.md`
   - `/docs/assumptions_log.md`
   - `/docs/open_questions.md`
   - `/docs/agent_team_design.md`
   - `/docs/workflow_design.md`
   - `/context/source_artifact_index.md`
   - `/context/extracted`, if available
2. Assess current project stage.
3. Identify what has been completed.
4. Identify unresolved blockers.
5. Recommend the next concrete action.
6. Identify inputs needed for that action.
7. Identify expected outputs.
8. Recommend whether the next step should be a skill invocation, agent team design task, source ingestion task, artifact generation task, implementation task, review task, or research task.
9. Update `/docs/next_action_plan.md`.

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
7. A PR is opened back into `main`.
