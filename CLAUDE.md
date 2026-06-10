# agentic-wiki — schema & conventions

This repository is an **LLM Wiki** in the sense of [Karpathy's pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f): an LLM incrementally builds and maintains a persistent, interlinked markdown knowledge base instead of re-deriving knowledge from raw sources on every query. **Humans curate sources and ask questions; the LLM does all the writing, cross-referencing, and bookkeeping.**

Topic: **agentic engineering** — building and operating LLM agents (Spring AI, MCP, RAG, agent security, agent workflows). Seeded from Victor Rentea's training sessions; it will grow as more session transcripts are ingested.

## Layers

- **`raw/`** — immutable source documents (session summaries, transcripts, articles). The LLM reads these but never edits them. Source of truth.
- **`wiki/`** — LLM-owned markdown. One file per concept/tool/pattern, richly cross-linked. The LLM creates, updates, and lints these.
- **`CLAUDE.md`** (this file) — the schema: structure, conventions, workflows.

## Page conventions

Every `wiki/` page is kebab-case (`chat-memory.md`) and starts with YAML frontmatter:

```yaml
---
title: Chat Memory
category: concept        # one of: concept | tool | pattern | security | entity | source
tags: [spring-ai, memory, context]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---
```

Body rules:
- First line after frontmatter is a one-sentence definition.
- Link **liberally** to related pages with `[[slug]]` wikilinks (Obsidian resolves by filename across folders). Aim for ≥3 cross-links per page; a `[[slug]]` that doesn't exist yet is a valid TODO marker, not an error.
- End each page with a `## See also` list of `[[wikilinks]]`.
- Cite the originating source as a `[[...]]` link to a file in `raw/`.
- Escape literal tildes as `\~` (markdown strikethrough) — prefer `≈` for "approximately".

## Special files

- **`wiki/index.md`** — content catalog, grouped by category, one line per page with a `[[link]]` and summary. Update on every ingest.
- **`wiki/log.md`** — append-only, chronological. Each entry: `## [YYYY-MM-DD] operation | Title`. Grep with `grep "^## \[" wiki/log.md | tail -5`.
- **`wiki/overview.md`** — the synthesis / "map of the concepts": how everything connects.

## Workflows

- **Ingest.** Drop a source in `raw/`, then: read it → write/update concept pages → update `index.md` → append to `log.md`. One source may touch 10–15 pages.
- **Query.** Read `index.md` first, drill into relevant pages, synthesize with `[[citations]]`; file good answers back as new pages.
- **Lint.** Periodically check for contradictions, stale claims, orphan pages (no inbound links), concepts mentioned but lacking a page, and missing cross-references.

## Browse

Open the repo folder in [Obsidian](https://obsidian.md/) (free) to navigate links and see the **graph view** — the best way to see the shape of the wiki. It's a plain git repo of markdown, so you also get version history for free.
