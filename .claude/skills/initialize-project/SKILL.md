# Initialize Project

Use this skill when the user wants to initialize a new project from the agentic build-cell template.

## Purpose

Transform a generic template repository into a project-specific agentic workspace.

This skill should not immediately build the final product, write final deliverables, or create implementation artifacts. It should first understand the project, identify missing context, ask structured questions, propose the project-specific workflow, and establish human approval gates.

## Invocation

Use this skill when the user invokes `/initialize-project [project title or short description]` or asks to start a new project from the template.

## Core Behavior

Sequence within this skill is at your discretion — the lists below define what must be true, not choreography.

Required inputs:

- The current repository structure
- `README.md` and `CLAUDE.md`
- The standard `/docs` template files (project brief, strategy and scope, source authority model, decision log, assumptions log, open questions, agent team design, workflow design, next action plan)
- `/.claude/agents`

Required outputs:

- See "Expected Outputs After User Answers"

Invariants:

- Determine whether the repo is still in template state or already initialized before asking the user anything.
- Gather the Initialization Information Checklist below: infer what you responsibly can, ask only the rest.
- Ask what source files exist and whether they should be uploaded to `/context/source-drop`.
- Recommend the first initialization PR scope.
- Do not proceed to build until the user has answered the unanswerable checklist items or explicitly instructed you to make reasonable assumptions.

## Initialization Information Checklist

Before writing the project brief, you must know:

- **Identity:** project title, short description, and project type (code / non-code artifact / strategy / research / data / mixed)
- **Objective and outputs:** the problem being solved, the desired outputs or deliverables, and what "done" looks like for the first phase
- **Users and stakeholders:** the primary user, customer, buyer, or audience, and any secondary users or reviewers
- **Strategy and scope:** the north star or broader vision (if any), first-phase scope, explicit non-scope, and any terms, claims, or messages that require caution
- **Constraints:** whatever governs the work — security, data sensitivity, platform, budget, timeline, policy, legal, regulatory, technical, organizational
- **Source context:** what source materials exist, which are authoritative, whether they should be uploaded to `/context/source-drop` before further work, and which are historical, speculative, stale, or reference-only
- **Decisions and authorization:** decisions already made, decisions Claude must not make without human approval, and whether web research, external systems, or connectors are authorized
- **Agent team and workflow:** whether Claude should recommend the project-specific agent team, whether approved agents should be created immediately or documented first, and whether work proceeds through PRs only or direct commits are acceptable during setup

Answer what you can responsibly infer from the repository, the user's description, and any provided sources, and record every inferred answer as an assumption in `/docs/assumptions_log.md`. Batch only the genuinely unanswerable items into at most two rounds of questions, at most four questions per round, blockers first. Do not interrogate the user item by item.

## Expected Outputs After User Answers

After the checklist is resolved (answered or covered by approved assumptions), create a branch from latest `main` and open a PR back into `main`.

Create or update:

- `/docs/project_brief.md`
- `/docs/product_strategy_and_scope.md`, if relevant
- `/docs/source_authority_model.md`
- `/docs/decision_log.md`
- `/docs/assumptions_log.md`
- `/docs/open_questions.md`
- `/docs/agent_team_design.md`, including clear status for persistent, recommended, and simulated-only agents
- `/docs/workflow_design.md`
- `/docs/next_action_plan.md`

## Persistent Agent Team Handling

During initialization, determine whether the project needs a persistent project-specific agent team. Apply the canonical Persistent Agent Team Rules in `CLAUDE.md` — an agent is persistent only if its Markdown file exists under `/.claude/agents/`.

- If the user asks for recommendations: document the team in `/docs/agent_team_design.md`, marking each agent Persistent, Recommended, or Simulated Only.
- If the user approves creation: create one file per approved agent under `/.claude/agents/` and record each file path in `/docs/agent_team_design.md`.
- If creation is not approved: document the recommended team only, and recommend `/design-agent-team` as the next step if agent creation is needed.

The initialization PR should state which of those three outcomes applies.

## Recommended First PR

The first PR should usually be titled: `Initialize project workspace`.

It should include:

1. Completed project brief
2. Initial scope and non-scope
3. Source context plan
4. Initial decision log
5. Initial assumptions log
6. Initial open questions
7. Recommended project-specific agent team
8. Recommended next action plan

## Guardrails

- Do not build app code during initialization.
- Do not generate final deliverables during initialization.
- Do not create project-specific specialist agents unless the user approves.
- Do not run web research unless explicitly authorized.
- Do not treat raw source files as authoritative.
- Do not proceed past human approval gates.
- Do not make unsupported legal, contractual, pricing, security, regulatory, or acquisition claims.
- Distinguish facts, assumptions, source-supported claims, inferences, and recommendations.

## If Source Files Are Needed

If the project requires source files, instruct the user to upload them to `/context/source-drop`.

Then recommend running `/ingest-context` after the files are uploaded.

## If the User Provides Only a Short Description

If the user only provides a short project title or description, do not assume the full project setup. Work through the Initialization Information Checklist first: infer what you responsibly can, log those inferences as assumptions, and ask about the rest.

## Completion Criteria

This skill is complete when:

1. The Initialization Information Checklist is resolved — items answered by the user, or covered by logged assumptions the user has accepted.
2. The project brief and control-plane docs are updated.
3. Initial decision gates are documented.
4. Source context needs are identified.
5. The recommended project-specific agent team is documented.
6. The next recommended skill or task is clear.
7. A PR is opened back into `main` and its number is recorded, or the reason it could not be opened is recorded in `/docs/next_action_plan.md`.
