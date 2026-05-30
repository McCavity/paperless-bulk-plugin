# CLAUDE.md — paperless-bulk-plugin

> Letzte Aktualisierung: 2026-05-30

## Was ist das?

Claude-Code- und Codex-Plugin, das den `paperless-bulk-mcp`-Server um drei Workflow-Skills ergänzt: `check-inbox`, `categorize-inbox-batch`, `bulk-retag`.

Das Plugin ist das Artefakt für Unit 3 (Plugins) des Hugging Face Context Course — siehe [04-projects/context-course/unit3-plugins-notes.md im KI-OS-Vault](../../ki-os/04-projects/context-course/unit3-plugins-notes.md) für die Lern-Rückblicke.

## Repo-Strukur

```
paperless-bulk-plugin/
├── .claude-plugin/plugin.json     # CC-Manifest (nur Identitäts-Metadaten)
├── .codex-plugin/plugin.json      # Codex-Manifest (mit interface.displayName)
├── .mcp.json                      # gemeinsame MCP-Server-Referenz (Env-Vars)
├── skills/
│   ├── check-inbox/SKILL.md
│   ├── categorize-inbox-batch/SKILL.md
│   └── bulk-retag/SKILL.md
├── example/
│   └── marketplace.json           # lokale Test-Installation
├── README.md
├── LICENSE                        # MIT
└── CLAUDE.md (diese Datei)
```

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
