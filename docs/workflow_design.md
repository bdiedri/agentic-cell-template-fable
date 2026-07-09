# Workflow Design

This file documents the project-specific workflow.

## Current Workflow Stage
[Initialization / context ingestion / team design / planning / implementation / review / delivery]

## Standard Workflow

1. Initialize project
2. Ingest source context
3. Classify source authority
4. Surface decisions and assumptions
5. Design project-specific agent team
6. Create first-phase plan
7. Execute through branch-and-PR workflow
8. Review and merge approved work
9. Start next phase from latest main

## Human Approval Gates

| Gate | Description | Required Before |
|---|---|---|

## Branch and PR Workflow

- Start from latest `main`.
- Create a task-specific branch.
- Open PR back into `main`.
- Update the same PR for corrections when possible.
- Merge only when the PR is an accepted checkpoint.
- Delete stale branches after merge.
