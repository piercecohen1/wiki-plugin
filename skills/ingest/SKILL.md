---
name: ingest
description: "Ingest sources into the Obsidian wiki vault. Reads a source, extracts entities and concepts, creates or updates wiki pages, cross-references, and logs the operation. Supports URLs, YouTube, images, pasted text, files, and batch mode. Triggers on: ingest, process this source, add this to the wiki, read and file this, batch ingest, ingest all of these, ingest this url."
---

# wiki-ingest: Source Ingestion

Read the source. Write the wiki. Cross-reference everything.

**Vault path:** `/Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain`

All paths below are relative to this vault root. There is NO `wiki/` subdirectory — `index.md`, `hot.md`, `log.md`, etc. live at the vault root.

**Before creating or modifying any wiki page**, read the vault's own `CLAUDE.md` (at the vault root). It defines all frontmatter schemas, page templates, naming conventions, and the canonical workflows. Do not duplicate that schema here — defer to it.

---

## Step 0: Load Context

Every ingest starts here. No exceptions.

1. Read `hot.md` (~500 words of recent context).
2. Read `index.md` (full page catalog). You need this to avoid creating duplicate pages.
3. Read `raw/.manifest.json` if it exists (delta tracking).

Do not read additional pages unless needed. This is your context budget for setup.

---

## Delta Tracking

Before ingesting any source, check `raw/.manifest.json` to avoid re-processing unchanged content.

**Manifest location:** `raw/.manifest.json` (inside the vault, not `.raw/`)

**Manifest format** (create if missing):
```json
{
  "sources": {
    "raw/2026-04-14-article-title.md": {
      "hash": "abc123",
      "ingested_at": "2026-04-14",
      "pages_created": ["sources/article-title.md", "entities/person-name.md"],
      "pages_updated": ["topics/some-topic.md"]
    }
  }
}
```

**Before ingesting a file:**
1. Compute hash: `md5 -q [file]` (macOS).
2. Check if the file path exists in the manifest with the same hash.
3. If hash matches → skip. Report: "Already ingested (unchanged). Use `force` to re-ingest."
4. If missing or hash differs → proceed.

**After ingesting a file:**
1. Record `{hash, ingested_at, pages_created, pages_updated}` in the manifest.
2. Write the updated manifest back.

Skip delta checking if the user says "force ingest" or "re-ingest."

---

## Source Types

### URL Ingestion

Trigger: user passes a URL (starts with `http://` or `https://`).

1. **YouTube URLs** (`youtube.com`, `youtu.be`): invoke the `yt-transcript` skill via the Skill tool.
2. **All other URLs**: run `firecrawl scrape <url>` via the Bash tool.
3. **Clean** the output: strip ads, navigation, community footer, attribution boilerplate. Keep only article content.
4. **Derive slug** from URL path or page title — kebab-case, no query strings.
5. **Save** to `raw/{YYYY-MM-DD}-{slug}.md` with frontmatter:
   ```yaml
   ---
   url: <original URL>
   scraped: YYYY-MM-DD
   ---
   ```
6. Proceed to **Single Source Ingest** (step 2 onward — file is now in `raw/`).

### Image / Vision Ingestion

Trigger: user passes an image file (`.png`, `.jpg`, `.jpeg`, `.gif`, `.webp`, `.svg`, `.avif`).

1. **Read** the image using the Read tool. Claude processes images natively via vision.
2. **Extract** all visible text (OCR), identify key concepts, entities, diagrams, data.
3. **Save** the description to `raw/{YYYY-MM-DD}-{slug}.md`:
   ```yaml
   ---
   source_type: image
   original_file: <path>
   scraped: YYYY-MM-DD
   ---
   ```
   Followed by the full description and transcribed text.
4. Copy the original image to `assets/` if not already in the vault.
5. Proceed to **Single Source Ingest**.

### Pasted Text Ingestion

Trigger: user pastes text directly (not a URL, not a file path).

1. If the source isn't self-evident, ask for a brief label. Skip if user said "just ingest it."
2. Save verbatim to `raw/{YYYY-MM-DD}-{slug}.md`:
   ```yaml
   ---
   origin: pasted
   label: <user-provided or inferred label>
   date: YYYY-MM-DD
   ---
   ```
3. Proceed to **Single Source Ingest**.

### File Ingestion

Trigger: user points to a file path.

1. Read the file.
2. If not already in `raw/`, copy it there with date-prefixed kebab-case name.
3. Proceed to **Single Source Ingest**.

---

## Single Source Ingest

This is the common core. All source types converge here after saving to `raw/`.

1. **Read** the source completely. Do not skim.
2. **Discuss** with the user: surface what's new, interesting, and how it connects to existing wiki content. Keep it to 5-10 bullet points. Flag ambiguities or claims that conflict with existing pages. Skip this step if the user says "just ingest it" or "batch ingest."
3. **Create** the source filing receipt in `sources/{slug}.md`. Use the source template from the vault's `CLAUDE.md`.
4. **Create or update** entity pages in `entities/` for every person, org, product, tool, and place mentioned. One page per entity. Add `[[cross-references]]` in both directions.
5. **Create or update** topic pages in `topics/` for significant concepts, ideas, and frameworks. Add cross-references both directions.
6. **Update** `index.md` — add entries for all new pages.
7. **Append** to `log.md` (newest on top). Use the log format from the vault's `CLAUDE.md`:
   ```
   ## [YYYY-MM-DD HH:MM] ingest | {Source Title}
   - Type: url | pasted-text | file | image
   - Origin: {URL or filename}
   - Raw: raw/{filename}
   - Source page: sources/{filename}
   - Updated: {page-a, page-b}
   - New: {page-c, page-d}
   - Notes: {anything worth flagging}
   ```
8. **Update** `hot.md` — overwrite with fresh context reflecting this ingest. ~500 words max.
9. **Update** `overview.md` if the vault's big picture has meaningfully changed.
10. **Update** `raw/.manifest.json` with hash and page tracking.
11. **Check for contradictions** (see below).

---

## Contradiction Handling

When new information conflicts with existing wiki pages:

**On the existing page**, add:
```markdown
> [!contradiction] Conflict with [[new-source-receipt]]
> This page claims X. [[new-source-receipt]] says Y.
> Needs resolution — check dates, context, and primary sources.
```

**On the new source receipt**, add:
```markdown
> [!contradiction] Contradicts [[existing-page]]
> This source says Y, but [[existing-page]] says X.
```

Do not silently overwrite old claims. Flag and let the user decide.

---

## Batch Ingest

Trigger: user drops multiple files/URLs or says "ingest all of these."

1. **List** all sources to process. Confirm with the user before starting.
2. **Process** each source through the single ingest flow. For batch mode, skip the "discuss with user" step (step 2 of single ingest) — just ingest.
3. **Defer** cross-referencing between batch sources until all are processed, then do a cross-reference pass.
4. **Update** `index.md`, `hot.md`, and `log.md` once at the end (not per-source).
5. **Report**: "Processed N sources. Created X pages, updated Y pages. Key connections found: ..."

For large batches (10+ sources), check in with the user every 10 sources. For 30+, expect significant processing time.

---

## Context Window Discipline

Token budget matters. Follow these rules:

- Read `hot.md` first. If it has what you need, stop.
- Read `index.md` to find existing pages before creating new ones.
- Read only 3-5 existing pages per ingest. If you're reading 10+, you're reading too broadly.
- Use the Grep tool to find specific content without reading full pages.
- Keep wiki pages short: 100-300 lines max. If a page grows past 300 lines, split it.
- Use the Edit tool for surgical updates. Don't re-read an entire file to change one section.

---

## What Not to Do

- Do not modify anything in `raw/`. These are immutable source documents.
- Do not create duplicate pages. Always check `index.md` and use Grep before creating.
- Do not skip the log entry. Every ingest must be recorded.
- Do not skip the hot cache update. It keeps future sessions fast.
- Do not use `WebFetch` or `WebSearch` tools. Use `firecrawl scrape` (Bash) and `brave` (Bash) instead.
- Do not fabricate content from training data. The wiki contains what was ingested, nothing more.
- Do not strip or modify the vault's `CLAUDE.md`. That's the schema, not content.
