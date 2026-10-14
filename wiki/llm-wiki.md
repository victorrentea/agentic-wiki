---
title: LLM Wiki
category: tool
tags: [knowledge-management, obsidian, rag, compounding-artifact, legacy-codebases]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-05-13-ai-it-services]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-10
updated: 2026-10-13
---

Andrej Karpathy's pattern: instead of re-retrieving knowledge on every query, an LLM incrementally builds and maintains a persistent, interlinked markdown wiki — a compounding artifact that grows more useful with each session.

## The pattern

Described in [Karpathy's gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f), the core idea is that re-deriving knowledge from raw sources on every query is wasteful and lossy. Instead, a curator agent reads new source material (transcripts, articles, docs) and writes or updates wiki pages in a structured vault, cross-linking each new page to related existing ones. The result compounds: a page written from session 1 becomes a link target in session 10, accumulating context no single query could reconstruct.

[Obsidian](https://obsidian.md/) is the natural viewer — free, plain-git, phone-syncable, and its graph view makes the link topology visible in a way that reveals gaps and clusters. A neat framing: *"skills on steroids, but linked."*

## In the enterprise: a wiki mined from the code

<span style="color:red">By autumn 2026 the pattern had moved from personal vaults to company infrastructure. **A markdown wiki mined from the code beats Confluence for agents — if a cron keeps it in sync** ([[2026-10-13-ai-bank]], [[2026-09-02-ai-insurer]]):</span>

- <span style="color:red">**Mine once, with a top model.** One `.md` per microservice, extracted from all repos — log messages, inputs/outputs, API calls, data model — with Confluence reconciled against the code. One company spent **≈$10k of tokens** on the initial mining; another burned a week of top-model quota.</span>
- <span style="color:red">**A mirror of the truth, never the source.** When Confluence and code disagree, the code wins — half of a typical Confluence is obsolete ([[drift]]).</span>
- <span style="color:red">**Serve it over an MCP with two tools:** semantic search by keyword, then fetch the matching `.md`s **into a bucket where the agent greps them — not into context**. Agents grep markdown far faster than they dig through Confluence pages. Born for incident investigation, now used daily ([[firefighter-agent]], [[cross-repo-knowledge]]).</span>
- <span style="color:red">**Garden it weekly.** A skill in crontab, run in the cloud, ingests **only the diff since the last commit**, using Karpathy's LLM-wiki prompt and sub-agents on a weaker model. OpenAI's [harness engineering](https://openai.com/index/harness-engineering/) post calls this **doc gardening** ([[doc-gardener]], [[2026-09-25-ai-bank-migration]]).</span>
- <span style="color:red">**Central memory beats rediscovery:** ten agents rediscovering Jira and Confluence on every task is waste. One company reported a dramatic token drop and better quality once agents read the central memory instead of Confluence; a scheduled job refreshes it proactively from Confluence, code and diffs, not per task ([[2026-09-10-testing-ai-bank]], [[memory-layers]]).</span>
- <span style="color:red">A cheaper variant: mirror Confluence weekly into a wiki in git and put [[rag]] over the `.md`s. A knowledge-graph builder like [Graphify](https://github.com/Graphify-Labs/graphify) pays off on docs and PDFs, not on code ([[2026-09-04-ai-software-company]]).</span>

<span style="color:red">The same shape works per task: the [humanlayer research_codebase](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md) prompt launches parallel locator / analyzer / pattern-finder [[sub-agents]] that write hierarchical markdown reports, and later tasks reference the reports instead of re-reading the code ([[2026-05-13-ai-it-services]]). For legacy systems with no docs, everything mined (decompiled code, staging DB, tickets, [[elder-wisdom-mining|retirement interviews]]) lands in an Obsidian wiki first, then concisely back into Confluence ([[2026-09-02-ai-insurer]]).</span>

<span style="color:red">**Diagrams in the source material:** draw.io diagrams exported from Confluence are parseable (XML in the PNG metadata if you tick "Include a copy of my diagram"); Lucid exports are dead images. **One in three AI-generated diagrams was wrong** — don't trust them blindly ([[deterministic-diagrams]]).</span>

## Structure: one wiki per bounded context

<span style="color:red">Keep what *you* know apart from what your agent needs. Two clients' (or two domains') wikis should meet only through **a glossary and a context map** — merged, words lose meaning ([bounded context](https://martinfowler.com/bliki/BoundedContext.html)). An idea for memory evolution: **patterns repeat into principles, then values, and decay when unused** — an always-on assistant's "REM sleep" consolidation. Agent-memory products such as [Mnemosyne](https://mnemosyne.site/) are entering the space, and so are the frontier labs ([[2026-10-09-ai-jax-london]], [[self-improving-knowledge]]).</span>

## Relationship to other patterns

LLM Wiki **complements [[rag]]**, not replaces it: RAG retrieves from unstructured corpora; a wiki is pre-synthesized, explicitly linked, and human-curated at the source layer. For legacy codebases, the pattern is especially powerful — map the monolith **module-by-module**, one wiki page per subsystem, and an agent navigating that wiki avoids the [[context-window]] explosion of reading millions of lines cold. The open question is recall at scale: how does a new page get linked to something written 200 sessions ago? A pragmatic answer is [[vector-search]]-assisted link suggestion (semantic neighbors across the corpus), followed by agent confirmation — hybrid [[rag]] + graph.

**This repository is itself an instance of this pattern.** Source transcripts live in `raw/`; the LLM owns `wiki/`.

It is also the **graph-traversal** alternative to [[rag]]'s vector similarity: two complementary ways to navigate massive knowledge, where the wiki is pre-synthesized and cross-linked — and the cheap fix for RAG's token burn is to spin a summarization [[sub-agents|sub-agent]] over a human-written page before feeding it in. This very wiki now runs a **delta-highlighting** convention: each ingest red-marks the genuinely new concepts versus what was already there, and continuously strips session-specific cruft — durable knowledge stays, the chronology lives in [[log]].

## See also

- [[rag]]
- [[vector-search]]
- [[ralph-loop]]
- [[context-window]]
- [[code-graph]]
- [[sub-agents]]
- <span style="color:red">[[doc-gardener]]</span>
- <span style="color:red">[[cross-repo-knowledge]]</span>
- <span style="color:red">[[self-improving-knowledge]]</span>
- <span style="color:red">[[elder-wisdom-mining]]</span>
- <span style="color:red">[[drift]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
