# paperless-bulk-plugin

Claude-Code- und Codex-Plugin, das den [paperless-bulk-mcp](https://github.com/McCavity/paperless-bulk-mcp)-Server um drei Skills ergänzt, die *erklären wann* die Bulk-Operations greifen sollen.

Das Plugin ist die strukturierte Antwort auf die Beobachtung aus Unit 3 des [Hugging Face Context Course](https://huggingface.co/learn/context-course): *Skills beschreiben wie und wann, MCP-Server liefern was — Plugins bringen beide Welten so zusammen, dass Discovery automatisch greift, ohne dass der Nutzer den MCP explizit aufrufen muss.*

## Repo-Layout

Reines **Plugin-Repo** — distribuiert über den gemeinsamen [mccavity-Marketplace-Hub](https://github.com/McCavity/claude-marketplace), der dieses Plugin via `git-subdir` aus `plugins/paperless-bulk-plugin/` zieht:

```
paperless-bulk-plugin/
└── plugins/
    └── paperless-bulk-plugin/             # Plugin-Verzeichnis
        ├── .claude-plugin/plugin.json     # Claude-Code-Manifest
        ├── .codex-plugin/plugin.json      # Codex-Manifest
        ├── .mcp.json                      # MCP-Server-Referenz
        └── skills/...
```

## Bundled Skills

| Skill | Trigger | MCP-Tools |
|---|---|---|
| `check-inbox` | „was wartet im Eingang?", Session-Start mit non-zero Paperless count | `list_inbox` |
| `categorize-inbox-batch` | „Eingang durchgehen", Triage-Workflow | `find_*_by_name`, `bulk_set_correspondent`, `bulk_set_document_type`, `bulk_add_tag`, `bulk_modify_tags`, `bulk_remove_tag` |
| `bulk-retag` | Tag-Cleanup, Tag-Migration über viele Docs | `find_tag_by_name`, `bulk_add_tag`, `bulk_remove_tag`, `bulk_modify_tags`, `list_documents` |

## MCP-Voraussetzung

Das Plugin enthält **keinen Server-Code** und registriert standardmäßig **keinen** MCP-Server — es ist **skills-only** und nutzt den `paperless-bulk`-Server, den du **bereits registriert** hast (z.B. user-scope in `~/.claude.json` / `~/.mcp.json`). So scheitert die Installation nie an fehlenden Env-Vars oder doppelter Registrierung.

### Setup (nur falls noch kein `paperless-bulk`-Server registriert ist)

Server installieren und das Template aus [`.mcp.json`](plugins/paperless-bulk-plugin/.mcp.json) in einen Top-Level-`mcpServers`-Block kopieren (Plugin-`.mcp.json` ODER user-scope), dann die Env-Vars setzen:

```bash
pip install git+https://github.com/McCavity/paperless-bulk-mcp.git
export PAPERLESS_BASE_URL="https://your-paperless.example/api"
export PAPERLESS_TOKEN="..."
export PAPERLESS_INBOX_TAG_ID="123"  # optional, sonst Name-Lookup
```

## Installation in Claude Code

Über den gemeinsamen `mccavity`-Marketplace-Hub ([claude-marketplace](https://github.com/McCavity/claude-marketplace)):

```text
/plugin marketplace add McCavity/claude-marketplace
/plugin install paperless-bulk-plugin@mccavity
```

> Dieses Repo hostet **keinen** eigenen Marketplace mehr — Marketplace-Namen sind pro Nutzer eindeutig, ein self-hosted `mccavity` würde den Hub verdrängen. Füge nur den Hub hinzu, nicht dieses Repo direkt.

## Installation in Codex

```bash
mkdir -p ~/.codex/plugins
cp -R /absoluter/pfad/paperless-bulk-plugin/plugins/paperless-bulk-plugin ~/.codex/plugins/paperless-bulk-plugin
```

Dann `~/.agents/plugins/marketplace.json` um diesen Eintrag erweitern und Codex neu starten.

## Skill-Discovery — wie sie wirkt

Mit diesem Plugin installiert wird folgendes möglich, **ohne dass der Nutzer den MCP explizit erwähnt**:

- „Wie sieht's im Paperless-Eingang aus?" → `check-inbox` triggert, ruft `list_inbox` auf, präsentiert Triage-Summary.
- „Lass uns die drei Dokumente kategorisieren." → `categorize-inbox-batch` übernimmt den Workflow.
- „Setz mir den Lizenz-Tag bei allen Bartender-Dokumenten." → `bulk-retag` resolviert Tag-IDs und macht den Bulk-Op.

Ohne das Plugin: der Nutzer muss daran denken, dass es eine `paperless-bulk`-MCP gibt, und sie explizit aufrufen.

## Wartung

- Versionierung folgt [Semantic Versioning](https://semver.org/lang/de/).
- Skills sind atomar — neue Workflows kommen als neue Skill-Verzeichnisse rein, nicht als Erweiterung existierender Skills.
- Bei MCP-Tool-Updates (neue Tools, geänderte Signaturen): pro betroffenem Skill die SKILL.md aktualisieren und Version bumpen.

## Lizenz

MIT — siehe [LICENSE](LICENSE).
