---
name: bulk-retag
description: Use when the user wants to add, remove, or rename a tag across many documents — "Tag X bei allen Y-Dokumenten setzen", "Tag rebenennen", "alle Dokumente von Korrespondent Z einen Tag verleihen". Wraps the bulk_add_tag / bulk_remove_tag / bulk_modify_tags MCP tools.
---

# Bulk Tag Operations

Add, remove, or migrate tags across a set of Paperless-ngx documents in one operation.

## When to Use

Trigger when the user wants tag changes across many documents:

- "Tag #94 (Lizenz) bei diesen Dokumenten setzen"
- "Tag X umbenennen" → in Paperless = "add new tag, remove old tag" (Tag-Namen sind immutable in der API; ein Rename ist ein Migrate)
- "alle Dokumente von Allianz mit Tag Versicherung versehen"
- Tag-Konsolidierung / Cleanup-Workflows

## How to Use

### Tag hinzufügen

1. Build the document set: typically via `list_documents` with filter parameters (correspondent, document_type, tags, query).
2. Resolve tag name → ID via `find_tag_by_name` (icontains-filter).
3. Apply `bulk_add_tag` with `[doc_ids]` + tag_id.
4. Report count + first 3 examples.

### Tag entfernen

Same as add, with `bulk_remove_tag`.

### Tag migrieren (Rename / Merge)

Wenn ein Tag in einen anderen migriert werden soll:

1. Resolve both old and new tag IDs.
2. Use `bulk_modify_tags` mit `add_tags=[new_id]` + `remove_tags=[old_id]` in einem Aufruf (atomar).
3. Verify mit `list_documents?tags__id=<old_id>` dass keine Doks mehr den alten Tag haben.
4. Wenn alles weg ist: **alten Tag in der Paperless-UI löschen** (der MCP hat keine Tag-Delete-Operation — bewusst weggelassen, das ist eine vollständige API-Operation außerhalb der Bulk-API-Domäne).

## Anti-Patterns

- **Nie Tag-IDs raten.** Immer via `find_tag_by_name` lookup. IDs ändern sich nicht, aber Tags können umbenannt werden — der Lookup ist robust gegen Tag-Drift.
- **Vor Anlegen eines neuen Tags suchen** (siehe `categorize-inbox-batch` Anti-Pattern). Tag-Duplikate sind chronisch lästig.
- **Bulk-Größen managen** — bei >100 Documents auf einmal kann die Paperless-Side hängen. Lieber in 50er-Batches, mit visualer Bestätigung dazwischen.

## Verknüpfung mit dem CLAUDE.md-Lernprotokoll

- Tag-Cleanup-Erfahrung 2026-04 (Paperless Tag-System): 87→46 Tags, Taxonomie definiert. Beim aktiven Bulk-Tag-Cleanup an die Taxonomie halten — siehe Vault `08-resources` für die aktuelle Taxonomie-Doku (falls dort dokumentiert).
- PUT-statt-PATCH-Lehre (2026-05-25) wird vom MCP-Wrapper automatisch korrekt gehandhabt — Skill braucht das nicht zu erwähnen.
