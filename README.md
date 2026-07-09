# Agentic Build Cell Template

This repository is a reusable Claude Code boot-sequence template for initializing, structuring, and executing agentic project workflows.

The template is designed for projects where Claude Code should help:
- understand the project objective
- ingest and organize source context
- identify required decisions
- recommend the right agent team
- create project-specific agents and workflows
- generate planning, strategy, design, data, code, or documentation artifacts through pull requests

The repository is intentionally project-neutral. It should be copied into a new project repo, then initialized through Claude Code.

## Intended Workflow

1. Create a new repository from this template.
2. Open Claude Code.
3. Connect Claude Code to the new repository.
4. Run `/initialize-project [project title]`.
5. Claude Code reviews the repo structure and asks project-formation questions.
6. The user answers the questions and uploads source files to `/context/source-drop`.
7. Claude Code ingests and extracts source context.
8. Claude Code proposes the project-specific agent team and workflow.
9. The user approves decision gates.
10. Claude Code begins project execution through branch-and-PR workflows.

## Core Folders

| Folder | Purpose |
|---|---|
| `/docs` | Project brief, scope, decisions, assumptions, workflows, next actions, generated planning artifacts |
| `/context/source-drop` | Raw source files uploaded by the user |
| `/context/extracted` | Claude-generated Markdown summaries and structured source extractions |
| `/data` | Structured data, mock data, schemas, CSV, JSON, or seed files |
| `/.claude/agents` | Project-level Claude Code subagents |
| `/.claude/skills` | Reusable Claude Code skills and slash-command workflows |

## Operating Principles

- GitHub is the source of truth.
- Claude Code sessions are disposable workers.
- Pull requests are review checkpoints.
- `main` should represent the latest approved project state.
- Raw source files are not automatically authoritative.
- Source authority must be classified before use.
- Human approval gates should be used for important product, strategy, architecture, pricing, legal, acquisition, security, or implementation decisions.
- Claude Code should distinguish source-supported facts, assumptions, inferences, and recommendations.
- Project-specific agents should be designed after the project objective, constraints, source context, and decision boundaries are understood.

## Agent Team Persistence

This template distinguishes between recommended agents, simulated perspectives, and persistent agents.

A persistent project-specific agent exists only when a Markdown definition file has been created under `/.claude/agents/`.

During initialization, Claude may recommend an agent team. That recommendation does not mean the agents exist yet.

When the user approves persistent agent creation, Claude should create one `.md` file per approved agent and update `/docs/agent_team_design.md` with the agent status and file path.

## Starting Command

After creating a new repo from this template and connecting it to Claude Code, start with:

```text
/initialize-project [project title]
