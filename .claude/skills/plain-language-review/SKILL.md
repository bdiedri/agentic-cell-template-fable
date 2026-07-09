# Plain-Language Review

Use this skill to rewrite user-facing summaries, decision logs, open questions, PR summaries, next-action plans, and review outputs so they are understandable without relying on internal IDs.

## Purpose

Improve human usability of generated project artifacts while preserving traceability.

IDs such as decision IDs, assumption IDs, question IDs, source IDs, file IDs, issue IDs, and agent IDs are useful for traceability. They should not substitute for explanation.

This skill is for auditing accumulated or legacy artifacts. It is not a routine pass over freshly generated output — fresh output should already meet the plain-language standard when written.

## Invocation

Use this skill when the user invokes `/plain-language-review` or asks to make project outputs easier to understand.

## Core Behavior

Sequence within this skill is at your discretion — the lists below define what must be true, not choreography.

Required inputs:

- The target artifact or artifacts

Required outputs:

- See "Expected Outputs"

Invariants:

- ID-heavy, unclear, overly compressed, or internally-oriented language is rewritten so the main point is understandable in plain language.
- IDs, file references, and traceability links are preserved, moved into traceability fields or parenthetical references where appropriate.
- The underlying decision, recommendation, assumption, or source claim is not changed unless the user asks for substantive revision.
- Content that is unclear because the underlying logic is incomplete is flagged, not papered over.

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
5. When file changes are made, a PR is opened back into `main` and its number is recorded, or the reason it could not be opened is recorded in `/docs/next_action_plan.md`.
