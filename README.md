# agentic-wiki

An **LLM-maintained wiki** about **agentic engineering** — building and operating LLM agents (Spring AI, MCP, RAG, agent security, agent workflows).

It follows [Andrej Karpathy's "LLM Wiki" pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f): an LLM incrementally builds and maintains a persistent, interlinked set of markdown notes (a *compounding* artifact) rather than re-retrieving from raw documents on every query. Humans curate sources and ask questions; the LLM does the summarizing, cross-referencing, and bookkeeping.

## Layout

```
raw/     immutable source documents (session summaries, transcripts, articles)
wiki/    LLM-owned, interlinked concept pages
  index.md     catalog of all pages, by category
  overview.md  the "map of the concepts" — how everything connects
  log.md       append-only changelog
CLAUDE.md   the schema: conventions + workflows the LLM follows
```

## Browse

Open this folder in [Obsidian](https://obsidian.md/) and use the **graph view** to navigate the concept map. Start at [`wiki/overview.md`](wiki/overview.md) or [`wiki/index.md`](wiki/index.md).

## Provenance

Seed source: the **Spring AI @ ITKonekt** workshop (2026-06-10) by Victor Rentea. As more session transcripts are ingested, the wiki grows and re-links itself.
