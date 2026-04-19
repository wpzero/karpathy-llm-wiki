---
type: summary
---
# Summary: LLM Wiki (Karpathy Gist)

> Sources: Andrej Karpathy, Unknown
> Raw: [raw-2026-04-19-llm-wiki-karpathy-gist.md](raw-2026-04-19-llm-wiki-karpathy-gist.md)
> Updated: 2026-04-19

## Overview

Karpathy proposes building a personal knowledge base where the LLM incrementally writes and maintains a structured wiki instead of re-deriving answers from raw documents at query time. The key insight: knowledge is compiled once and kept current — a persistent, compounding artifact rather than a retrieval system.

## Key Points

- RAG re-derives knowledge on every query; the LLM wiki compiles it once and keeps it current
- Three layers: raw sources (immutable) / wiki (LLM-owned markdown) / schema (CLAUDE.md / AGENTS.md)
- Wiki page types named: summaries, entity pages, concept pages, comparisons, overviews, syntheses
- A single ingest touches 10–15 wiki pages; LLM discusses takeaways before writing
- Good query answers should be filed back into the wiki so explorations compound
- Lint periodically for contradictions, orphans, stale claims, data gaps
- Human responsibilities: curating sources, directing analysis, asking questions, thinking
- LLM responsibilities: summarizing, cross-referencing, filing, bookkeeping, maintaining consistency
- Schema is intentionally abstract — co-evolve it with your LLM to fit your domain
- Related to Vannevar Bush's Memex (1945): private, curated, associative — the part Bush couldn't solve was maintenance; LLMs handle that

## Entities Mentioned

- [Andrej Karpathy](entity-andrej-karpathy.md) — author; AI researcher who proposed this pattern
- Obsidian — recommended as the wiki viewer/IDE alongside the LLM agent
- qmd — optional local search engine for markdown (BM25/vector, MCP server)
- NotebookLM, ChatGPT file uploads — cited as examples of the RAG approach this pattern improves on

## Concepts Discussed

- [LLM Wiki Pattern](concept-llm-wiki-pattern.md) — the full three-layer architecture and operations
- [Compounding Knowledge Base](concept-compounding-knowledge-base.md) — why persistent wiki beats RAG

## See Also

- [LLM Wiki Beats RAG for Long-Term Knowledge](synthesis-llm-wiki-beats-rag.md)
