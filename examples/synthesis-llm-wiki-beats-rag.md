---
type: synthesis
---
# A Persistently Maintained LLM Wiki Compounds in Value in Ways RAG Cannot

> Sources: Andrej Karpathy, Unknown
> Wiki pages: [LLM Wiki Pattern](concept-llm-wiki-pattern.md); [Compounding Knowledge Base](concept-compounding-knowledge-base.md)
> Updated: 2026-04-19

## Claim

A persistently maintained LLM wiki compounds in value over time in ways that RAG over raw documents cannot, because RAG re-derives knowledge on every query while the wiki accumulates pre-integrated synthesis.

## Supporting Evidence

- [LLM Wiki Pattern](concept-llm-wiki-pattern.md) — each ingest updates 10–15 pages: contradictions flagged, cross-references added, syntheses updated. By the time a question is asked, the hard work is already done.
- [Compounding Knowledge Base](concept-compounding-knowledge-base.md) — RAG finds and pieces together fragments on every query; the wiki answers from pre-integrated concept pages. A five-document synthesis question costs one page read instead of five chunk retrievals.
- [Compounding Knowledge Base](concept-compounding-knowledge-base.md) — query answers filed back as pages mean explorations compound too, not just ingested sources. The wiki grows smarter from being used.
- [raw-2026-04-19-llm-wiki-karpathy-gist.md](raw-2026-04-19-llm-wiki-karpathy-gist.md) — Karpathy directly: "the LLM is rediscovering knowledge from scratch on every question. There's no accumulation."

## Caveats & Counterarguments

- RAG has no ingest cost — useful for one-off questions over a document set you don't intend to revisit. The wiki's advantage only materializes with consistent long-term ingest.
- Wiki quality depends on schema discipline. A poorly maintained schema produces inconsistent pages; RAG over raw sources is at least consistent with the source.
- At very small scale (< ~20 sources), the wiki may not yet have enough cross-references to outperform RAG. The compounding advantage grows with volume.
- RAG systems with re-ranking (e.g. qmd) narrow the gap at query time, though they still don't produce pre-integrated synthesis.

## See Also

- [LLM Wiki Pattern](concept-llm-wiki-pattern.md)
- [Compounding Knowledge Base](concept-compounding-knowledge-base.md)
- [Andrej Karpathy](entity-andrej-karpathy.md)
