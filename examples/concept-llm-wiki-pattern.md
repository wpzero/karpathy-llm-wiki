---
type: concept
---
# LLM Wiki Pattern

> Sources: Andrej Karpathy, Unknown
> Raw: [raw-2026-04-19-llm-wiki-karpathy-gist.md](raw-2026-04-19-llm-wiki-karpathy-gist.md)
> Updated: 2026-04-19

## Overview

A pattern for building personal knowledge bases where the LLM incrementally writes and maintains a structured wiki. Rather than re-deriving answers from raw documents at query time (RAG), knowledge is compiled once and kept current — growing richer with every source added and every question asked.

## Three-Layer Architecture

**Raw sources** — immutable source documents (articles, papers, images). The LLM reads but never modifies them. Source of truth.

**The wiki** — LLM-generated markdown files. Page types: summaries, entity pages, concept pages, comparisons, syntheses. The LLM owns this layer entirely: creates pages, updates them as new sources arrive, maintains cross-references.

**The schema** — configuration document (CLAUDE.md / AGENTS.md) that defines wiki structure, naming conventions, and workflows. Co-evolved by the human and LLM over time.

## Three Operations

**Ingest** — process a new source: discuss takeaways → write summary page → update entity pages → update concept pages → update index → append to log. Touches 10–15 pages per source. Can be done one-at-a-time with guidance or batch-processed.

**Query** — ask questions against the wiki: LLM reads index → reads relevant pages → synthesizes answer with citations. Good answers are filed back as new wiki pages so explorations compound.

**Lint** — periodic health check: contradictions between pages, stale claims, orphan pages, missing cross-references, data gaps for web search.

## Index and Log

**index.md** — content-oriented catalog. Organized by category. LLM reads it first on every query. Updated on every ingest.

**log.md** — append-only chronological record of all operations. Parseable with unix tools via consistent prefix: `## [YYYY-MM-DD] operation | title`.

## Human / LLM Division

**Human**: curating sources, directing analysis, asking questions, thinking about meaning.  
**LLM**: summarizing, cross-referencing, filing, bookkeeping, maintaining consistency.

## See Also

- [Compounding Knowledge Base](concept-compounding-knowledge-base.md)
- [LLM Wiki Beats RAG for Long-Term Knowledge](synthesis-llm-wiki-beats-rag.md)
- [Andrej Karpathy](entity-andrej-karpathy.md)
