# Log

Append-only, chronological record of wiki operations. Each entry: `## [YYYY-MM-DD] operation | Title`.
Tip: `grep "^## \[" wiki/log.md | tail -5` for the last 5 entries.

## [2026-06-10] ingest | Spring AI @ ITKonekt workshop

Seed ingest. Source: [[2026-06-10-spring-ai-itkonekt]] (full session summary, copied into `raw/`).

Created 33 concept pages spanning five clusters (see [[overview]]):
- **chat fundamentals** — [[chat-model]], [[chat-memory]], [[chat-history]], [[advisor]], [[context-window]], [[system-prompt]]
- **MCP & knowledge** — [[model-context-protocol]], [[mcp-transport]], [[elicitation]], [[sampling]], [[rag]], [[vector-search]], [[graph-database]]
- **security** — [[prompt-injection]], [[judge-llm]], [[guardrails]], [[tool-calling]], [[tool-context]], [[jwt-identity]], [[supply-chain-attack]], [[dual-leg-rule]]
- **tools** — [[spring-ai]], [[ollama]], [[embabel]], [[code-graph]], [[context7]], [[pgvector]], [[claude-code-router]], [[grill-me]], [[llm-wiki]]
- **patterns** — [[ralph-loop]], [[token-economy]], [[profiling-agents]]

Created navigation: [[wiki/index|index]], [[overview]]. Lint passed: no broken wikilinks, no orphan pages, no markdown tilde hazards.

Note: the wiki itself instantiates the [[llm-wiki]] pattern it documents — fitting, since the pattern was discussed live during this very session.

## [2026-06-11] ingest | AI @ Playtika (day 1)

First *delta* ingest. Source: [[2026-06-11-ai-playtika]] (day-1 summary; day 2 — security, Docker sandboxing, the multi-agent review workflow, the OpenAPI→TypeScript drift pipeline — is still pending). Today's brand-new concepts are red-flagged in each page (a red 🆕 badge on new pages, red `<span>` on new bullets in existing ones), with `new_in: 2026-06-11` on every new page.

**Created 29 pages** across new clusters:
- **context & token economy** — [[dumb-zone]], [[prompt-caching]], [[effort-setting]], [[harness]], [[rtk]], [[toon]]
- **reliability & human factors** — [[hallucination]], [[sycophancy]], [[drift]], [[vibe-coding]], [[agentic-engineering]], [[skill-erosion]], [[ai-addiction]], [[caveman]]
- **skills & knowledge engineering** — [[agent-skill]], [[progressive-disclosure]], [[claude-md]], [[tripwire]], [[cross-repo-knowledge]]
- **operating patterns** — [[plan-mode]], [[handover]], [[sub-agents]], [[spec-driven-development]], [[openspec]], [[acceptance-test-bdd]], [[multi-model-review]], [[field-reality-diagram]], [[worktree]]
- **tools** — [[wispr-flow]] (and [[rtk]], [[toon]], [[openspec]] above)

**Updated 12 existing pages** (merged genuinely new knowledge + added the source + red-marked deltas): [[token-economy]] (output ≈5× input, RTK/TOON, stateless-API + prompt-cache, effort knob), [[context-window]] (dumb-zone numbers, compaction = failure, `/context`), [[supply-chain-attack]] (S-BOM bomb, npm vs Maven, Log4Shell, `--ignore-scripts`), [[ralph-loop]] (bash `while`, Docker sandbox), [[grill-me]] (4-lines = war, technical vs business grill, handover ending), [[llm-wiki]] (live meta-demo + red-delta convention, graph-vs-vector), [[claude-code-router]] (on-prem DeepSeek sovereignty), [[system-prompt]] (turn-1 payload inventory), [[rag]] (approximate find, summarization sub-agent), [[code-graph]] (grep-for-reuse), [[context7]] (post-cutoff facts via WebFetch/Context7), [[prompt-injection]] (sycophancy cross-link).

**Navigation:** [[wiki/index|index]] regrouped into new categories; [[overview]] gained a sixth movement ("the agentic-engineering operating discipline").

**Standing-lint:** stripped client/participant names and "today we…" phrasing from prose as pages were touched (provenance lives here in the log and in `sources:`). No purely-session-artifact pages were created.

### Follow-up (2026-06-11) — delta extended: §9 multi-agent review & the tool gauntlet

Same delta, same red marks (untouched). Source [[2026-06-11-ai-playtika]] was extended with **§9 "Multi-agent review & the tool gauntlet"** (a team break-out, translated from Romanian) plus Super-summary bullets #38–43. Ingested the durable concepts (participant names stripped — provenance stays here):

**Created 2 pages** (both `new_in: 2026-06-11`, red 🆕 badge):
- [[code-review]] — the human's last gate: judgement not mechanics, **review sorted** (surface the lines reviewers disagreed on first, before your attention dies), "done includes I watched it work", the PR-size sweet spot (too big hides bugs / too small collides on merge), never approve what you don't understand.
- [[static-analysis-gauntlet]] — SonarQube (tuned, e.g. max-params 7→5) / CodeQL / Semgrep as **build-failing CI gates** (not advisory dashboards) so code self-repairs before a human reviews — "make the machine draw blood first".

**Updated 6 pages** (new sentences red-`<span>`-wrapped): [[multi-model-review]] (review orchestrator spawning ≈3 sub-agents per concern across Opus+GPT ⇒ ≈6 reviewers; review-sorted section; "done includes I watched it work" section), [[code-graph]] (reuse hunting must be *instructed explicitly* — grep business symbols + code-graph, else context-anxiety YOLO), [[rag]] (vector find is the wrong tool for code reuse — grep + code-graph instead), [[guardrails]] (the layered-defence idea extends to an agent's output *code*; +day-1 source), [[skill-erosion]] (front-load the analysis — ≈7 days of decisions into ≈1 hour; never approve what you don't understand), plus hub cross-links in [[agentic-engineering]], [[tripwire]], [[wiki/index|index]], [[overview]].
