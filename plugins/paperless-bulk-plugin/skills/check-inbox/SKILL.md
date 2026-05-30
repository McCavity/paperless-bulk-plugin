---
name: check-inbox
description: Use when the user asks about the Paperless inbox — "was wartet im Eingang?", "wie voll ist die Inbox?", "check inbox", "schau mal im Paperless-Eingang nach". Calls list_inbox from the paperless-bulk MCP server and presents a triage-ready summary.
---

# Check Paperless Inbox

Show what's waiting in the Paperless-ngx inbox and surface what needs triage.

## When to Use

Trigger this skill when the user asks about the state of the Paperless inbox — common phrasings: "was wartet im Eingang", "Paperless-Eingang", "check inbox", "wie viele Dokumente warten", "neue Dokumente?". Also use at session start when the dashboard hint about Paperless count is non-zero.

## How to Use

1. Call `list_inbox` from the `paperless-bulk` MCP server (no parameters needed; defaults to up to 20 results).
2. From the returned `count` and `results` array, build a triage summary:
   - **Empty inbox** → report `leer ✓` and stop.
   - **Non-empty** → group documents by what's missing:
     - missing correspondent
     - missing document type
     - missing both
     - has both (only the Eingang tag is keeping them in the inbox)
3. Present the summary in **at most 5 lines** for the user. Do NOT dump the full results list — only group totals and 2-3 example titles per group.

## Example Output

```
Paperless-Eingang: 7 Dokumente

- 3× ohne Korrespondent UND ohne Typ — neue Quellen, brauchen volle Triage
  (z.B. "Rechnung 2026-05-20", "Mietnebenkostenabrechnung")
- 2× nur ohne Typ — Korrespondent erkannt, Typ unklar
- 2× komplett — nur noch Eingang-Tag entfernen

Soll ich die ersten 3 jetzt durchgehen?
```

## Handoff

If the user wants to triage:
- For batch processing → invoke `categorize-inbox-batch` skill
- For tag-cleanup operations → invoke `bulk-retag` skill
- For per-document detail → fall back to PaperCortex search (full-text) or to direct Paperless UI

## Anti-Patterns

- Do NOT call `list_documents` with a query filter — `list_inbox` is the specialized endpoint that already filters by the Eingang-tag.
- Do NOT report a count of zero as "no data" — `count: 0` is a valid, frequent state ("leer ✓").
- Do NOT paste raw JSON back at the user.
