---
name: query
description: "Answer questions using the Obsidian wiki vault. Reads hot cache first, then index, then relevant pages. Synthesizes answers with wikilink citations. Files good answers back as wiki pages. Supports quick, standard, and deep modes. Triggers on: what do you know about, query, what is, explain, summarize, find in wiki, search the wiki, based on the wiki, quick:, deep:."
---

# wiki-query: Query the Wiki

The wiki has already done the synthesis work. Read strategically, answer precisely, cite with `[[wikilinks]]`.

**Vault path:** `/Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain`

All paths below are relative to this vault root. There is NO `wiki/` subdirectory.

**Before creating any new wiki page**, read the vault's own `CLAUDE.md` (at the vault root) for frontmatter schemas, page templates, and conventions. Defer to it — don't duplicate it here.

---

## Auto-Depth Selection

Pick the mode based on the question, or let the user override.

| Mode | Trigger | Reads | Token budget | Best for |
|------|---------|-------|-------------|---------|
| **Quick** | `quick:` prefix, or simple factual Q ("what is X?", "when did Y?") | `hot.md` + `index.md` only | ~1,500 | Date lookups, definitions, quick facts |
| **Standard** | Default (no flag) | `hot.md` + `index.md` + 3-5 pages | ~3,000 | Most questions |
| **Deep** | `deep:` prefix, or "thorough", "comprehensive", "tell me everything", "compare X vs Y" | Full wiki scan + optional web | ~8,000+ | Synthesis, comparisons, gap analysis |

If the user doesn't specify, infer from the question:
- One entity or fact → Quick
- How/why question, or involves 2+ topics → Standard
- "Everything about", comparison across sources, broad synthesis → Deep

---

## Quick Mode (~1,500 tokens)

For simple factual lookups. Do not open individual wiki pages.

1. Read `hot.md`. If it answers the question → respond immediately.
2. Read `index.md`. Scan page titles and descriptions for the answer.
3. If found in index summary → respond with the answer. Cite the page: `(Source: [[page-name]])`.
4. If not found → say: "Not in quick cache. Want me to run a standard query?"

**Hard rule:** do not open individual wiki pages in quick mode. If the answer isn't in `hot.md` or `index.md`, escalate.

---

## Standard Mode (~3,000 tokens)

Default for most questions.

1. **Read** `hot.md`. Check if it already has the answer or directly relevant context.
2. **Read** `index.md`. Identify the 3-5 most relevant pages by scanning titles, categories, and descriptions.
3. **Read** those pages. Follow `[[wikilinks]]` to depth-2 max for key entities or related topics. No deeper.
4. **Synthesize** the answer. Cite every claim: `(Source: [[page-name]])`.
5. **Gap check:** if the wiki doesn't cover part of the question, say so explicitly. Do not fill gaps from training data.
6. **Offer to file** if the answer is a substantive synthesis: "This seems worth keeping. Want me to save it as a wiki page?"

---

## Deep Mode (~8,000+ tokens)

For synthesis, comparisons, or "tell me everything about X."

1. Read `hot.md` and `index.md`.
2. Identify **all** relevant pages across topics, entities, sources, and threads.
3. Read every relevant page. Use Grep to find mentions across the vault if needed. No skipping.
4. If wiki coverage is thin on a subtopic, offer to supplement:
   - `pplx "<query>"` via Bash for AI-powered research
   - `brave "<query>"` via Bash for web search
   - State clearly which parts came from the wiki vs. web supplement.
5. Synthesize a comprehensive answer with full `[[wikilink]]` citations.
6. **Always offer to file the result.** Deep answers are too valuable to lose to chat history.

---

## Citation Style

Cite wiki pages with Obsidian wikilinks inline or at the end of a claim:

- Inline: "Spaced repetition works because of the spacing effect `(Source: [[spaced-repetition]])`"
- Multiple sources: "This connects several ideas `(Sources: [[topic-a]], [[topic-b]], [[entity-name]])`"

When supplementing from the web, distinguish clearly:
- Wiki content: cite with `[[wikilinks]]`
- Web content: cite with URL and note it's external

---

## Filing Answers Back

Good answers compound into the wiki. Don't let insights disappear into chat.

**When to file:**
- Substantive synthesis, comparison, or analysis → yes, file it
- New connection between existing pages → yes, file it
- Simple factual lookup → no, answer inline only

**Where to file:**
- New concept or framework → `topics/`
- Evolving question or comparison → `threads/`
- Never file to `sources/` (those are ingest receipts only)

**Filing process:**
1. Create the page using the appropriate template from the vault's `CLAUDE.md`.
2. Add `[[cross-references]]` both directions on related pages.
3. Add entry to `index.md`.
4. Append to `log.md` (newest on top):
   ```
   ## [YYYY-MM-DD HH:MM] query | "{question paraphrase}"
   - Read: {pages consulted}
   - Filed: {new page path, or "none — answered inline"}
   ```
5. Update `hot.md` with the new context.

---

## Gap Handling

When the wiki can't answer the question — or can only partially answer it:

1. **Say so clearly.** "I don't have enough in the wiki to answer this well."
2. **Be specific about the gap.** "I have pages on X and Y, but nothing on Z."
3. **Suggest next steps.** "Want to find a source on this? I can search with `pplx` or `brave`, or you can paste/link one for ingest."
4. **Do NOT fabricate.** Do not answer domain-specific wiki questions from training data. If the question is about content that should be in this wiki (a person mentioned in sources, a concept from an ingested article), the answer comes from the wiki or it's a gap. Period.

General knowledge questions (how does TCP work, what year was X founded) can be answered from general knowledge — but if the user is clearly asking "what does my wiki say about X", only the wiki counts.

---

## Token Discipline

Read the minimum needed:

| Layer | Cost | When to stop |
|-------|------|-------------|
| `hot.md` | ~500 tokens | If it has the answer |
| `index.md` | ~1,000 tokens | If you can identify relevant pages |
| 3-5 pages | ~300 each | Usually sufficient for standard |
| 10+ pages | expensive | Only for deep mode synthesis |

If `hot.md` has the answer, respond without reading further. Every page you open costs tokens — be deliberate.

---

## What Not to Do

- Do not fabricate wiki content. If it's not in the vault, say so.
- Do not use `WebFetch` or `WebSearch` tools. Use `pplx` and `brave` via Bash instead.
- Do not modify `raw/` files. Ever.
- Do not skip the citation. Every claim from the wiki gets a `[[wikilink]]`.
- Do not read the entire vault for a standard query. That's what deep mode is for.
- Do not create pages without offering first (except in deep mode, where you always offer).
