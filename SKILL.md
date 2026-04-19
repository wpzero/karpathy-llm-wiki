---
name: karpathy-llm-wiki
description: "Use when building or maintaining a personal LLM-powered knowledge base. Triggers: ingesting sources into a wiki, querying wiki knowledge, linting wiki quality, 'add to wiki', 'what do I know about', or any mention of 'LLM wiki' or 'Karpathy wiki'."
---

# Karpathy LLM Wiki

Build and maintain a personal knowledge base using LLMs. Rather than retrieving from raw documents at query time (RAG), the LLM incrementally builds a structured wiki that gets richer with each source added — cross-references already in place, contradictions already flagged, conclusions already drawn.

The tedious part of maintaining a knowledge base is not the reading or the thinking — it's the bookkeeping. Humans abandon wikis because the maintenance burden grows faster than the value. LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass.

**Human responsibilities:** curating sources, directing analysis, asking questions, thinking about meaning.  
**LLM responsibilities:** summarizing, cross-referencing, filing, bookkeeping, maintaining consistency.

Core mechanics:
- "The LLM writes and maintains the wiki; the human reads and asks questions."
- "The wiki is a persistent, compounding artifact."
- A single ingest touches ~10–15 wiki pages.
- This schema (SKILL.md) is meant to evolve — adapt structure, naming, and conventions as you discover what works for your domain.

## Page Types

Every wiki file (except `index.md` and `log.md`) carries a `type:` field in its frontmatter:

| Type | What it is | Template |
|------|-----------|---------|
| `summary` | One per raw source — distills what the source says | `references/summary-template.md` |
| `entity` | One per discrete named thing: person, org, tool, model, paper, dataset, algorithm, project, event, product, framework, place, … | `references/entity-template.md` |
| `concept` | Thematic idea synthesized across multiple sources | `references/concept-template.md` |
| `comparison` | Side-by-side evaluation of two or more entities or concepts | `references/comparison-template.md` |
| `synthesis` | Higher-order conclusion drawn by reasoning across multiple wiki pages | `references/synthesis-template.md` |
| `archive` | Point-in-time snapshot of a saved query answer | `references/archive-template.md` |

Entity pages grow over time as more sources mention the same entity. Summary, concept, comparison, and synthesis pages are created at ingest or query time; archive pages are created only when the user explicitly saves a query answer.

## Architecture

Knowledge is organized into **domains** — top-level folders in the vault, each representing a distinct subject area (e.g., `AI/`, `Work/`, `Books/`). Each domain has its own `raw/` and `wiki/` pair, keeping compounding artifacts separate.

```
<vault>/
  wiki/
    index.md              ← global cross-domain index
  <domain>/               ← e.g., AI/, Work/, Books/
    raw/                  ← immutable sources for this domain
    wiki/
      index.md            ← domain-scoped index
      log.md              ← append-only operation log
      <topic>/
        <page>.md
```

**`<domain>/raw/`** — Immutable source material. You read, never modify. Organized by topic subdirectories.

**`<domain>/wiki/`** — Compiled wiki pages. You have full ownership. Topic subdirectories, one level only: `<domain>/wiki/<topic>/<page>.md`. Two special files:
- `<domain>/wiki/index.md` — Domain index. Grouped by topic, then by page type subheading (Concepts / Entities / Summaries / Comparisons & Syntheses / Archived) within each topic. Omit subheadings with no pages.
- `<domain>/wiki/log.md` — Append-only operation log for this domain.

**`<vault>/wiki/index.md`** — Global index. One section per domain, one row per page. Updated on every ingest/archive/lint-fix.

**SKILL.md** (this file) — Schema layer. Defines structure and workflow rules.

Templates live in `references/` relative to this file. Read them when you need the exact format for raw files, summary pages, entity pages, concept pages, archive pages, or the index.

### Domain Resolution

Every operation requires a **domain**. Resolve it in this order:

1. User explicitly names it: "ingest X into AI", "query my Books wiki"
2. Inferred from conversation context (recent domain mentioned, current vault path)
3. If ambiguous, ask: "Which domain? (e.g., AI, Work, Books)"

Never default silently to a domain when multiple exist.

### Initialization

Triggers only on the first Ingest for a given domain. Check whether the domain's directories exist. Create only what is missing; never overwrite existing files:

- `<domain>/raw/` directory (with `.gitkeep`)
- `<domain>/wiki/` directory (with `.gitkeep`)
- `<domain>/wiki/index.md` — heading `# <Domain> Knowledge Base`, empty body
- `<domain>/wiki/log.md` — heading `# <Domain> Wiki Log`, empty body
- `<vault>/wiki/` directory (if it does not exist yet)
- `<vault>/wiki/index.md` — heading `# Knowledge Base Index`, empty body (if it does not exist yet)

If Query or Lint cannot find a domain's wiki structure, tell the user: "Run an ingest first to initialize the [domain] wiki." Do not auto-create.

---

## Ingest

Fetch a source into `<domain>/raw/`, then compile it into `<domain>/wiki/`. Always both steps, no exceptions.

### Fetch (raw/)

1. Get the source content using whatever web or file tools your environment provides. If nothing can reach the source, ask the user to paste it directly.

2. Pick a topic directory. Check existing `<domain>/raw/` subdirectories first; reuse one if the topic is close enough. Create a new subdirectory only for genuinely distinct topics.

3. Save as `<domain>/raw/<topic>/YYYY-MM-DD-descriptive-slug.md`.
   - Slug from source title, kebab-case, max 60 characters.
   - Published date unknown → omit the date prefix from the file name (e.g., `descriptive-slug.md`). The metadata Published field still appears; set it to `Unknown`.
   - If a file with the same name already exists, append a numeric suffix (e.g., `descriptive-slug-2.md`).
   - Include metadata header: source URL, collected date, published date.
   - Preserve original text. Clean formatting noise. Do not rewrite opinions.

   See `references/raw-template.md` for the exact format.

### Compile (wiki/)

Follow this sequence on every ingest. Target **10–15 wiki pages touched** per source.

**Step 0 — Surface key takeaways (interactive mode)**

Before writing any wiki files, briefly surface 3–5 key takeaways from the source:
- What is the core claim or finding?
- Which entities are most prominent?
- Which existing wiki pages are most likely affected?

Then proceed to Steps 1–4 unless the user redirects. Skip this step only when the user explicitly requests batch ingestion (see Batch Ingestion below).

**Step 1 — Summary page (always)**

Create or update a `summary` page for this source. Name it `summary-<source-slug>.md` in the same topic directory as the raw file. See `references/summary-template.md`.

**Step 2 — Entity pages**

For every discrete named thing the source mentions — person, org, tool, model, paper, dataset, algorithm, project, event, product, framework, place, or anything else that is a specific named entity:
- If an entity page already exists anywhere in the domain: update it. Add this source's summary page to its Appearances table. Refresh Updated date.
- If no entity page exists: create one in the most relevant topic directory. See `references/entity-template.md`.

Name entity files after the entity itself (e.g., `andrej-karpathy.md`, `openai.md`, `transformer.md`).

**Step 3 — Concept pages**

For each theme or idea the source discusses:
- If an existing concept page covers this theme: merge new insights in. Add source to Sources/Raw. Update affected sections.
- If the concept is new: create a concept page in the most relevant topic directory. Name it after the concept, not the source. See `references/concept-template.md`.

A single source often warrants both updating existing concept pages and creating new ones for distinct ideas it introduces.

**Step 4 — Comparison and synthesis pages**

- **Comparison**: create or update a comparison page when the source explicitly contrasts two or more entities/concepts, or when the wiki now has enough pages to make a meaningful side-by-side useful. See `references/comparison-template.md`.
- **Synthesis**: create a synthesis page when the new source plus existing wiki pages together support a higher-order conclusion that no single page captures. State the claim in the title. See `references/synthesis-template.md`. Use the `Wiki pages:` metadata field (not `Raw:`) to link to the pages being reasoned across.

**Conflict annotation**: if the new source contradicts existing content anywhere, annotate the disagreement inline with source attribution.

Key formatting notes (all page types):
- Sources field: author, organization, or publication name + date, semicolon-separated.
- Raw field: markdown links to raw/ files, semicolon-separated.
- Relative paths from `<domain>/wiki/<topic>/` use `../../../raw/<topic>/<file>.md` (three levels up to vault root, then into the domain's raw/).

  Example: concept page at `AI/wiki/llm/attention.md` → raw at `AI/raw/llm/source.md` → relative path `../../../raw/llm/source.md`.

### Cascade Updates

After the primary pages, check for ripple effects **within the same domain only**:

1. Scan pages in the same topic directory for content affected by the new source.
2. Scan `<domain>/wiki/index.md` entries in other topics for pages covering related concepts.
3. Update every page whose content is materially affected. Each updated file gets its Updated date refreshed.

Archive pages are never cascade-updated (they are point-in-time snapshots). Cross-domain cascade updates do not happen automatically — if a source clearly affects another domain, mention it to the user.

### Post-Ingest

1. Update `<domain>/wiki/index.md`: add or update entries for every touched page, including the page Type column. When adding a new topic section, include a one-line description. The Updated date reflects when the page's knowledge content last changed. See `references/index-template.md` for format.

2. Update `<vault>/wiki/index.md`: add or update only the rows for pages touched in this operation. Add a domain section if this is the first page for that domain.

3. Append to `<domain>/wiki/log.md`:

```
## [YYYY-MM-DD] ingest | <primary page title>
- Updated: <cascade-updated page title>
```

Omit `- Updated:` lines when no cascade updates occur.

### Batch Ingestion

When the user asks to ingest multiple sources in one pass: skip Step 0 for each source. Fetch all raw files first, then compile all in sequence without surfacing per-source takeaways. Log each source as a separate entry.

---

## Query

Search the wiki and answer questions. Examples of triggers:
- "What do I know about X?"
- "Summarize everything related to Y"
- "Compare A and B based on my wiki"
- "What wikis do I have?" → List domains (see below)

### Output Formats

Choose the format that best fits the question:

| Question type | Recommended format |
|---|---|
| Factual lookup, summary | Prose with inline citations |
| "Compare A vs B" | Markdown comparison table |
| Multi-step argument / conclusion | Prose with Claim + Evidence structure |
| Timeline or ranked list | Numbered markdown list |
| Slides / presentation | Marp-fenced markdown (`--- marp: true ---`) |
| Data / metrics across sources | Table or code block |

When archiving, match the template to the format: comparison answer → `comparison` page; synthesized conclusion → `synthesis` page; general answer → `archive` page.

### Domain-scoped query

1. Resolve the domain (see Domain Resolution above).
2. Read `<domain>/wiki/index.md` to locate relevant pages.
3. Read those pages and synthesize an answer.
4. Prefer wiki content over your own training knowledge. Cite sources with markdown links using vault-root-relative paths: `[Page Title](<domain>/wiki/topic/page.md)`.
5. Output the answer in the conversation. Do not write files unless asked.
6. After delivering a substantive answer (more than a factual lookup), recommend filing it: *"This answer synthesized [N] pages — worth archiving? It would become a [comparison/synthesis/archive] page in [domain]/wiki/[topic]."* Let the user decide; do not auto-file.

### Cross-domain query

When the user's question spans multiple domains or they don't specify one:

1. Read `<vault>/wiki/index.md` to identify relevant pages across all domains.
2. Read the relevant pages from each domain.
3. Synthesize an answer with citations showing which domain each comes from.

### List Domains

When the user asks "what wikis do I have?" or similar:

- Scan the vault for folders containing `wiki/index.md` (i.e., initialized domains).
- Report: domain name, number of pages, last log entry date.

### Archiving

Archiving happens in two ways:
1. **Explicit**: user asks to archive upfront — proceed immediately.
2. **Recommended**: LLM suggested filing after answering (step 6 above) and user confirms — proceed the same way.

To archive:

1. Pick the right template based on the answer's form: comparison table → `comparison-template.md`; synthesized conclusion → `synthesis-template.md`; general answer → `archive-template.md`.
2. Write the page in the most relevant domain and topic directory. Sources: markdown links to the wiki pages cited. No Raw field (unless a synthesis draws directly on raw sources). File name reflects the query topic.
3. Always create a new page. Never merge into existing pages.
4. Update both `<domain>/wiki/index.md` (under the correct type subheading) and `<vault>/wiki/index.md`.
5. Append to `<domain>/wiki/log.md`:
   ```
   ## [YYYY-MM-DD] query | Archived: <page title>
   ```

---

## Lint

Quality checks on a domain's wiki. Specify the domain; if not specified, ask. Two categories with different authority levels.

### Deterministic Checks (auto-fix)

**Index consistency** — compare `<domain>/wiki/index.md` against actual files (excluding index.md and log.md):
- File exists but missing from index → add entry under the correct type subheading with `(no summary)` placeholder.
- Index entry points to nonexistent file → mark as `[MISSING]`. Do not delete; let the user decide.
- Page is under the wrong type subheading → move it to the correct one.
- Subheading has no pages → remove the subheading.
- Sync any changes to `<vault>/wiki/index.md`.

**Internal links** — for every markdown link in wiki/ page files (body text and Sources metadata), excluding Raw field links and index.md/log.md:
- Target does not exist → search the domain's wiki/ for a file with the same name.
  - Exactly one match → fix the path.
  - Zero or multiple matches → report to the user.

**Raw references** — every link in a Raw field must point to an existing raw/ file:
- Target does not exist → search the domain's raw/ for a file with the same name.
  - Exactly one match → fix the path.
  - Zero or multiple matches → report to the user.

**See Also** — within each topic directory:
- Add obviously missing cross-references between related pages.
- Remove links to deleted files.

**Missing type frontmatter** — any wiki file (excluding index.md and log.md) without a `type:` field → report as lint issue with the file path. Do not auto-assign; let the user classify.

### Heuristic Checks (report only)

- Factual contradictions across pages
- Outdated claims superseded by newer sources
- Missing conflict annotations where sources disagree
- Orphan pages with no inbound links from other wiki pages
- Missing cross-topic references
- Concepts frequently mentioned but lacking a dedicated concept page
- Archive pages whose cited source pages have been substantially updated since archival
- Entity pages whose Appearances table is missing a recently ingested summary page
- Sources that were ingested but have no corresponding summary page
- **Data gaps** — entities or concepts referenced frequently across pages (high inbound link count or mention frequency) but with thin coverage (few raw sources, short page, or no raw file). Report as candidates for targeted ingestion. Example: *"'mixture-of-experts' is referenced in 7 pages but has only 1 raw source — consider ingesting more."*

### Post-Lint

Append to `<domain>/wiki/log.md`:

```
## [YYYY-MM-DD] lint | <N> issues found, <M> auto-fixed
```

---

## Conventions

- Standard markdown with relative links throughout wiki/ files.
- wiki/ supports one level of topic subdirectories only. No deeper nesting.
- Today's date for log entries, Collected dates, and Archived dates. Updated dates reflect when the page's knowledge content last changed. Published dates come from the source (use `Unknown` when unavailable).
- Inside wiki/ files, all markdown links use paths relative to the current file. In conversation output, use vault-root-relative paths (e.g., `AI/wiki/topic/page.md`).
- Every wiki page (except index.md and log.md) has a `type:` frontmatter field: `summary`, `entity`, `concept`, `comparison`, `synthesis`, or `archive`.
- Ingest updates `<domain>/wiki/index.md`, `<vault>/wiki/index.md`, and `<domain>/wiki/log.md`. Archive (from Query) updates both indexes and the domain log. Lint updates the domain log (and both indexes only when auto-fixing entries). Plain queries do not write any files.
- Cascade updates stay within a domain. Cross-domain effects are surfaced to the user, not applied automatically.
