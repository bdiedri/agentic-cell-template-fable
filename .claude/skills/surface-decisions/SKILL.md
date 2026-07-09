# Surface Decisions

Use this skill to identify, structure, and prioritize decisions, assumptions, and open questions.

## Purpose

Prevent Claude Code from silently making important project decisions. Convert ambiguity into explicit decision records, assumptions, and open questions.

## Invocation

Use this skill when the user invokes `/surface-decisions` or asks what decisions are needed before the next phase.

## Core Behavior

Sequence within this skill is at your discretion — the lists below define what must be true, not choreography.

Required inputs:

- The `/docs` control-plane files (project brief, strategy and scope, source authority model, decision log, assumptions log, open questions, agent team design, workflow design, next action plan)
- `/context/source_artifact_index.md` and `/context/extracted`, if available

Required outputs:

- See "Expected Outputs"

Invariants:

- Decisions required before the next phase are identified and classified as blocker or non-blocker, with default choices recommended where appropriate.
- Assumptions currently being made and open questions requiring user input, source review, or research are captured.
- The decision, assumption, and open-question logs are updated.
- The next action is recommended.

## User-Facing Summary Requirement

When this skill responds to the user, do not lead with IDs.

Provide a plain-language summary with this format:

### Blocker Decisions

For each blocker decision:

- Decision needed:
- Why it matters:
- Options considered:
- Recommended path:
- Risk if unresolved:
- Traceability:

### Non-Blocker Decisions

Use the same format, but keep these shorter.

### Open Questions

For each major open question:

- Question:
- Why it matters:
- Recommended next step:
- Traceability:

IDs should appear only in the Traceability line unless the user specifically asks for ID-heavy output.

Do not write user-facing summaries that require the user to understand internal IDs before understanding the issue.

## Decision Fields

Each decision should include:

- Decision ID
- Decision title
- Category
- Status
- Current recommendation
- Options considered
- Rationale
- Risk if unresolved
- Required human action
- Related files

## Decision Categories

Use categories appropriate to the project, such as:

- Product Scope
- User / Audience
- Strategy
- Source Authority
- Architecture
- Data
- AI / ML
- UX / Design
- Pricing / Commercial
- Acquisition / Contracting
- Security / Compliance
- Research
- Implementation Sequencing
- Customer-Facing Messaging

## Guardrails

- Do not approve decisions on behalf of the user.
- Do not silently resolve blocker decisions.
- Do not bury decisions only in prose.
- Do not proceed to implementation if blocker decisions remain.
- Use assumptions only when acceptable for non-blocking progress.

## Expected Outputs

Create or update:

- `/docs/decision_log.md`
- `/docs/assumptions_log.md`
- `/docs/open_questions.md`
- `/docs/next_action_plan.md`

## Completion Criteria

This skill is complete when:

1. Blocker decisions are identified.
2. Non-blocker decisions are identified.
3. Assumptions are documented.
4. Open questions are actionable.
5. Next recommended action is clear.
6. A PR is opened back into `main` and its number is recorded, or the reason it could not be opened is recorded in `/docs/next_action_plan.md`.
