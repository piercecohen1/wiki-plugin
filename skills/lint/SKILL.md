---
name: lint
description: >
  Health check the Obsidian wiki vault. Finds orphan pages, dead wikilinks, stale claims,
  missing pages, missing cross-references, frontmatter gaps, empty sections, and index drift.
  Generates a lint report and optionally auto-fixes safe issues.
---

# wiki-lint: Wiki Health Check

Run lint after every 10-15 ingests, or weekly. Output a lint report to `meta/lint-report-YYYY-MM-DD.md`.

**Vault path:** `/Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain`

All paths below are relative to the vault root. Read the vault's `CLAUDE.md` for page templates and frontmatter schemas — do not duplicate them here.

---

## Lint Checks

Work through these in order:

### 1. Orphan Pages

Wiki pages with no inbound `[[wikilinks]]`. They exist but nothing points to them.

- Scan all `.md` files for wikilink targets.
- Compare against all page filenames.
- Exclude `index.md`, `log.md`, `overview.md`, `hot.md`, and anything in `templates/`, `raw/`, `meta/`.
- Report each orphan with a suggestion: link from a related page, or flag for deletion.

### 2. Dead Links

Wikilinks that reference a page that does not exist.

- Extract all `[[target]]` references from every `.md` file.
- Resolve each target to a filename (kebab-case, `.md` extension).
- Report each dead link with the source page and the missing target.

### 3. Stale Claims

Assertions on older pages that newer sources have contradicted or updated.

- For each page in `sources/`, check the `date` frontmatter.
- Cross-reference claims in `topics/` and `entities/` against newer sources.
- Flag where a newer source contradicts or supersedes an older page's claim.
- Use `> [!contradiction]` callout format for flagged items.

### 4. Missing Pages

Concepts or entities mentioned across multiple pages but lacking their own page.

- Scan all pages for recurring proper nouns, technical terms, or named concepts that appear 3+ times across different pages.
- Exclude terms that already have a page.
- Suggest which directory the page belongs in (`topics/`, `entities/`, `threads/`).

### 5. Missing Cross-References

Entities or concepts mentioned in a page but not linked with `[[wikilinks]]`.

- For each page, check if it mentions the title (H1 or filename) of another existing page without a wikilink.
- Report each unlinked mention with the source page and target page.

### 6. Frontmatter Gaps

Pages missing required frontmatter fields.

- Required fields: `type`, `date` or `created` (at least one).
- Check all `.md` files except those in `templates/`, `raw/`, and root-level system files (`index.md`, `log.md`, `overview.md`, `hot.md`).
- Report each page with its missing fields.

### 7. Empty Sections

Headings with no content underneath (no text before the next heading or end of file).

- Scan all wiki pages for `## ` or `### ` headings followed immediately by another heading or EOF.
- Exclude template files.

### 8. Index Drift

Mismatch between `index.md` and actual pages on disk.

- Pages on disk not listed in `index.md`.
- Entries in `index.md` pointing to pages that don't exist (renamed or deleted).
- Report both directions of drift.

---

## Lint Report Format

Create at `meta/lint-report-YYYY-MM-DD.md`:

```markdown
---
type: meta
title: "Lint Report YYYY-MM-DD"
created: YYYY-MM-DD
---

# Lint Report: YYYY-MM-DD

## Summary
- Pages scanned: N
- Issues found: N
- Auto-fixed: N
- Needs review: N

## Orphan Pages
- [[page-name]]: no inbound links. Suggest: link from [[related-page]] or delete.

## Dead Links
- [[missing-page]]: referenced in [[source-page]] but does not exist. Suggest: create stub or remove link.

## Stale Claims
- [[page-name]]: claim "X" may conflict with newer source [[newer-source]].

## Missing Pages
- "concept name": mentioned in [[page-a]], [[page-b]], [[page-c]]. Suggest: create in topics/.

## Missing Cross-References
- [[entity-name]] mentioned in [[page-a]] without a wikilink.

## Frontmatter Gaps
- [[page-name]]: missing fields: type, date

## Empty Sections
- [[page-name]]: empty heading "## Section Name"

## Index Drift
- On disk but not in index: [[page-name]]
- In index but not on disk: [[deleted-page]]
```

---

## Writing Style Check

During lint, flag pages that violate the wiki style guide:

- Not in declarative present tense ("X basically does Y" instead of "X does Y")
- Missing source citations where claims are made
- Uncertainty not flagged with `> [!gap]`
- Contradictions not flagged with `> [!contradiction]`

Include style violations in the lint report under a `## Style Issues` section.

---

## Naming Conventions

Enforce these during lint:

| Element | Convention | Example |
|---------|-----------|---------|
| Filenames | kebab-case, match H1 | `machine-learning.md` |
| Wikilinks | match filename | `[[machine-learning]]` |
| Tags | lowercase, hierarchical | `#domain/architecture` |

Filenames must be unique across the vault.

---

## Auto-Fix Rules

After generating the lint report, present it to the user. Ask: "Should I fix these automatically, or do you want to review each one?"

**Safe to auto-fix** (do these without asking per-item):
- Adding missing frontmatter fields with sensible defaults
- Creating stub pages for missing entities/concepts
- Adding `[[wikilinks]]` for unlinked mentions of existing pages

**Needs review before fixing** (always ask):
- Deleting orphan pages (they might be intentionally standalone)
- Resolving contradictions (requires human judgment)
- Merging duplicate pages

---

## After Lint

Once the lint report is written and any auto-fixes are applied:

1. **Update `index.md`** — add any new pages, remove entries for deleted pages.
2. **Update `log.md`** — add a log entry (newest on top) summarizing the lint run and fix count.
3. **Update `hot.md`** — refresh with current vault state.
4. **Git commit** — commit all changes with a message like `wiki: lint YYYY-MM-DD — N issues found, M fixed`.
