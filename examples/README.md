# Examples: Real Ingest of the Karpathy Gist

These files show a complete ingest of the Karpathy gist that inspired this skill:
**https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f**

In a real vault these files would live at `knowledge-management/raw/llm-wiki/` and `knowledge-management/wiki/llm-wiki/`. Here they are kept flat for readability.

## Files

| File | Type | Description |
|------|------|-------------|
| `raw-2026-04-19-llm-wiki-karpathy-gist.md` | raw | Source fetched from the gist — metadata header, original content preserved |
| `summary-llm-wiki-karpathy-gist.md` | summary | Per-source distillation: key points, entities mentioned, concepts discussed |
| `entity-andrej-karpathy.md` | entity | Named-thing page: role, affiliations, appearances table |
| `concept-llm-wiki-pattern.md` | concept | Three-layer architecture and three operations — thematic synthesis |
| `concept-compounding-knowledge-base.md` | concept | Why persistent wiki beats RAG — the bookkeeping insight and Memex connection |
| `synthesis-llm-wiki-beats-rag.md` | synthesis | Claim + supporting evidence + caveats — uses `Wiki pages:` not `Raw:` |
| `index-knowledge-management.md` | index | Topic index grouped by type subheadings: Concepts / Entities / Summaries / Comparisons & Syntheses |
| `log-knowledge-management.md` | log | Ingest entry (with Step 0 takeaways in comment) + lint entry |

## What One Ingest Produces

One source → six files created, following the compile workflow in order:

```
raw source fetched
  └── Step 0: takeaways surfaced (see log comment)
  └── Step 1: summary page
  └── Step 2: entity page (Andrej Karpathy)
  └── Step 3: concept pages (LLM Wiki Pattern, Compounding Knowledge Base)
  └── Step 4: synthesis page (LLM Wiki Beats RAG)
  └── Post-ingest: index updated, log appended
```

## Reading Order

Start with the raw source, then the summary (which maps it to wiki pages), then follow the links into the concept and entity pages. The synthesis page shows how the skill reasons across multiple wiki pages to produce a higher-order conclusion.
