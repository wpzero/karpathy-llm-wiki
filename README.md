# karpathy-llm-wiki

**A skill for building Karpathy-style LLM wikis with Claude Code and other Agent Skills tools.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

`karpathy-llm-wiki` packages [Karpathy's LLM Wiki idea](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) into one installable skill. Your agent ingests sources into `raw/`, compiles durable knowledge pages into `wiki/`, answers questions with citations, and lints the wiki for consistency.

## What Is an LLM Wiki?

Rather than retrieving from raw documents at query time (RAG), the LLM incrementally builds a structured wiki that gets richer with each source added — cross-references already in place, contradictions already flagged, conclusions already drawn.

This skill gives your agent three operations:

| Operation | What it does |
|-----------|--------------|
| **Ingest** | Fetches a source into `raw/`, then compiles it into wiki pages (summary, entities, concepts, comparisons, syntheses) |
| **Query** | Reads the wiki and answers with citations; recommends filing good answers back as new pages |
| **Lint** | Checks index integrity, broken links, orphan pages, and data gaps |

See [SKILL.md](SKILL.md) for the full specification and [examples/](examples/) for a worked example.

## Install

Clone the repo and place it in your tool's skill directory:

```bash
git clone git@github.com:wpzero/karpathy-llm-wiki.git ~/.claude/skills/karpathy-llm-wiki
```

For other tools, copy the directory into the appropriate skill location:

```bash
# Codex CLI
git clone git@github.com:wpzero/karpathy-llm-wiki.git .agents/skills/karpathy-llm-wiki

# Any Agent Skills-compatible tool
git clone git@github.com:wpzero/karpathy-llm-wiki.git <tool-skill-dir>/karpathy-llm-wiki
```

The skill is self-contained — `SKILL.md` is the schema and `references/` holds the page templates.

## Quick Start

The [examples/](examples/) directory shows a complete ingest of the Karpathy gist itself. Here is the same flow you would run:

### 1. Ingest the Karpathy gist

```
Ingest https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f into my knowledge-management wiki
```

The agent will:
- Surface 3–5 key takeaways before writing (Step 0)
- Save the raw source to `knowledge-management/raw/llm-wiki/2026-04-19-llm-wiki-karpathy-gist.md`
- Create a **summary page** distilling what the gist says
- Create an **entity page** for Andrej Karpathy
- Create **concept pages** for the LLM Wiki Pattern and Compounding Knowledge Base
- Create a **synthesis page** arguing why persistent wiki beats RAG
- Update `wiki/index.md` and append to `wiki/log.md`

See the output in [examples/](examples/).

### 2. Query your wiki

```
What do I know about why LLM wikis beat RAG?
```

The agent reads the index, finds the relevant concept and synthesis pages, and answers with citations. After a substantive answer it will suggest filing it back as a new wiki page.

### 3. Lint

```
Lint my knowledge-management wiki
```

Checks for broken links, missing index entries, orphan pages, and data gaps (topics referenced often but with thin source coverage).

## How the Workflow Works

```
<vault>/
  <domain>/                    e.g. knowledge-management/
    raw/                       immutable source files
      <topic>/
        YYYY-MM-DD-slug.md
    wiki/
      index.md                 domain index, grouped by topic → page type
      log.md                   append-only operation log
      <topic>/
        summary-<slug>.md      one per raw source
        entity-<name>.md       one per named person/org/tool/model/…
        concept-<idea>.md      thematic synthesis across sources
        comparison-<a>-vs-<b>.md
        synthesis-<claim>.md
  wiki/
    index.md                   global cross-domain index
```

Each ingest touches ~10–15 pages. Knowledge compounds: every source strengthens cross-references, flags contradictions, and deepens concept pages. The human curates sources and asks questions; the LLM does the bookkeeping.

## License

[MIT](LICENSE)
