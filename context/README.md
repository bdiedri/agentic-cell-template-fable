# Context

This folder contains source materials and extracted project context.

## Folders

| Folder | Purpose |
|---|---|
| `/context/source-drop` | Raw source files uploaded by the user |
| `/context/extracted` | Claude-generated Markdown summaries and structured extractions |

## Rules

- Raw files in `/context/source-drop` are inputs, not automatically authoritative.
- Claude should classify each source before relying on it.
- Extracted Markdown should preserve traceability to the original source file.
- Graphic-heavy files, spreadsheets, PDFs, and decks should be summarized carefully and flagged for human review when extraction quality is low.
- Source conflicts should be surfaced in `/docs/open_questions.md` or `/docs/decision_log.md`.
