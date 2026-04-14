# wiki-plugin

Claude Code plugin for managing an Obsidian wiki vault. Forked from [AgriciDaniel/claude-obsidian](https://github.com/AgriciDaniel/claude-obsidian) and rebuilt for personal use.

Based on [Karpathy's LLM Wiki pattern](https://github.com/karpathy/LLM-wiki): drop in sources, Claude builds and maintains structured, cross-referenced knowledge pages.

## Skills

- `/wiki:status` — vault status and routing
- `/wiki:ingest` — ingest sources (URLs, files, pasted text, images)
- `/wiki:query` — query the wiki (quick / standard / deep)
- `/wiki:save` — save conversation insights as wiki pages
- `/wiki:research` — multi-round autonomous research
- `/wiki:lint` — health check
- `/wiki:canvas` — Obsidian canvas operations

## Usage

Test locally:

```bash
claude --plugin-dir ~/repos/wiki-plugin
```

Install:

```bash
claude plugin install wiki --plugin-dir ~/repos/wiki-plugin
```

## License

MIT
