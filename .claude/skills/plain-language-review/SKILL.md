# Plain-Language Review

Use this skill to rewrite user-facing summaries, decision logs, open questions, PR summaries, next-action plans, and review outputs so they are understandable without relying on internal IDs.

## Purpose

Improve human usability of generated project artifacts while preserving traceability.

IDs such as decision IDs, assumption IDs, question IDs, source IDs, file IDs, issue IDs, and agent IDs are useful for traceability. They should not substitute for explanation.

## Invocation

Use this skill when the user invokes `/plain-language-review` or asks to make project outputs easier to understand.

## Core Behavior

When this skill is invoked:

1. Review the target artifact or artifacts.
2. Identify ID-heavy, unclear, overly compressed, or internally-oriented language.
3. Rewrite the content so the main point is understandable in plain language.
4. Preserve IDs, file references, and traceability links.
5. Move IDs into traceability fields or parenthetical references where appropriate.
6. Do not change the underlying decision, recommendation, assumption, or source claim unless the user asks for substantive revision.
7. Flag any content that is unclear because the underlying logic is incomplete.

## Review Targets

Potential targets include:

- `/docs/decision_log.md`
- `/docs/open_questions.md`
- `/docs/assumptions_log.md`
- `/docs/next_action_plan.md`
- `/docs/workflow_design.md`
- `/docs/agent_team_design.md`
- PR summaries
- source extraction summaries
- generated recommendations
- user-facing artifact summaries

## Rewrite Standard

For decisions, use:

- Plain-language decision needed
- Why it matters
- Options considered
- Recommended path
- Risk if unresolved
- Required human action
- Traceability

For open questions, use:

- Plain-language question
- Why it matters
- Recommended next step
- Risk if unanswered
- Needed for
- Traceability

For PR or task summaries, use:

- What changed
- Why it changed
- What still needs review
- What decisions are needed
- Recommended next step
- Traceability

## Guardrails

- Do not remove traceability IDs.
- Do not obscure source references.
- Do not rewrite weak evidence as strong evidence.
- Do not convert assumptions into facts.
- Do not approve decisions on behalf of the user.
- Do not silently resolve ambiguity.
- Preserve the difference between source-supported facts, assumptions, inferences, and recommendations.

## Expected Outputs

Create or update the reviewed artifacts with clearer plain-language summaries.

If the review reveals unresolved ambiguity, update:

- `/docs/open_questions.md`
- `/docs/decision_log.md`
- `/docs/assumptions_log.md`

as appropriate.

## Completion Criteria

This skill is complete when:

1. The reviewed material is understandable without relying on internal IDs.
2. Traceability IDs and file references are preserved.
3. Decisions, questions, and risks are stated plainly.
4. No substantive meaning is changed without approval.
5. A PR is opened back into `main` when file changes are made.
