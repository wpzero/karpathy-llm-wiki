---
type: concept
---
# Compounding Knowledge Base

> Sources: Andrej Karpathy, Unknown
> Raw: [raw-2026-04-19-llm-wiki-karpathy-gist.md](raw-2026-04-19-llm-wiki-karpathy-gist.md)
> Updated: 2026-04-19

## Overview

A knowledge base that grows more valuable with each addition — not just larger, but more interconnected, more consistent, and more synthesized. The LLM Wiki pattern achieves this by compiling knowledge once and keeping it current, rather than re-deriving it on every query.

## Why Wikis Fail Without LLMs

The tedious part of maintaining a knowledge base is not the reading or the thinking — it's the bookkeeping: updating cross-references, keeping summaries current, flagging when new data contradicts old claims, maintaining consistency across dozens of pages. Humans abandon wikis because the maintenance burden grows faster than the value. LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass.

## How Compounding Works

Each ingest does more than add a page — it integrates knowledge:
- New source may strengthen or challenge an existing synthesis
- Entity pages gain new appearances and updated attributes
- Contradictions are flagged inline with source attribution
- Cross-references connect the new source to existing pages

Over time the wiki reflects not just individual sources but the relationships between them. A question that requires synthesizing five sources is answered by reading pre-integrated concept pages — the synthesis work was already done at ingest time.

## Explorations Compound Too

Query answers that are valuable — comparisons, analyses, connections discovered — should be filed back as wiki pages. This means the wiki grows not just from ingested sources but from the questions asked of it.

## The Memex Connection

Karpathy connects this idea to Vannevar Bush's Memex (1945): a personal, curated knowledge store with associative trails between documents. Bush's vision was private and actively curated, with the connections as valuable as the documents themselves. The part Bush couldn't solve was who does the maintenance. The LLM handles that.

## See Also

- [LLM Wiki Pattern](concept-llm-wiki-pattern.md)
- [LLM Wiki Beats RAG for Long-Term Knowledge](synthesis-llm-wiki-beats-rag.md)
