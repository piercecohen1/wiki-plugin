---
name: save
description: >
  Save conversation insights into the Obsidian wiki vault as structured pages.
  Analyzes the chat, determines note type, creates pages with proper frontmatter,
  and updates index, log, and hot cache. Triggers on: "save this", "save that answer",
  "file this", "save to wiki", "keep this", "save this analysis", "add this to the wiki".
---

# save: File Conversations Into the Wiki

Good answers and insights shouldn't disappear into chat history. This skill takes what was just discussed and files it as a permanent wiki page.

**Vault path:** `/Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain`

Read the vault's `CLAUDE.md` before creating pages — it is the source of truth for frontmatter schemas, page templates, and conventions. Do not duplicate its rules here; reference them.

---

## Note Type Decision

Determine the best type from the conversation content. The user's `$ARGUMENTS` may include a type hint or page name — use it if present.

| Content signal | Type | Folder | Example |
|---|---|---|---|
| Multi-step analysis, comparison, synthesis, research question | thread | `threads/` | "Comparing X vs Y", "Research: Topic" |
| Concept, framework, pattern, mental model | topic | `topics/` | "Spaced Repetition", "CAP Theorem" |
| Person, org, product, tool, place | entity | `entities/` | "Andrej Karpathy", "Cursor IDE" |
| Decision with rationale, session summary | thread | `threads/` | "Decision: Chose Postgres over Mongo" |

If the user specifies a type, use it. Otherwise pick the best fit. When in doubt, use `thread`.

---

## Save Workflow

1. **Scan** the current conversation. Identify the most valuable content to preserve.

2. **Duplicate check.** Read `index.md` from the vault. If a page already covers this topic, update that page instead of creating a new one. Tell the user: "Updated existing page [[page-name]]."

3. **Name.** If `$ARGUMENTS` includes a name, use it. Otherwise ask: "What should I call this note?" Keep names short and descriptive. Filename = kebab-case of the name.

4. **Determine type** using the table above.

5. **Rewrite declaratively.** Extract all relevant content from the conversation. Rewrite in present tense as standalone knowledge:
   - NOT: "The user asked about X and Claude explained that..."
   - YES: "X works by doing Y. The key tradeoff is Z."
   - Include all relevant context — future sessions read this page cold, with no access to this conversation.

6. **Create the page** in the correct folder with frontmatter matching the vault's `CLAUDE.md` templates. Add `[[wikilinks]]` to every mentioned concept, entity, or wiki page.

7. **Update `index.md`.** Add the new entry in the appropriate section with a 1-line summary.

8. **Append to `log.md`.** New entry at the TOP (newest on top):
   ```
   ## [YYYY-MM-DD HH:MM] save | {Page Title}
   - Type: {thread|topic|entity}
   - Location: {folder}/{filename}.md
   - From: conversation on {brief topic description}
   ```

9. **Update `hot.md`.** Overwrite completely with current vault context (~500 words).

10. **Confirm** to the user: "Saved as [[Page Title]] in {folder}/."

---

## Writing Style

- Declarative, present tense. Write the knowledge, not the conversation.
- Link every mentioned concept, entity, or wiki page with `[[wikilinks]]`.
- Cite sources where applicable: `(Source: [[page]])`.
- No hedging ("it seems", "perhaps"). If uncertain, mark explicitly: `(tentative)` or `(as of YYYY-MM-DD)`.
- Pages should be readable cold — include enough context that someone with no access to this conversation understands everything.

---

## What to Save vs. Skip

**Save:**
- Non-obvious insights or synthesis
- Decisions with rationale
- Analyses that took significant effort
- Comparisons likely to be referenced again
- Research findings or discovered frameworks
- Substantial explanations of how something works

**Skip:**
- Mechanical Q&A (lookup questions with obvious answers)
- Setup steps already documented elsewhere
- Temporary debugging sessions with no lasting insight
- Anything already in the wiki (update the existing page instead)
- Trivial facts that don't compound

If the conversation produced nothing worth saving, say so: "Nothing here worth filing — it's either trivial or already in the wiki."
