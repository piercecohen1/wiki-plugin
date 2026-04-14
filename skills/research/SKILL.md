---
name: research
description: >
  Multi-round autonomous research loop. Takes a topic, runs web searches across
  multiple angles, fetches and synthesizes sources, then files everything into the
  wiki as structured pages. Based on Karpathy's autoresearch pattern with a
  configurable program.md. Triggers on: "research [topic]", "deep dive into [topic]",
  "investigate [topic]", "find everything about [topic]", "go research".
---

# research: Multi-Round Autonomous Research

You are a research agent. You take a topic, run iterative web searches, synthesize findings, and file everything into the wiki. The user gets wiki pages, not a chat response.

**Vault path:** `/Users/piercecohen/Library/Mobile Documents/iCloud~md~obsidian/Documents/second-brain`

---

## Before Starting

1. Read `references/program.md` to load research objectives, constraints, and domain notes. This file is user-configurable — its rules override defaults here.
2. Read the vault's `CLAUDE.md` for frontmatter schemas and page templates.
3. Read `index.md` from the vault to know what already exists (avoid duplicates).
4. Parse `$ARGUMENTS` for the research topic. If no topic is provided, ask.

---

## Tool Mapping

Do NOT use WebSearch or WebFetch. Use these instead:

| Task | Tool | Invocation |
|---|---|---|
| AI-powered research (broad questions) | Bash | `pplx "<query>"` |
| Web search (targeted queries) | Bash | `brave "<query>"` |
| Scrape a URL | Bash | `firecrawl scrape <url>` |
| YouTube transcript | Skill | invoke `yt-transcript` skill |

---

## Research Loop

### Round 1 — Broad Search

1. Decompose the topic into **3-5 distinct search angles** (different facets, perspectives, or sub-questions).
2. For each angle: run `pplx "<query>"` with a well-formed natural-language question.
3. From each result: extract key claims, entities, concepts, contradictions, and open questions.
4. For the **2-3 most promising URLs** surfaced by pplx: run `firecrawl scrape <url>` to get full content.

**Max sources fetched this round: 5.**

### Round 2 — Gap Fill

5. Review Round 1 findings. Identify:
   - Missing perspectives or unanswered sub-questions
   - Contradictions that need resolution
   - Claims that lack authoritative sourcing
6. For each gap: run `brave "<query>"` with targeted keyword searches.
7. For top results: `firecrawl scrape <url>` to fetch full content.

**Max sources fetched this round: 5.**

### CONFIRMATION CHECKPOINT

**Stop here.** Report findings to the user:

```
Research so far: {Topic}

Rounds completed: 2 | Sources fetched: N

Key findings:
- {Finding 1}
- {Finding 2}
- {Finding 3}

Gaps remaining:
- {Gap 1}
- {Gap 2}

Proceed to Round 3 for deeper investigation, or file what we have?
```

**Wait for user approval before continuing.** If the user says file it, skip to Filing. If they approve Round 3, continue.

### Round 3 — Deep (Optional, User-Approved Only)

8. Target remaining gaps and contradictions with focused searches.
9. Use `brave` for targeted queries, `firecrawl scrape` for promising URLs.
10. Resolve contradictions where possible; flag unresolvable ones.

**Max sources fetched this round: 5.**

---

## Filing Results

After research is complete (or after checkpoint if user says to file), create these pages:

### sources/

One page per major source found. Use the source template from the vault's `CLAUDE.md`:
- Frontmatter: `type: source`, `category`, `date`
- Body: summary of the source, what it contributes, key takeaways
- Link to raw file if content was saved to `raw/`

### topics/

One page per significant concept or framework extracted. Only create if the concept is substantive enough to stand alone. **Check index.md first** — update existing topic pages rather than creating duplicates.

### entities/

One page per significant person, org, product, or tool identified. **Check index.md first** — update existing entity pages rather than creating duplicates.

### threads/Research: {Topic}.md

The master synthesis page. Everything comes together here. Structure:

```markdown
---
type: thread
category: synthesis
status: active
---
# Research: {Topic}

**Summary:** {2-3 sentence overview of what was found.}

## Key Findings
- {Finding 1} (Source: [[source-page]])
- {Finding 2} (Source: [[source-page]])

## Key Entities
- [[Entity Name]] — role/significance

## Key Concepts
- [[Concept Name]] — one-line definition

## Contradictions
- [[Source A]] says X. [[Source B]] says Y. {Brief note on credibility.}

## Open Questions
- {Question that research didn't fully answer}
- {Gap that needs more sources}

## Related
- [[every-page-created-this-session]]

## Sources
- [[source-1]] — author, date
- [[source-2]] — author, date
```

---

## After Filing

1. **Update `index.md`** — add all new pages to the appropriate sections with 1-line summaries.

2. **Append to `log.md`** (newest on top):
   ```
   ## [YYYY-MM-DD HH:MM] research | {Topic}
   - Rounds: N
   - Sources fetched: N
   - Pages created: [[Page 1]], [[Page 2]], ...
   - Synthesis: [[Research: {Topic}]]
   - Key finding: {one sentence}
   ```

3. **Update `hot.md`** — overwrite completely with current vault context (~500 words).

---

## Report to User

After filing everything:

```
Research complete: {Topic}

Rounds: N | Sources fetched: N | Pages created: N

Created:
  threads/research-{topic}.md (synthesis)
  sources/{source-1}.md
  topics/{concept-1}.md
  entities/{entity-1}.md

Key findings:
- {Finding 1}
- {Finding 2}
- {Finding 3}

Open questions filed: N
```

---

## Constraints

Follow the limits in `references/program.md`:
- **Max rounds:** 3
- **Max pages per session:** 15
- **Max sources fetched per round:** 5

If a constraint conflicts with completeness, respect the constraint and note what was left out in the Open Questions section of the synthesis page.
