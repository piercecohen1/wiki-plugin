# Wiki Plugin

LLM Wiki plugin for Obsidian. Manages a persistent, compounding knowledge vault based on Karpathy's LLM Wiki pattern.

**Plugin name:** `wiki`
**Vault path:** `/Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain`

## Skills

| Slash command | Purpose |
|---|---|
| `/wiki:status` | Vault status, recent activity, routing |
| `/wiki:ingest` | Ingest a source (URL, file, pasted text, image) |
| `/wiki:query` | Query the wiki (quick / standard / deep modes) |
| `/wiki:save` | Save conversation insights as wiki pages |
| `/wiki:research` | Multi-round autonomous research loop |
| `/wiki:lint` | Health check — orphans, dead links, gaps |
| `/wiki:canvas` | Create and edit Obsidian canvas files |

## Vault Structure

The vault lives at the path above. All content is at the vault root (no `wiki/` subdirectory).

```
second-brain/
├── CLAUDE.md          # Schema, workflows, templates (source of truth for conventions)
├── index.md           # Page catalog with 1-line summaries
├── log.md             # Chronological activity log (newest on top)
├── overview.md        # Focus areas and vault purpose
├── hot.md             # Hot cache — recent context (~500 words)
├── raw/               # Immutable raw sources
│   └── .manifest.json # Delta tracking (file hashes → skip re-ingestion)
├── sources/           # Filing receipts (one per ingested source)
├── topics/            # Core knowledge — concepts, ideas, frameworks
├── entities/          # People, organizations, products, tools
├── threads/           # Open questions, evolving synthesis
├── canvases/          # Obsidian .canvas visual boards
├── meta/              # Lint reports, dashboards
├── assets/            # Images and attachments
└── templates/         # Obsidian templates
```

## Conventions

All page conventions, frontmatter schemas, and templates are defined in the vault's own `CLAUDE.md`. Read it before creating or modifying wiki pages.

## Hot Cache

`hot.md` is a ~500-word summary of recent context. It exists so any session can get vault context cheaply.

- **Read:** at session start (via hook), after compaction (via hook), and as the first step of every wiki skill
- **Update:** after every ingest, save, research, lint, or significant query
- **Format:** overwrite completely each time (it's a cache, not a journal)

## Cross-Project Access

To reference this wiki from another project's CLAUDE.md:

```markdown
## Wiki Knowledge Base
Path: /Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain

When you need context not already in this project:
1. Read hot.md first (recent context, ~500 words)
2. If not enough, read index.md (full catalog)
3. Only then read individual wiki pages

Do NOT read the wiki for general coding questions or things already in this project.
```

## Tools

This plugin is configured for Pierce's local toolchain:
- **URL scraping:** `firecrawl scrape <url>` (Bash tool)
- **YouTube:** invoke the `yt-transcript` skill (Skill tool)
- **AI research:** `pplx "<query>"` (Bash tool)
- **Web search:** `brave "<query>"` (Bash tool)
