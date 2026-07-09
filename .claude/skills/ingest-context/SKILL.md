# Ingest Context

Use this skill to index, classify, extract, and summarize source files from `/context/source-drop`.

## Purpose

Transform raw source materials into structured, agent-readable context while preserving traceability, authority classification, and extraction-quality notes.

## Invocation

Use this skill when the user invokes `/ingest-context` or asks to organize, classify, extract, or summarize source files.

## Core Behavior

Sequence within this skill is at your discretion — the lists below define what must be true, not choreography.

Required inputs:

- `/context/source-drop`
- `/docs/source_authority_model.md`
- `/context/source_artifact_index.md`

Required outputs:

- See "Expected Outputs"

Invariants:

- Every source file is indexed and classified by type, topic/category, authority level, and extraction quality.
- Markdown summaries are created under `/context/extracted`, with traceability preserved to the original source files.
- Conflicts, gaps, low-confidence extractions, and human-review needs are surfaced.
- Decision, assumption, and open-question logs are updated as needed.
- A PR is opened back into `main`.

## Source Classification Fields

For each source, capture:

- Source ID
- File name
- Source path
- Source type
- Topic/category
- Authority level
- Extraction quality
- Relevance to the project
- Extracted summary path
- Human review needed
- Key takeaways
- Claims or constraints to preserve
- Conflicts or gaps
- Recommended future use

## Authority Levels

- Authoritative
- Supporting
- Reference-only
- Historical
- Speculative
- Unknown

## Extraction Quality Values

- High
- Medium
- Low
- Not extracted

## Extraction Guidance

- Prefer Markdown for extracted summaries.
- Preserve source names and paths.
- Do not overwrite raw source files.
- For PDFs, extract structure, sections, claims, tables, and constraints where feasible.
- For PowerPoints, extract slide titles, visible text, key messages, and likely visual intent.
- For spreadsheets, extract workbook structure, sheet names, column meanings, key fields, and implications.
- For graphic-heavy or hard-to-parse files, summarize what can be extracted and flag for human review.
- Do not invent visual details or unsupported claims.

## Expected Outputs

Create or update:

- `/context/source_artifact_index.md`
- `/context/extracted/`
- `/docs/assumptions_log.md`
- `/docs/open_questions.md`
- `/docs/decision_log.md`
- `/docs/next_action_plan.md`

## Guardrails

- Do not generate final project deliverables.
- Do not build app code.
- Do not run web research unless explicitly authorized.
- Do not treat raw source files as authoritative.
- Distinguish source-supported facts, assumptions, inferences, and recommendations.
- If extraction is incomplete, flag it instead of guessing.

## Completion Criteria

This skill is complete when:

1. Source files are indexed.
2. Authority levels are assigned.
3. Extraction quality is recorded.
4. Markdown summaries are created where feasible.
5. Conflicts, assumptions, and open questions are surfaced.
6. A PR is opened back into `main` and its number is recorded, or the reason it could not be opened is recorded in `/docs/next_action_plan.md`.
