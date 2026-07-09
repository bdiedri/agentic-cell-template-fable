# Initialize Project

Use this skill when the user wants to initialize a new project from the agentic build-cell template.

## Purpose

Transform a generic template repository into a project-specific agentic workspace.

This skill should not immediately build the final product, write final deliverables, or create implementation artifacts. It should first understand the project, identify missing context, ask structured questions, propose the project-specific workflow, and establish human approval gates.

## Invocation

Use this skill when the user invokes `/initialize-project [project title or short description]` or asks to start a new project from the template.

## Core Behavior

When this skill is invoked:

1. Inspect the current repository structure.
2. Read the standard template files:
   - `README.md`
   - `CLAUDE.md`
   - `/docs/project_brief.md`
   - `/docs/product_strategy_and_scope.md`
   - `/docs/source_authority_model.md`
   - `/docs/decision_log.md`
   - `/docs/assumptions_log.md`
   - `/docs/open_questions.md`
   - `/docs/agent_team_design.md`
   - `/docs/workflow_design.md`
   - `/docs/next_action_plan.md`
   - `/.claude/agents`
3. Determine whether the repo is still in template state or already initialized.
4. Ask the user structured project-formation questions.
5. Ask the user what source files exist and whether they should be uploaded to `/context/source-drop`.
6. Recommend the first initialization PR scope.
7. Do not proceed to build until the user answers the setup questions or explicitly instructs you to make reasonable assumptions.

## Structured Questions

Ask the minimum useful set of questions needed to initialize the project. Prefer a concise grouped format.

### Project Identity

1. What is the project title?
2. What is the short description of the project?
3. Is this primarily a code project, non-code artifact project, strategy project, research project, data project, or mixed project?

### Objective and Outputs

4. What problem is this project trying to solve?
5. What are the desired outputs or deliverables?
6. What does “done” look like for the first phase?

### Users and Stakeholders

7. Who is the primary user, customer, buyer, stakeholder, or audience?
8. Are there secondary users or reviewers whose needs should be considered?

### Strategy and Scope

9. Is there a north star or broader strategic vision?
10. What is in scope for the first phase?
11. What is explicitly out of scope?
12. Are there terms, claims, or messages that require caution?

### Constraints

13. What constraints should govern the work? Consider security, data sensitivity, platform, budget, timeline, policy, legal, regulatory, technical, and organizational constraints.

### Source Context

14. What source materials are available?
15. Which sources, if any, are authoritative?
16. Should source files be uploaded to `/context/source-drop` before further work?
17. Are any source files historical, speculative, stale, or reference-only?

### Decisions and Approval Gates

18. What decisions have already been made?
19. What decisions should Claude not make without human approval?
20. Are web research, external systems, or connectors authorized for this project?

### Agent Team and Workflow

21. Should Claude recommend the project-specific agent team?
22. Should Claude create project-specific agents immediately after approval, or only document the recommended team first?
23. Should work proceed through PRs only, or are direct commits acceptable during setup?

## Expected Outputs After User Answers

After the user answers the structured questions, create a branch from latest `main` and open a PR back into `main`.

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

During initialization, determine whether the project needs a persistent project-specific agent team.

If the user asks for Claude to recommend agents:
- Document the recommended team in `/docs/agent_team_design.md`.
- Clearly mark each agent as one of:
  - Persistent: file exists under `/.claude/agents/`
  - Recommended: proposed but not yet created
  - Simulated only: used as a temporary reasoning perspective

If the user approves creating the agent team:
- Create one Markdown file per approved persistent agent under `/.claude/agents/`.
- Update `/docs/agent_team_design.md` to show the file path for each created agent.
- Do not say the agent team is created unless the files exist.

If the user has not approved creation:
- Do not create project-specific agent files.
- Do not imply a persistent agent team exists.
- Recommend `/design-agent-team` as the next step if agent creation is needed.

The initialization PR should make clear whether agents were:
1. created as durable files,
2. recommended only, or
3. simulated as temporary perspectives.

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

If the user only provides a short project title or description, do not assume the full project setup. Ask the structured questions first.

## Completion Criteria

This skill is complete when:

1. The user has answered the required initialization questions or approved assumptions.
2. The project brief and control-plane docs are updated.
3. Initial decision gates are documented.
4. Source context needs are identified.
5. The recommended project-specific agent team is documented.
6. The next recommended skill or task is clear.
7. A PR is opened back into `main` and its number is recorded, or the reason it could not be opened is recorded in `/docs/next_action_plan.md`.
