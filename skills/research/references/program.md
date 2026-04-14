# Research Program

This file configures the research loop. The research skill reads it before every run. Edit it to match your domain and research style.

---

## Search Objectives

Default objectives for every research session:

- **Authoritative sources first.** Prefer: .edu, peer-reviewed papers, official documentation, primary sources, established publications, named experts with credentials.
- **Extract entities and concepts.** Every source should yield people, organizations, products, tools, and concepts worth tracking.
- **Extract frameworks.** If a source presents a mental model, decision framework, or taxonomy, capture it as a topic page.
- **Note contradictions.** When sources disagree, record both positions with citations. Do not silently pick a side.
- **Identify open questions.** What isn't settled? What needs more sources? File these explicitly.
- **Prefer recent sources** (last 2 years) unless the topic is foundational or historical. Always note the source date.

---

## Confidence Scoring

Label every claim with a confidence level when filing:

- **high** — Multiple independent authoritative sources agree. Primary source or official documentation confirms. Peer-reviewed or independently verified.
- **medium** — Single credible source. Or multiple sources partially agree but with caveats. Named author with relevant expertise but no independent confirmation.
- **low** — Single informal source. Opinion piece or speculation. Claim not independently verified. Source doesn't cite its own claims. Undated content.

Always note the source date for factual claims. Mark claims from sources older than 3 years as `(potentially stale — YYYY)`.

---

## Loop Constraints

- Max search rounds per topic: **3**
- Max wiki pages created per session: **15**
- Max sources fetched per round: **5**
- If max pages is reached before the loop completes: file what you have and note skipped items in Open Questions
- If a search returns nothing useful: note the dead end and move on, don't burn remaining rounds on the same angle

---

## Output Style

- **Declarative, present tense.** State what is known, not what was found.
- **Cite every non-obvious claim:** `(Source: [[page]])`.
- **Short pages.** Under 200 lines of body content. Split if longer.
- **No hedging language.** Not "it seems", "perhaps", "might be". If uncertain, mark explicitly: `(tentative)`, `(unverified)`, or `(as of YYYY-MM-DD)`.
- **Flag uncertainty with callouts:** `> [!gap] This claim needs verification.`
- **No ads, community footers, or attribution boilerplate** from scraped sources. Strip all of it.

---

## Domain Notes

### AI / Tech Research
- **Prefer:** arXiv papers, official GitHub repos, official product documentation, technical blogs by named engineers, Hacker News discussions with high engagement.
- **Caution:** LLM benchmarks are frequently gamed — treat leaderboard claims as low confidence unless independently reproduced. Product launch announcements are marketing, not evidence.
- **Extract:** model names, parameter counts, release dates, architectural details, benchmark results with methodology.

### Business / Market Research
- **Prefer:** SEC filings, Crunchbase, Bloomberg, verified industry reports, company 10-Ks, earnings transcripts.
- **Caution:** Press releases are low confidence without independent verification. Funding announcements don't equal product-market fit.
- **Extract:** revenue figures, funding rounds, key personnel, competitive positioning, market size estimates with methodology.

### Medical / Health Research
- **Prefer:** PubMed, Cochrane systematic reviews, peer-reviewed clinical trials, FDA approvals, NIH resources.
- **Always note:** sample size, study type (RCT vs. observational vs. case study), publication date, whether results have been replicated.
- **Caution:** Single studies are low confidence. Pre-prints are unverified. Supplement/wellness industry content is marketing unless backed by cited trials.

---

## Source Exclusions

Do not cite as high-confidence sources:
- **Reddit posts and forums** — use as pointers to primary sources only, never as evidence
- **Social media posts** (Twitter/X, LinkedIn, Facebook) — low confidence unless the author is a verified domain expert making a specific factual claim
- **Undated web pages** — cannot assess recency, mark as low confidence
- **Sources that don't cite their own claims** — if a page makes factual assertions with no references, it's low confidence at best
- **Content farms and SEO-optimized listicles** — zero evidentiary value, skip entirely
