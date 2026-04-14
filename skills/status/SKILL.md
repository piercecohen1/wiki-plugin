---
name: status
description: >
  Check the wiki vault's status, recent activity, and active threads. Entry point
  for the wiki plugin. Use when: "wiki status", "what's in the wiki", "check the vault",
  "wiki overview", or when you need to orient before wiki operations.
---

# Wiki Status

**Vault:** `/Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain`

## Steps

1. Read `hot.md` from the vault for recent context.
2. Read `index.md` to get the full page inventory.
3. Read the last 30 lines of `log.md` for recent activity.
4. Report:
   - **Page counts** by type (topics, entities, sources, threads)
   - **Recent activity** (last 3 log entries summarized)
   - **Active threads** (from hot.md)
   - **Health hints** (e.g. "no ingests in 2 weeks", "index has orphan entries")

## Routing

Based on what the user says next, suggest the appropriate skill:

| User intent | Skill |
|---|---|
| "ingest this", URL, file reference | `/wiki:ingest` |
| "what do you know about X", question | `/wiki:query` |
| "save this", "file this" | `/wiki:save` |
| "research X", "deep dive into X" | `/wiki:research` |
| "lint", "health check", "clean up" | `/wiki:lint` |
| "canvas", "visual board" | `/wiki:canvas` |
