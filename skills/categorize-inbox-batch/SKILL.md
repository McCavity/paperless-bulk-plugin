---
name: categorize-inbox-batch
description: Use when the user wants to process a batch of inbox documents — assigning correspondent, document type, tags, and removing the inbox tag. Use after check-inbox surfaced a non-empty inbox, or when the user says "lass uns den Eingang durchgehen", "triage", "Eingang aufräumen".
---

# Categorize Inbox Batch

End-to-end triage workflow for the Paperless inbox: per document, set correspondent + document type + tags, then remove the Eingang tag.

## When to Use

Trigger when the user wants to process inbox documents — common phrasings: "Eingang durchgehen", "Eingang aufräumen", "Dokumente kategorisieren", "triage". Typically follows a `check-inbox` summary.

## How to Use

For each document the user wants to process:

1. **Identify the correspondent** — if obvious from the title, use `find_correspondent_by_name` with `name__icontains` to find the existing ID. If no match, ask the user before creating a new correspondent (anti-duplicate discipline — see Open Loops).
2. **Identify the document type** — same pattern with `find_document_type_by_name`.
3. **Tags** — ask the user which tags apply (or suggest based on prior similar documents in the same correspondent).
4. **Apply** — preferred sequence on a small batch (1-5 docs):
   - `bulk_set_correspondent` with `[doc_id]` + correspondent_id
   - `bulk_set_document_type` with `[doc_id]` + document_type_id
   - `bulk_add_tag` for each tag (or `bulk_modify_tags` if multiple tags + removals at once)
   - **Last**: `bulk_remove_tag` with the Eingang-tag ID to release from inbox
5. After each document, confirm what was applied. Don't batch all documents into one giant operation without per-doc confirmation — gives the user a place to redirect.

## Anti-Patterns

- **Bulk-Sync vermeiden — kleine Schritte** (see KI-OS feedback memory). Don't process 20+ documents in one operation without intermediate cleanup-stops.
- **Vor jedem Neu-Anlegen suchen.** Before creating a new correspondent or tag, use the `find_*_by_name` tool with `name__icontains` to check if one already exists. Server-side filter, not client-side grep (this caught the "Stadtwerke" duplicate, 2026-05-22).
- **PUT, not PATCH** — but this MCP wraps `bulk_edit` which handles the method-choice automatically. Mentioned only as a CLAUDE.md cross-reference for context.

## Failure Modes

- `bulk_set_correspondent` with an invalid correspondent_id returns a 400; defensive: validate ID exists via `find_correspondent_by_name` first.
- `find_*_by_name` with no match returns an empty list — handle this branch explicitly (don't pass `None` as ID to a bulk_set call).
