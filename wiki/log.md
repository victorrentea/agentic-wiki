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

## [2026-06-12] ingest | AI@Playtika Day 2

Second delta ingest from [[2026-06-11-ai-playtika]]. All Day-1 red marks (44 pages) promoted to normal text first. Day-2 source: §§10–16, the Super-summary, and the Day-2 header block.

**Created 19 pages** (red 🆕 badge + `new_in: 2026-06-12`):

*MCP & tooling:*
- [[mcp-sandbox-inheritance]] — spawned (stdio) MCP inherits agent sandbox; remote MCPs do not.
- [[dynamic-tool-discovery]] — `tools/list` at connect; pitfall of too many tools; bridge for unknown CLIs.
- [[cli-vs-mcp-tradeoff]] — CLI ≈0 tokens if in training data vs MCP ≈300–600 eager; when each wins.
- [[browser-mcp]] — Playwright MCP + accessibility tree (structure > screenshots); Lighthouse integration.
- [[observability-mcp]] — Grafana / DB / log MCPs; anomaly detection; PII risk; volumetry pattern.
- [[agent-auth]] — service-account API key + on-behalf-of bearer token; out-of-band identity.
- [[headless-claude]] — `claude -p` + `--resume`; reuse CLI as harness instead of building your own.

*Review workflow:*
- [[cloud-review-workflow]] — "no laptop" pipeline: ticket → Three Amigos → PR bot → SDD → cloud review.
- [[three-amigos]] — Business + Developer + Tester (ninja) session producing acceptance tests.
- [[vibe-fixer]] — receives ≈10k vibe-coded lines; applies engineering judgement (keep/reuse/discard).

*Security:*
- [[jailbreak-ladder]] — RLHF → abliteration → regex → judge LLM → context accumulation; bypass for each.
- [[lethal-trifecta]] — Simon Willison's three-way (private data + untrusted content + external comms).
- [[openclaw]] — always-on PDA with payments; canonical lethal-trifecta product.
- [[geo-steganography]] — GEO invisible-text injection; cross-platform encoded instructions.
- [[agent-permissions]] — ask → auto-mode → YOLO spectrum; tripwires; why denying part of bash fails.
- [[os-sandbox]] — Seatbelt / seccomp+Landlock / bubblewrap; kernel-enforced file+network allow-list.
- [[docker-sandboxing]] — gold standard: mount-only-needed, egress allow-list, don't-mount-socket, burn-box.
- [[secret-zero]] — MCP-as-proxy + kernel-key-swap broker; agent never holds long-lived credential.
- [[production-safety]] — Railway (3 months gone) + Kiro (13h outage) lessons; kill-all-but-one autopsy.

**Updated 14 existing pages** (red `<span>` on new sentences):
- [[model-context-protocol]] — remote vs local MCP sections; dynamic discovery; agent auth; +source.
- [[mcp-transport]] — local stdio section and security-relevant remote-vs-local distinction.
- [[elicitation]] — "absence of elicitation causes disasters" (Railway incident cross-link).
- [[prompt-injection]] — indirect injection via fetched content; quarantine pattern; +cross-links.
- [[dual-leg-rule]] — "strengthened framing from Day 2"; lethal-trifecta canonical name; +cross-links; +source.
- [[guardrails]] — jailbreak-ladder cross-link; "no layer is sufficient" framing; +lethal-trifecta link.
- [[multi-model-review]] — cloud embedding; "weakest link" framing; +cloud-review-workflow link.
- [[spec-driven-development]] — cloud workflow integration; bot-built SDD; tasks.md for cheaper model.
- [[supply-chain-attack]] — Docker socket as supply-chain amplifier; +docker-sandboxing link.
- [[vibe-coding]] — vibe-fixer role and cloud workflow connection; +links; updated.
- [[profiling-agents]] — "kill all but one" corollary; +observability-mcp and production-safety links; +source.
- [[ralph-loop]] — `/loop` as tamer cousin; token budget; Haiku guard warning.

**Navigation:** [[wiki/index|index]] gained 3 new sections (MCP additions, new security cluster, new patterns); [[overview]] gained movement §7 (security + containment + cloud workflow). All red marks are today's Day-2 delta only.

**Stripped:** all "today we…" phrasing, client names, participant anecdotes, and transient pricing/version trivia removed from new pages. No session-artifact-only pages created.

**Addendum (same day):** [[secret-zero]] updated to name **Bromure** (the microVM locker that ships fake credential stubs swapped at egress) — the concrete product instantiating the kernel-key-swap broker pattern. [[os-sandbox]] recommendation ladder updated to include the Bromure tier.

## [2026-06-22] ingest | AI@Kambi (Day 1)

Third delta ingest. Source: [[2026-06-22-ai-kambi]] (Day 1 of a 2-day AI-Assisted / Agentic Coding workshop). All Day-2 red marks (30 pages) promoted to normal text first.

**Created 9 pages** (red 🆕 badge + `new_in: 2026-06-22`):

- [[2026-06-22-ai-kambi]] — source page.
- [[ci-green-loop]] — post-push hook vs `/loop fix ci`; two variants with different context postures.
- [[reward-hacking]] — agent cheating (assert true, deleting tests); defenses must be out-of-band.
- [[firefighter-agent]] — on-call incident-recovery agent gathering intel before human connects.
- [[draft-only-email]] — OAuth scope discipline: `compose` not `send`; structural not model enforcement.
- [[headroom]] — evolved CLI-output compression proxy, sibling of [[rtk]].
- [[agents-md]] — cross-harness project memory standard; symlink AGENTS.md → CLAUDE.md.
- [[pg-trgm]] — Postgres trigram extension for substring search; GIN/GiST indexes.
- [[keyset-pagination]] — cursor-based pagination; OFFSET breaks at 100K+ rows; tie-breaker required.

**Updated 18 existing pages** (new sentences/paragraphs red-`<span>`-wrapped):
- [[harness]] — harness ≠ model definition sharpened; LLM-as-weights framing; AGENTS.md cross-link.
- [[claude-md]] — three moves to shrink a bloated file; ≈100-line rule; per-folder split; skill extraction; AGENTS.md link.
- [[agent-skill]] — skill activation is model-strength-dependent; strong models wait, weak local models trash context.
- [[token-economy]] — most tokens in tool results, not messages; XML-noise framing; headroom added; "write yourself a script" pattern.
- [[dumb-zone]] — "context rot" alias; compaction-as-failure-mode elaborated; hallucinated test results example.
- [[prompt-caching]] — 8-minute test run trap; 1-hour TTL option via cache-control.
- [[handover]] — compaction-is-inferior elaborated; manual plan.md review before /clear.
- [[sub-agents]] — pure-router pattern; rate-limit outbound; prefer OpenAPI over hand-taught calls.
- [[grill-me]] — grill spawns sub-agent into codebase to self-answer; one-question-at-a-time mode.
- [[spec-driven-development]] — "never touch the files, only prompt" discipline; thinking-partner framing; keyset/pg-trgm cross-links.
- [[supply-chain-attack]] — typosquatting vector added.
- [[docker-sandboxing]] — `--dangerously-skip-permissions` only safe inside Docker.
- [[sycophancy]] — "don't over-praise" corollary; cornfield-parable framing.
- [[skill-erosion]] — quarter-sprint-hands-only; mob/pair programming as gold standard.
- [[plan-mode]] — task-size table (trivial/day/multi-day); Shift-Tab Shift-Tab keybinding noted.
- [[wispr-flow]] — alternatives (macOS Dictation, Claude mobile mic); dictation for grilling.
- [[multi-model-review]] — push review rules to reviewer stage; encode-rule-then-run-it-now; fresh sub-agent to verify rule.
- [[code-review]] — skip trivial diffs; stop reviewing unit tests; "option C" independent judgment.
- [[agentic-engineering]] — every failure = enrich plan or context; sprint retro question; production horizon (firefighter, all-day-in-CC).
- [[rtk]] — headroom cross-link added.
- [[overview]] — Day-2 red span removed (promoted); new §8 added for AI@Kambi.

**Navigation:** [[wiki/index|index]] gained 2 new sections (database & search; new tools headroom, pg-trgm), new patterns (ci-green-loop, firefighter-agent, reward-hacking), new security entry (draft-only-email), new knowledge-engineering entry (agents-md), and updated source list. [[overview]] gained §8.

**Stripped:** "Claudette" client name removed and generalized to "a Slack bot" in [[sub-agents]]. No session-artifact-only pages created. All provenance in `sources:` frontmatter and this log entry.
