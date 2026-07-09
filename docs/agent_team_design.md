# Agent Team Design

This file documents the recommended and/or persistent project-specific agent team.

## Agent Status Definitions

| Status | Meaning |
|---|---|
| Persistent | Agent has a durable Markdown definition file under `/.claude/agents/` |
| Recommended | Agent is recommended but has not been created as a file |
| Simulated Only | Role has been used as a temporary analysis perspective but is not a durable agent |
| Deferred | Agent may be useful later but should not be created now |
| Retired | Agent was previously used but is no longer active |

## Project-Specific Agent Team

| Agent | Status | Agent File Path | Purpose | Core Responsibilities | When to Use | Human Approval Needed? | Creation Approved By / Date |
|---|---|---|---|---|---|---|---|

## Recommended Agents Not Yet Created

| Agent | Why It May Be Needed | Trigger for Creation | Priority |
|---|---|---|---|

## Simulated Perspectives Used

| Perspective | Where Used | Why It Was Simulated | Should It Become Persistent? |
|---|---|---|---|

## Agent Design Principles

- Add agents based on project need, not org-chart mimicry.
- Prefer fewer agents with sharper responsibilities.
- Use specialist agents for domain-specific review.
- Every persistent agent must have a corresponding file under `/.claude/agents/`.
- Do not describe a role as a created agent unless its Markdown file exists.
- Temporary role-based reasoning should be marked as simulated-only.
- Every agent should have clear forbidden responsibilities.
- Agents should create traceable artifacts, not just opinions.
