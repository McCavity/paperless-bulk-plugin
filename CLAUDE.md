# CLAUDE.md — paperless-bulk-plugin

> Letzte Aktualisierung: 2026-05-30

## Was ist das?

Claude-Code- und Codex-Plugin, das den `paperless-bulk-mcp`-Server um drei Workflow-Skills ergänzt: `check-inbox`, `categorize-inbox-batch`, `bulk-retag`.

Das Plugin ist das Artefakt für Unit 3 (Plugins) des Hugging Face Context Course — siehe [04-projects/context-course/unit3-plugins-notes.md im KI-OS-Vault](../../ki-os/04-projects/context-course/unit3-plugins-notes.md) für die Lern-Rückblicke.

## Repo-Struktur (reines Plugin-Repo)

Dieses Repo enthält **nur das Plugin** in `plugins/<name>/` — es hostet **keinen** eigenen Marketplace mehr (seit 2026-06-06). Distribution läuft über den gemeinsamen `mccavity`-Hub [claude-marketplace](https://github.com/McCavity/claude-marketplace), der dieses Plugin via `git-subdir` aus `plugins/paperless-bulk-plugin/` zieht.

```
paperless-bulk-plugin/
├── plugins/
│   └── paperless-bulk-plugin/             # Plugin-Verzeichnis (gecached bei Install)
│       ├── .claude-plugin/plugin.json     # CC-Manifest
│       ├── .codex-plugin/plugin.json      # Codex-Manifest
│       ├── .mcp.json                      # MCP-Backend-Referenz (skills-only; Server als Template)
│       └── skills/{check-inbox,categorize-inbox-batch,bulk-retag}/SKILL.md
├── README.md
├── LICENSE                                # MIT
└── CLAUDE.md (diese Datei)
```

**Marketplace-Lektionen (2026-06-06):**
- Ein Marketplace-`name` ist **pro Nutzer eindeutig** — ein zweites `add` mit gleichem Namen ersetzt das erste. Früher hatten paperless- UND iobroker-plugin je eine `marketplace.json` namens `mccavity` → sie verdrängten sich gegenseitig. Fix: ein Hub-Repo, beide Plugins via `git-subdir` (`{ "source": "git-subdir", "url": "McCavity/<repo>", "path": "plugins/<name>" }`).
- `source: ".."` und flache `example/`-Layouts sind in CC 2.1.x invalid (Fehler: „source type your Claude Code version does not support").
- Plugin-`.mcp.json` **skills-only** halten, wenn der Server schon user-scope registriert ist — sonst „Missing environment variables" / Doppelregistrierung beim Plugin-Load.

## Solo-Maintainer-Konventionen

- Branch Protection auf `main`: `required_approving_review_count: 0`, `enforce_admins: false`, no force-push, no deletion.
- Commit-Messages auf Deutsch.
- Semantic Versioning in beiden `plugin.json`-Files synchron halten.
- Bei Skill-Edits **Version bumpen** + Datum im SKILL.md-Frontmatter ergänzen (optional, aber empfohlen für Wartbarkeit).

## Trade-Offs / bewusste Auslassungen

- **Kein OpenCode-Branch.** OpenCode-Plugins sind code-first (JS/TS-Module), das passt nicht zum manifest-first Pattern hier. Wenn jemand mit OpenCode arbeiten will, gehören Skills nach `.opencode/skills/` und die MCP-Konfig nach `opencode.json` — siehe Lesson "Using Plugins" im HF Context Course Unit 3.
- **Keine Hooks.** Hooks sind Unit-5-Material. Für die Skills hier wären sie nicht klar wertstiftend.
- **Kein Sub-Agent.** Sub-Agents sind Unit-4-Material; sie könnten beim Massen-Triage in Frage kommen ("Pro Document einen Sub-Agent"), aber das ist Spezialfall.

## Verwandte Repos

- [paperless-bulk-mcp](https://github.com/McCavity/paperless-bulk-mcp) — der MCP-Server, der die Bulk-Tools liefert. Dieses Plugin ist ohne den Server nicht funktional.
- [iobroker-mcp](https://github.com/McCavity/iobroker-mcp) — geplantes Geschwister-Plugin (`iobroker-plugin`) als Open Loop im KI-OS (siehe `03-strategy/open-loops.md`).
