# Log

Append-only, chronological record of wiki operations. Each entry: `## [YYYY-MM-DD] operation | Title`.
Tip: `grep "^## \[" wiki/log.md | tail -5` for the last 5 entries.

## [2026-06-10] ingest | Spring AI open workshop

Seed ingest. Source: [[2026-06-10-spring-ai-open-workshop]] (full session summary, copied into `raw/`).

Created 33 concept pages spanning five clusters (see [[overview]]):
- **chat fundamentals** — [[chat-model]], [[chat-memory]], [[chat-history]], [[advisor]], [[context-window]], [[system-prompt]]
- **MCP & knowledge** — [[model-context-protocol]], [[mcp-transport]], [[elicitation]], [[sampling]], [[rag]], [[vector-search]], [[graph-database]]
- **security** — [[prompt-injection]], [[judge-llm]], [[guardrails]], [[tool-calling]], [[tool-context]], [[jwt-identity]], [[supply-chain-attack]], [[dual-leg-rule]]
- **tools** — [[spring-ai]], [[ollama]], [[embabel]], [[code-graph]], [[context7]], [[pgvector]], [[claude-code-router]], [[grill-me]], [[llm-wiki]]
- **patterns** — [[ralph-loop]], [[token-economy]], [[profiling-agents]]

Created navigation: [[wiki/index|index]], [[overview]]. Lint passed: no broken wikilinks, no orphan pages, no markdown tilde hazards.

Note: the wiki itself instantiates the [[llm-wiki]] pattern it documents — fitting, since the pattern was discussed live during this very session.

## [2026-06-11] ingest | AI @ a mobile-gaming company (day 1)

First *delta* ingest. Source: [[2026-06-11-ai-mobile-gaming]] (day-1 summary; day 2 — security, Docker sandboxing, the multi-agent review workflow, the OpenAPI→TypeScript drift pipeline — is still pending). Today's brand-new concepts are red-flagged in each page (a red 🆕 badge on new pages, red `<span>` on new bullets in existing ones), with `new_in: 2026-06-11` on every new page.

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

Same delta, same red marks (untouched). Source [[2026-06-11-ai-mobile-gaming]] was extended with **§9 "Multi-agent review & the tool gauntlet"** (a team break-out, translated from Romanian) plus Super-summary bullets #38–43. Ingested the durable concepts (participant names stripped — provenance stays here):

**Created 2 pages** (both `new_in: 2026-06-11`, red 🆕 badge):
- [[code-review]] — the human's last gate: judgement not mechanics, **review sorted** (surface the lines reviewers disagreed on first, before your attention dies), "done includes I watched it work", the PR-size sweet spot (too big hides bugs / too small collides on merge), never approve what you don't understand.
- [[static-analysis-gauntlet]] — SonarQube (tuned, e.g. max-params 7→5) / CodeQL / Semgrep as **build-failing CI gates** (not advisory dashboards) so code self-repairs before a human reviews — "make the machine draw blood first".

**Updated 6 pages** (new sentences red-`<span>`-wrapped): [[multi-model-review]] (review orchestrator spawning ≈3 sub-agents per concern across Opus+GPT ⇒ ≈6 reviewers; review-sorted section; "done includes I watched it work" section), [[code-graph]] (reuse hunting must be *instructed explicitly* — grep business symbols + code-graph, else context-anxiety YOLO), [[rag]] (vector find is the wrong tool for code reuse — grep + code-graph instead), [[guardrails]] (the layered-defence idea extends to an agent's output *code*; +day-1 source), [[skill-erosion]] (front-load the analysis — ≈7 days of decisions into ≈1 hour; never approve what you don't understand), plus hub cross-links in [[agentic-engineering]], [[tripwire]], [[wiki/index|index]], [[overview]].

## [2026-06-12] ingest | AI @ a mobile-gaming company, Day 2

Second delta ingest from [[2026-06-11-ai-mobile-gaming]]. All Day-1 red marks (44 pages) promoted to normal text first. Day-2 source: §§10–16, the Super-summary, and the Day-2 header block.

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

## [2026-06-22] ingest | AI @ a sports-betting company (Day 1)

Third delta ingest. Source: [[2026-06-22-ai-sports-betting]] (Day 1 of a 2-day AI-Assisted / Agentic Coding workshop). All Day-2 red marks (30 pages) promoted to normal text first.

**Created 9 pages** (red 🆕 badge + `new_in: 2026-06-22`):

- [[2026-06-22-ai-sports-betting]] — source page.
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
- [[overview]] — Day-2 red span removed (promoted); new §8 added for the sports-betting workshop.

**Navigation:** [[wiki/index|index]] gained 2 new sections (database & search; new tools headroom, pg-trgm), new patterns (ci-green-loop, firefighter-agent, reward-hacking), new security entry (draft-only-email), new knowledge-engineering entry (agents-md), and updated source list. [[overview]] gained §8.

**Stripped:** a client bot name removed and generalized to "a Slack bot" in [[sub-agents]]. No session-artifact-only pages created. All provenance in `sources:` frontmatter and this log entry.

## [2026-06-23] ingest | AI @ a device maker (Day 1)

Fourth delta ingest. Source: [[2026-06-23-ai-device-maker]] (Day 1 of a 2-day AI-Assisted / Agentic Coding workshop for mobile engineers). All the sports-betting workshop red marks (25 pages with spans/badges + 9 `new_in` pages) promoted to normal text first; the [[overview]] §8 red span was promoted too.

**Created 15 pages** (red 🆕 badge + `new_in: 2026-06-23`):

*Source:*
- [[2026-06-23-ai-device-maker]] — source page.

*Models, harness & anatomy:*
- [[model-hierarchy]] — Opus/Sonnet/Haiku/GPT as experienced hires; match the tier to the task.
- [[knowledge-cutoff]] — past-training facts get invented; pull them in (Context7) rather than recall.

*Reliability & human factors:*
- [[chop-ai-waiter]] — CHOP / "AI waiter" anti-pattern: copy-pasting instead of giving the agent a feedback loop.
- [[cognitive-debt]] — vibe-coding collapse in 2–3 months; the fake-coverage trap; business-reviewed `.feature` files.

*Patterns:*
- [[feedback-loop]] — give every handed-off task a way to observe its own result; the core operating rule.
- [[archunit-drift-control]] — ArchUnit + reflected class diagram so a stale `.puml` fails the build.
- [[agentic-corporation]] — the issue tracker as a "blackboard"; the "dark factory" horizon.

*Knowledge engineering:*
- [[agents-md-discipline]] — curating AGENTS.md: anti-dumping, seed-of-heresy Socratic loop, sub-folder injection, retrospect-every-sprint.
- [[memory-layers]] — the four scopes: user / project / team / company.
- [[skills-governance]] — one skill-set per team (megalomania vs dichotomy).
- [[custom-agent]] — single-purpose specialist with own context, pinned model, restricted tools.

*Security:*
- [[data-governance]] — ZDR agreements decide which endpoints may see your code; on-prem for sovereignty.

*Tools:*
- [[figma-mcp]] — pull designs straight into the app if they're truly maintained in Figma.
- [[adb-automation]] — `adb`-driven mobile UI loop; needs a deterministic emulator in CI.

**Updated 26 existing pages** (new sentences/paragraphs red-`<span>`-wrapped, + source + cross-links):
- [[hallucination]] — the "three real causes" compression (cutoff / dumb zone / sycophancy); knowledge-cutoff link.
- [[context7]] — vetted-feed vs poisoned-web-fetch framing; live-install / post-cutoff answer.
- [[ralph-loop]] — fresh-agent-per-batch ("150 checkboxes"); RALF; cheap-model fit.
- [[grill-me]] — Kumquat / over-vs-under-specify; effort-only-vs-all-properties catch; adb-browse-the-emulator; blackboard.
- [[cli-vs-mcp-tradeoff]] — `gh` CLI over GitHub MCP (≈1,300 tokens); "wakes up dreaming about gh."
- [[vibe-coding]] — cognitive-debt + fake-coverage + `.feature` files.
- [[acceptance-test-bdd]] — business-reviewed `.feature` files; the "summarise what each test asserts" AGENTS.md rule.
- [[drift]] + [[field-reality-diagram]] — ArchUnit/reflected-diagram as the static-structure cousin.
- [[multi-model-review]] — Sonnet+GPT-parallel-then-Opus-aggregate; "tell GPT it was Claude's code" ≈20% tip.
- [[ollama]] — open vs frontier weights; DeepSeek HF page; on-prem = strongest data governance.
- [[caveman]] — "saving tokens on the wrong end / protect my brain stamina."
- [[harness]] — harness-matters-as-much-as-model; CLI > IDE plugin ranking.
- [[agents-md]] — `copilot-instructions.md` is dead; links to discipline + memory-layers.
- [[claude-md]] — cross-links to agents-md-discipline + memory-layers.
- [[model-context-protocol]] — integration "paradise" (Figma/adb/Jira/CI) + API-key-scope caution.
- [[browser-mcp]] — screenshot loop as feedback; adb as the mobile equivalent.
- [[context-window]] — "what the model sees each turn" inventory; reasoning-not-carried-forward; 400K/500K ceiling.
- [[token-economy]] — token-burn as the first adoption metric.
- [[prompt-injection]] — Reddit web-fetch risk vs Context7 vetted feed.
- [[agent-skill]] — description-as-trigger; `.agents/skills/<name>/SKILL.md` path; pre-push canonical example.
- [[dumb-zone]] — 65% is a rule of thumb, not a published number; "drops prod databases."
- [[agentic-engineering]] — the two slogans (servant-of-AI; feel-the-problem); feedback-loop + model-hierarchy links.
- [[skill-erosion]] — the isolation cost; feel-the-problem-class antidote.
- [[claude-code-router]] — on-prem open model as the strongest answer to data governance.
- [[agent-permissions]] — mind the API-key scopes you mint; `/yolo` only inside Docker.
- [[sycophancy]] + [[prompt-caching]] + [[wispr-flow]] — source citation; wispr-flow gains the draw-a-picture / absorb-self-corrections note.

**Navigation:** [[wiki/index|index]] gained the new tools (figma-mcp, adb-automation), security (data-governance), patterns (feedback-loop, chop-ai-waiter, archunit-drift-control, agentic-corporation), knowledge-engineering (agents-md-discipline, memory-layers, skills-governance, custom-agent), reliability (cognitive-debt, knowledge-cutoff), anatomy (model-hierarchy), and the updated source list. [[overview]] §8 heading de-dated + de-redded; new §9 ("Don't be the servant; engineer the knowledge") added and red-flagged.

**Red-flagged new today:** the 14 new concept/tool/pattern pages above (🆕 badge) plus the §9 overview movement and every red `<span>` listed under the 26 updates. All older red marks were promoted to normal text first; only this ingest's deltas are red.

**Stripped:** the client name, an internal company-layer product name, participant names, and "today we…" / war-story phrasing generalized out of concept prose — provenance kept here and in `sources:` frontmatter. No session-artifact-only pages created. Note: the source-page prose still names the workshop for citation, per convention.

## [2026-06-24] ingest | Device-maker workshop Day 2

Fifth delta ingest. Source: [[2026-06-23-ai-device-maker]] (Day 2 added to the existing Day-1 file). All Day-1 device-maker red marks (15 new pages + spans in 29 pages + §9 overview movement) promoted to normal text first; zero red marks remain from prior ingests.

**Created 9 pages** (red 🆕 badge + `new_in: 2026-06-24`):
- [[non-goals]] — explicit non-goals chapter in spec; *"agents build rockets when you asked for a bicycle."*
- [[hardware-mock]] — swap physical Bluetooth/sensor hardware for a loopback protobuf stub on CI.
- [[legacy-migration-stages]] — tests → dead-code removal → migrate; the safe recipe for scary legacy codebases.
- [[skills-benchmarking]] — run ≈10× with/without the skill, score quality + token burn; LLM-as-judge shortcut.
- [[light-dark-code]] — designate the exemplar module; point the agent at the "light" code, not the dark 80%.
- [[openapi-test-fixtures]] — backend OpenAPI examples → generated JSON fixtures + Kotlin DTOs; breaking the spec breaks the test.
- [[policeman-agent]] — orchestrator that blocks new parallel tasks if file-sets overlap an open PR.
- [[sprint-design-quiz]] — end-of-sprint agent asks 1–2 design questions per PR to verify comprehension.
- [[productionalize]] — hardening a vibe-coded prototype to production-grade; the emerging engineering specialty.

**Updated 18 existing pages** (new sentences/paragraphs red-`<span>`-wrapped):
- [[spec-driven-development]] — four-artifact / four-reviewer mapping; "spec alignment is precision work"; non-goals chapter; +source; +see-also.
- [[openspec]] — four artifacts and who reviews each; human-in-the-loop discipline (plan-mode before skill); +source; +see-also.
- [[plan-mode]] — three-tier framing concretized: plan mode is "too ephemeral" for multi-day; OpenSpec is the persistent answer; +source.
- [[three-amigos]] — Gherkin output as independent oracle; lock `.feature` files before implementation; +source.
- [[multi-model-review]] — spawn fresh reviewers each round; surface coder–reviewer disagreements first; Day-1 device-maker span promoted.
- [[static-analysis-gauntlet]] — custom Refaster/Semgrep rules; Snyk/Fortify; "unleash every tool because the agent does the fixing"; +source.
- [[code-graph]] — dead-code detection stage in legacy migration; +source; +see-also.
- [[prompt-caching]] — $20 cold vs $0.50 warm at the 5-min TTL; operational lesson; +source already present.
- [[mcp-transport]] — token cost of tool descriptions; wrong-tool-pick failure mode; elicitation for destructive ops; +source.
- [[prompt-injection]] — three agent-era flavors (poisoned web / steganography / user-controlled fields); OWASP LLM01; +source already present.
- [[supply-chain-attack]] — tj-actions incident; clone-scan-build mitigation; hash-lock baseline; +source.
- [[vibe-coding]] — "productionalize the vibe-coded crap" cross-link; +see-also.
- [[agent-skill]] — prove-the-skill-helps-with-statistics; cross-link to skills-benchmarking; already had source.
- [[cloud-review-workflow]] — policeman agent; selective second reviewer; +source.
- [[acceptance-test-bdd]] — independence constraint; QA must lock `.feature` files before implementation; already had source.
- [[vibe-fixer]] — productionalize cross-link; +source.
- [[2026-06-23-ai-device-maker]] — source page expanded to cover Day 2; title updated.
- [[overview]] — §9 device-maker Day-1 span promoted; new §10 "reviewless code pipeline" added and red-flagged.

**Navigation:** [[wiki/index|index]] gained 9 new pages across patterns and concepts sections; source list updated. [[overview]] gained §10.

**Red-flagged new today:** the 9 new pages above (🆕 badge) plus the §10 overview movement and every red `<span>` listed under the 18 updates. All older red marks were promoted to normal text first.

**Stripped:** the client name, participant names removed from concept prose; "sarmale" lunch story, IoT-device anecdote, and other session-specific war stories not carried into concept pages. No session-artifact-only pages created. Provenance lives in `sources:` frontmatter and this log entry.

## [2026-06-25] ingest | sports-betting workshop Day 2

Sixth delta ingest. Source: [[2026-06-22-ai-sports-betting]] (Day 2 added to the existing Day-1 file; Day 2 = June 25, 2026, sections 6–10 and key points marked 2️⃣). All device-maker Day-2 red marks (9 new pages + spans in 25 pages + §10 overview movement) promoted to normal text first; zero red marks from prior ingests remain.

**Created 11 pages** (red 🆕 badge + `new_in: 2026-06-25`):

*Review pipeline:*
- [[agentic-review-pipeline]] — full ordered pipeline: coder → multi-model mother-in-law reviewers → Sonar max → attempt-merge → proof-of-life → sorted review → committed verdict `.md`.
- [[mob-spec-review]] — no spec/architecture markdown reviewed alone; 2+ humans; prevents fatigue-LGTM.
- [[review-by-images]] — look at ER/class/dependency images first; pre-push hook renders SQL schema change as red-highlighted PlantUML ER diagram.
- [[codeowners-elders]] — protect CLAUDE.md, settings, schema with GitHub CODEOWNERS "elder" group; governance complement to tripwires.

*Engineering patterns:*
- [[optimize-then-measure-subset]] — baseline first, ≈5% subset prototype, confirm speedup twice, then scale; watch for shared-resource contention.
- [[orchestrator-catches-lying-subagent]] — prove claims on a clean checkout; bake as orchestrator policy; context-budget discipline.
- [[selenium-to-playwright-migration]] — rewrite only glue (never `.feature` files); mutation-testing verification; TypeScript prerequisite.
- [[data-quality-validator]] — programmatic validator (known input → assert output conforms); latency metric for incremental pipelines.

*Security:*
- [[dont-run-in-home-folder]] — set cwd to `≈~/workspace`; ☢️ status-bar guard; prompt-injected PDF threat vector.
- [[agent-lifecycle-hooks]] — PreToolUse/PostToolUse/Stop/Notification; PreToolUse as the real guard (not skippable like git hooks).

*Knowledge:*
- [[two-layer-memory]] — home `memory.md` (personal style) vs project `CLAUDE.md` (domain rules); define both explicitly.

**Updated 22 existing pages** (new sentences/paragraphs red-`<span>`-wrapped, + sources + cross-links):
- [[worktree]] — shared object store / per-branch isolation / pull-before-push; multi-agent burnout; +source.
- [[browser-mcp]] — Chrome extension vs Playwright for login-gated apps; extension reuses session; Playwright hits login wall; `storageState` as clean production answer; +source.
- [[legacy-migration-stages]] — Selenium→Playwright as concrete pattern; mutation-testing verification; +source.
- [[multi-model-review]] — full agentic review pipeline section (5 steps); committed `.md` verdict; +see-also.
- [[code-review]] — proof-of-life merge gate; committed `.md` verdict; +see-also.
- [[handover]] — HANDOVER/post-mortem template before `/clear`; orchestrator context-budget alarm; +see-also.
- [[archunit-drift-control]] — review-by-images and pre-push hook that renders red-highlighted ER diagram; +source.
- [[production-safety]] — sycophancy-spiral framing after disaster; don't-run-in-home-folder; +source.
- [[docker-sandboxing]] — kernel-space key proxy section; +source already present.
- [[elicitation]] — CLI vs MCP: CLI can't say no; MCP elicitation blocks at protocol layer; +source.
- [[sub-agents]] — orchestrator verification policy; context-budget discipline; +see-also.
- [[spec-driven-development]] — framework interchangeability section (GSD/Kiro/OpenSpec/Specky converge); never hand-edit the framework; +source already present.
- [[reward-hacking]] — orchestrator-verification complement; +see-also.
- [[ci-green-loop]] — orchestrator verification section; +see-also.
- [[agent-permissions]] — Shift+Tab full cycle section (default → accept-edits → plan → auto/YOLO); prompt-injection risk via MCP/skills; +source.
- [[jailbreak-ladder]] — token-budget escalation pattern (jailbreak vendor chatbot, then abuse its LLM tokens); +source.
- [[prompt-injection]] — home-folder exposure via cwd; +see-also.
- [[supply-chain-attack]] — `npm install` as security event framing; +source already present.
- [[sprint-design-quiz]] — knowledge-spreading / bus-factor framing; +source.
- [[memory-layers]] — two-layer personal-style split; +source; +see-also.
- [[observability-mcp]] — latency metric for incremental pipelines; data-quality-validator companion; +source.
- [[2026-06-22-ai-sports-betting]] — source page title updated to Days 1 & 2; body expanded with Day-2 concept inventory.

**Navigation:** [[wiki/index|index]] gained 11 new pages across new "Patterns — agentic review pipeline" section, patterns cluster, security cluster, knowledge-engineering cluster, and new "Concepts — data quality & observability" section; source list updated. [[overview]] gained §11 ("Frameworks, the full review pipeline, and containment").

**Red-flagged new today:** the 11 new pages above (🆕 badge) plus the §11 overview movement and every red `<span>` listed under the 22 updates. All older red marks from the device-maker Day 2 were promoted first.

**Stripped:** the client name, participant names, and session-specific anecdotes generalized out of concept prose. One customer-support love story excluded as session colour, not a durable concept. Provenance lives in `sources:` frontmatter and this log entry.

## [2026-06-26] ingest | AI@agentic.how

Seventh delta ingest. Source: [[2026-06-26-ai-agentic-how]] (one-day remote workshop for ≈25 engineers, distilling the two-day flagship into a single intensive). All sports-betting Day-2 red marks (11 new pages + spans in 22 pages + §11 overview movement) promoted to normal text first; zero red marks remain from prior ingests.

**Created 7 pages** (red 🆕 badge + `new_in: 2026-06-26`):

- [[2026-06-26-ai-agentic-how]] — source page.
- [[decision-context]] — one terse line per decision replaces verbose ADRs in agent memory; "an ADR for humans is one line for Opus."
- [[self-improving-knowledge]] — mining `~/.claude/*.jsonl` for frustration signals; a weekly background agent files PRs to CLAUDE.md/skills; banks running hundreds of such PRs/week.
- [[prompt-injection-canary]] — hidden AGENTS.md canary instruction to detect humans who rubber-stamp AI output without reading; the Mitchell Hashimoto pattern.
- [[bug-heatmap]] — cyclomatic complexity × fan-in/out × git commit count = data-driven priority queue for vibe-code cleanup.
- [[skill-marketplace-security]] — community skills are supply-chain surface, *"npm circa 2015"*; cap at ≈10, prefer Anthropic-published, security-scan the rest.
- [[grilling-in-reverse]] — AI quizzes *you* on what existing code does; one-question-at-a-time comprehension test as countermeasure to cognitive debt.

**Updated 22 existing pages** (new sentences/paragraphs red-`<span>`-wrapped, + sources + cross-links):
- [[agents-md-discipline]] — decision-context pattern, JSONL mining + git-history scanning as two self-improvement signals; +decision-context and self-improving-knowledge links.
- [[cross-repo-knowledge]] — parent-folder session trick (open session in root of all clones); a central AI-knowledge repo symlink as production alternative; VPS/CI limitation of the "clone-all" approach.
- [[docker-sandboxing]] — red-team-your-own-sandbox practice (ask contained model to escape; Opus refuses, Sonnet weaker); +skill-marketplace-security link.
- [[skill-erosion]] — grilling-in-reverse technique; calisthenics in unfamiliar language; "yeah okay Claude" / parallel-agent dumb zone warning.
- [[agent-skill]] — description ≈60-token budget; <20 skills/agent limit; skill marketplace security section with cross-link.
- [[harness]] — Copilot IDE plugin anti-recommendation ("never give your team this if you want them to value AI").
- [[elicitation]] — harness-level permission vs MCP elicitation distinction; elicitation as the *one* uncircumventable human-in-the-loop mechanism.
- [[multi-model-review]] — Codex CLI cross-model adversarial review from within Claude.
- [[code-review]] — +1,500 lines = declaration of war; auto-fix CI on push ("don't dare push without watching").
- [[model-hierarchy]] — local-model tiering: Opus orchestrator + company-server Qwen for cheap subtasks.
- [[cognitive-debt]] — "yeah okay Claude" rubber-stamp moment; parallel-agent dumb zone for humans.
- [[prompt-caching]] — don't leave agents idle above ≈200k tokens; subscription ≈10× cheaper than API key for interactive use.
- [[token-economy]] — format efficiency hierarchy: XML → JSON → YAML → TOML (worst to best).
- [[prompt-injection]] — added [[prompt-injection-canary]] cross-link.
- [[context7]] — "Wikipedia for agents" label made explicit.
- [[supply-chain-attack]] — skill marketplace as supply-chain surface; +skill-marketplace-security link.
- [[acceptance-test-bdd]] — agent-inaccessible test repo anti-cheat pattern (separate repo coding agent can't see).
- [[codeowners-elders]] — expanded minimum file set: CI workflows, OpenAPI contract, domain model, pom.xml deps, architecture diagrams.
- [[grill-me]] — research-only minions pattern ("do not suggest, do not perform — you just freaking read"); +grilling-in-reverse link.
- [[ollama]] — Opus+Qwen tiered architecture vision.
- [[overview]] — §11 sports-betting Day-2 span promoted; new §12 "Anger-driven knowledge, self-improving wikis, and red-teaming" added and red-flagged.
- Source citations added to all touched pages.

**Navigation:** [[wiki/index|index]] gained 2 new security entries (skill-marketplace-security, prompt-injection-canary), 2 new pattern entries (grilling-in-reverse, bug-heatmap), 2 new knowledge-engineering entries (decision-context, self-improving-knowledge), and the new source entry. [[overview]] gained §12.

**Red-flagged new today:** the 7 new pages above (🆕 badge + red first-line) plus the §12 overview movement and every red `<span>` listed under the 22 updates.

**Stripped:** participant names, the "three to four parallel Claude Code instances" personal setup colour, the JIRA/Confluence workflow war stories, the Yann LeCun/HashiCorp factual corrections (recorded in the source raw file), the Tailscale+tmux side conversation, and the "spinning thinking verb" micro-demo. No session-artifact-only pages created. Provenance lives in `sources:` frontmatter and this log entry.

## [2026-10-13] ingest | Six-month merge: 44 sessions (Apr → Oct 2026)

Eighth ingest, and the first batch merge. Sources: every AI / agentic-engineering session summary not yet in the wiki — 44 files, ≈1.9 MB: the spring workshops that predate the seed (2026-04-07 → 2026-06-08), the summer and autumn editions (2026-07-01 → 2026-10-13), two Devoxx Belgium talks and JAX London. The LLM-security day of a secure-coding course was included; its non-AI content was not.

Sources added (raw + source page): [[2026-04-07-ai-product-team]], [[2026-04-16-ai-safety-critical]], [[2026-04-27-ai-healthcare-software]], [[2026-05-04-ai-telecom]], [[2026-05-06-ai-ecommerce]], [[2026-05-11-ai-software-vendor]], [[2026-05-13-ai-it-services]], [[2026-05-18-secure-coding-payments]], [[2026-05-25-ai-government]], [[2026-05-27-ai-open-workshop]], [[2026-06-02-ai-open-workshop]], [[2026-06-05-ai-consultancy]], [[2026-06-08-ai-security-tools]], [[2026-07-01-ai-java-community]], [[2026-07-03-ai-bank]], [[2026-07-06-ai-medical-diagnostics]], [[2026-07-09-ai-live-casino]], [[2026-07-20-ai-telecom]], [[2026-07-22-ai-agentic-how]], [[2026-07-23-ai-software-product]], [[2026-07-30-ai-safety-critical]], [[2026-08-03-ai-telecom-ops-qa]], [[2026-08-06-ai-bank]], [[2026-08-10-ai-consultancy]], [[2026-08-12-ai-bank]], [[2026-08-13-ai-insurer]], [[2026-08-27-ai-it-services]], [[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]], [[2026-09-04-ai-software-company]], [[2026-09-07-ai-bank]], [[2026-09-10-testing-ai-bank]], [[2026-09-14-ai-industrial]], [[2026-09-16-ai-safety-critical]], [[2026-09-18-ai-agentic-how]], [[2026-09-24-ai-bank]], [[2026-09-25-ai-bank-migration]], [[2026-09-28-ai-gaming-tech]], [[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]], [[2026-10-06-gray-factory-devoxx]], [[2026-10-07-agentic-reconversion-devoxx]], [[2026-10-09-ai-jax-london]], [[2026-10-13-ai-bank]].

**Method.** All AI@agentic.how (2026-06-26) red marks were promoted to normal text first. Eight agents read the sources in parallel and wrote per-source deltas against the existing wiki (new facts, sharper versions, contradictions); the deltas were regrouped per target page; three fact-check agents settled 26 disputed claims from primary sources only (docs.claude.com / code.claude.com, vendor write-ups, the papers); ten writer agents merged each page's bundle — newest source wins, fact sheet beats every session, unverifiable claims hedged or dropped.

**Corrections to existing pages** (the merge's most important output): prompt-cache TTL history (API default always 5 min, 1 h opt-in; Claude Code subscription main thread 1 h, sub-agents/API 5 min — the "1 h until April" claim was wrong); effort defaults ("xhigh, never max" → default high, xhigh for planning/cross-boundary work); auto-compaction fires near the window limit, not at a fixed ≈90%; AGENTS.md is read natively by Claude Code since v2.1.277 only without a CLAUDE.md, bridged with `@AGENTS.md`, not a symlink; CLAUDE.md files concatenate, never override; `@`-imports are eager; thinking blocks are kept across turns on current models; "50% recall at 500k" removed (unsourced); [[caveman]] definition was inverted; RTK/Headroom savings replaced by measured figures; [[supply-chain-attack]] splits chalk/debug, Shai-Hulud and axios (≈3 h RAT, not a worm); skill-store statistics now Snyk's (36.8% / 13.4% / 76 malicious); the Kiro outage is disputed and dropped no database; the Railway deletion was a raw API call; the ETH Zürich AGENTS.md study now cited (arXiv 2602.11988); subscription-vs-API multipliers hedged (only ≈8× is measured).

**Created 56 pages** (red 🆕 badge + `new_in: 2026-10-13`), plus 44 source pages:

- [[ai-never-deletes]] — Agents add code beside old code instead of replacing it; dead methods, overloads, duplication — and how to counter it.
- [[gray-factory]] — Dimming human review responsibly: sampling rule (10 good PRs → half), preconditions, upgrade loop, factory culture.
- [[openapi-contract-sync]] — Derive OpenAPI from backend code, regenerate frontend types, fail the build/push on any contract drift.
- [[semantic-coverage]] — Map tests through a PR's changed code to the spec sentences they verify; executed-but-unasserted shown as partial.
- [[spec-review]] — How to review agent specs: read every word, sleep on it, per-file audience, red flags, size caps, three-amigos meeting.
- [[spec-afterlife]] — After shipping, each spec file drains to Jira, tests or one-line ADRs, then is deleted within ≈3 months.
- [[memory-poisoning]] — Prompt injection that persists in MEMORY.md/CLAUDE.md/config so it re-runs every session; attack shapes (incl. jqwik protestware) and defences.
- [[slopsquatting]] — Attackers pre-register package names LLMs hallucinate (≈5.2% commercial / ≈21.7% open models); how agents amplify it and the defences.
- [[telemetry-by-default]] — Ship zero-code OpenTelemetry + LGTM stack and RED dashboards from day one so humans and agents can observe the running system.
- [[vibe-heresy]] — Bad AI-written decisions that linger in comments, tests and markdown and resurrect in later prompts; mechanism and clean-up defences.
- [[gitleaks]] — Pre-commit secret scanner as a fence the agent can't argue past; its blind spots, agents routing around it, CI backstop and prompt-side twin
- [[mutation-testing]] — Break production code on purpose to prove tests verify behaviour; PIT/Stryker, manual variant, where to aim it, cost and triage
- [[system-2-fatigue]] — AI took over coding's restful System-1 stretches and left all-day deep thinking: fatigue-LGTM, compression ratios, brain budgets
- [[tool-poisoning]] — Prompt injection through MCP tool and skill descriptions loaded at start-up, rug-pull updates, and the pinning and vetting defences
- [[tracer-bullets]] — Make the agent deliver one thin vertical slice end-to-end and review it before widening, against its token-cheap layer-by-layer default
- [[ai-spiraling]] — Agent loops on a wrong path for 10–30 min; stop after ≈3 failed tries, ask "how did you get here?", /clear with the missing knowledge.
- [[chatops-agent]] — Agent as a Slack/Teams team member that executes authorised work end-to-end; three maturity levels, whose-tokens budget, gateways, Claude Tag.
- [[elder-wisdom-mining]] — Agent-run interviews and footprint mining of departing experts, distilled into team memory so undocumented decisions don't leave with them.
- [[kiro]] — AWS's spec-driven agentic IDE/CLI: EARS requirements, steering inclusion modes, skills-as-powers, hooks, and the disputed Dec-2025 outage report.
- [[record-replay-refactoring]] — Record production inputs/outputs, replay them against the AI-rewritten version and diff — a characterization-test oracle for legacy rewrites.
- [[ai-calisthenics]] — Ladder of uncomfortable self-imposed drills (problem not order, no hand edits, zero paste, dictation, 4 parallel agents) for agentic reconversion.
- [[answer-injection]] — Biasing an agent by smuggling the answer into the prompt; describe the symptom, ask neutral questions, steer persona not solution.
- [[enterprise-architect-agent]] — Background long-context agent cross-checking all services' contracts in a central repo to flag cross-service drift.
- [[internal-tool-cli-skill]] — Reverse-engineer an in-house tool (sources, decompile, Playwright capture), ship a token-efficient CLI plus SKILL.md, secrets in .env.
- [[openrewrite]] — AST refactoring engine: let the agent write and test recipes from one worked example instead of mass-editing code by prompt.
- [[quota-economics]] — Money side of agentic coding: subscription vs API vs Copilot credits, quota negotiation, estimating tasks in hours and dollars.
- [[authenticated-cli]] — Letting the agent call CLIs you're already logged into (az, gh, psql): no MCP, no token in context, your blast radius.
- [[black-box-testing]] — Test strategy for agent code: black-box component tests at the service seam, honeycomb, mocks lie, fixture trees, coverage targets.
- [[determinism-over-llm]] — If you can script it, script it: leave scripts, codemods and linters behind; reserve the LLM for judgement.
- [[document-extraction-agents]] — Extracting structured data from documents with one agent per type, per-field confidence, no required fields, two-model cross-check.
- [[enforcement-ladder]] — Escalation from asking the agent nicely to hooks, CI gates, sandboxes and credentials; why a regex deny is only a tripwire.
- [[goals-over-instructions]] — Write prompts and skills as objectives and constraints, not step-by-step procedures; let the stronger model choose the how.
- [[plugin-marketplace]] — A git repo of plugins (skills, hooks, MCPs) auto-pulled by the team's agents: per-team, feedback skill first, never strangers'.
- [[stacked-prs]] — Splitting big agent diffs into chained small PRs within review budget; native GitHub stacks, cascading fixes, dissent.
- [[assumptions-ledger]] — Coding agent logs assumptions with confidence plus rejected reviewer findings, so human review starts where the agent hesitated.
- [[doc-gardener]] — Scheduled agent that prunes and resyncs CLAUDE.md/AGENTS.md, skills and docs against the code to fight drift and bloat.
- [[example-driven-prompting]] — Show the agent concrete examples (few-shot cases, example files, exemplar modules) instead of prose; polish one slice, then "do the rest".
- [[human-review-dashboard]] — Script-generated, zero-token HTML review page per PR (diagrams, oasdiff, schema diff, demo video, requirement coverage); AI enters last.
- [[red-green-tdd]] — Agent proves every change with a test red before, green after; cures premature fixation; recipes for bugs, ATDD, fast inner loop.
- [[superpowers]] — obra's community skill pack: brainstorming, systematic debugging, TDD, sub-agent review; when to use vs OpenSpec, costs and caveats.
- [[agentic-reconversion]] — Retraining developers for agent-written code: career directions (product, architecture, quality, FDE) and the mindsets that now earn the salary.
- [[ai-use-cases-beyond-coding]] — High-ROI agent chores around the code: flaky tests, release notes, docs, ticket→PR, in-house agent gallery, rollout path.
- [[c4-model]] — C4 levels for agent-built systems: generate C2–C4 from code/traces (Structurizr, PlantUML), enforce with ArchUnit, filtered views for review.
- [[mcp-tenant-isolation]] — The MCP server injects the tenant filter into every agent SQL query (or DB RLS) — security by tooling, not prompting.
- [[observation-points]] — Own the solution, not the code: deterministically generated views (diagrams, contracts, code city) whose diffs the engineer watches.
- [[test-case-doc-automation]] — Turn legacy Word/Excel test cases into WebdriverIO/Playwright suites with phrase-to-assertion traceability, then delete the doc.
- [[agent-evals]] — Testing agents as suites: scripted checks first, rubric LLM judge for free text, measure the bill, beware eval awareness.
- [[deterministic-diagrams]] — Feed agents only script-generated or test-verified diagrams (OpenAPI, DDL, PlantUML, C4, hand-laid draw.io checked by tests).
- [[uncensored-models]] — Abliterated open-weight models: how refusal is removed, red-teaming your sandbox, open weights ≠ open source.
- [[advisor-vs-executor]] — Two ways to use AI: advisor (it proposes, you judge; roll the dice on several models) vs executor (it proves its work against a measurable signal).
- [[agent-loop]] — Every agent is the same loop: call model with tools, execute tool calls, append results, repeat; where state, cost and control live.
- [[ai-adoption]] — How engineering orgs actually adopt agentic development: bottom-up, laggers found by token data, skeptics converted by pairing, paved roads.
- [[approval-fatigue]] — Humans approve 93–97% of permission prompts and stop reading; sourced evidence, room heuristics flagged as such, and remedies (classifier, keys, sandbox).
- [[chatbot-latency-tuning]] — Fixing a slow LLM chatbot from traces: traceparent into tools/LLM, programmatic pre-loading, parallel tool calls, streaming for perceived latency.
- [[code-city]] — Victor's 3D codebase city (after Wettel's CodeCity): size/complexity/heat per class, used to diff a PR's structure and flag hotspots for agents.
- [[file-based-testing]] — Data-transformation tests as in/out file pairs run by one generic test, cheap for agents to mass-maintain and readable by business.

**Updated 111 pages** (new/changed sentences red-`<span>`-wrapped, sources + `updated:` bumped):

- [[cloud-review-workflow]] — Phone review loop, cloud vs local split, agents coordinating via commit messages, own email inbox per agent.
- [[custom-agent]] — Markdown persona not a rewritten loop; skill vs sub-agent vs custom agent table; tool restriction as security boundary, examples.
- [[geo-steganography]] — Grok/Morse $150k sourced; screenshot faint-text (Brave) and downscaling (Trail of Bits); markdown-image exfiltration.
- [[keyset-pagination]] — Pagination as security control, collation, ORM join traps, API contract (PagedModel nuance), URL state, common bugs.
- [[mcp-sandbox-inheritance]] — Stdio MCPs inherit the agent's OS user: run the harness as a restricted user.
- [[os-sandbox]] — Claude Code /sandbox details, credential masking, catches, why harness permissions aren't enough, microVM/sbx section.
- [[production-safety]] — PocketOS/Railway rewritten per facts (API call, data restored); Kiro case corrected/disputed; identity, DB, ops posture.
- [[skill-marketplace-security]] — Sourced numbers (Snyk 36.8%/13.4%/76; Liu et al. 26.1%/5.2%), scanners, auto-update as RCE, poisoning.
- [[skills-benchmarking]] — Statistics role, eval cost (≈1–2 h, ≈1M tokens), evals on .md changes, link to agent-evals.
- [[skills-governance]] — Three tiers (company tool wrappers / team conventions / personal), extension options, feedback skill, REVIEW.md, no mega-frameworks.
- [[spec-driven-development]] — Triage table, artifact audiences, framework menu and tailoring, spec-by-day/apply-by-night, legacy, executable specs; split out two pages.
- [[static-analysis-gauntlet]] — More scanners, three-layer hooks/CI table, AI-code GIGO, recurring bug → pipeline rule, attack your own guardrails.
- [[browser-mcp]] — three-tool comparison table, a11y-tree limits and image-injection angle, explore-then-codify workflows, Playwright test agents, identity/profile risks, QA habits.
- [[claude-code-router]] — "open-weights insurance policy" framing: keep the harness, swap the model (Kimi in Claude Code, Kimi K3, OpenCode Go).
- [[elicitation]] — current spec limits, optional client capability, destructiveHint is only a hint; Railway corrected to raw API call (data restored); hook/permission bypasses per docs.
- [[firefighter-agent]] — field data, Sherlock vs Firefighter roles, guardrails coded in tools, numeric bounds, state-machine "chess player", 500k kill rule, log injection, team bot.
- [[legacy-migration-stages]] — stage 0 (pay debt, finish migrations), ways to build the test net, requirement mining, move-don't-rewrite, freeze layer, OpenRewrite, run-both equivalence, strangler.
- [[light-dark-code]] — GIGO (SQL injection by imitation), reformat once, finish half-done migrations, greenfield spike/proud repo, front-end and test exemplars, naming as leverage.
- [[mcp-transport]] — stdio vs Streamable HTTP as "who starts the server", HTTP+SSE legacy, stateless 2026-07-28 spec revision, harness-started local MCPs, CLI wrappers for bloated APIs.
- [[model-hierarchy]] — Fable tier, GPT Sol/Terra/Luna map, task-to-tier rules, reviewer outranks author, plan/execute debate resolved, auto-routing pitfalls, open-weights reality, verified prices.
- [[prompt-injection]] — "agents can't tell task from content", obfuscation, WebFetch-not-curl, image vectors per fact sheet, logs as top vector, context entry-point inventory, exfiltration channels, mitigations.
- [[rag]] — RAG poisoning by a rogue insider's Confluence pages; corpus as persistent untrusted input.
- [[reward-hacking]] — secret copied past gitleaks, BrowseComp eval awareness (Opus 4.6, 2/1,266), agentic misalignment, phrase intent not metric, guard-one-level-up, review red flags.
- [[sprint-design-quiz]] — quiz generated from merged PRs' design.md; two teams' top practice; link to grill-me; client name removed from prose.
- [[claude-md]] — load order and tiers (concatenation, lazy sub-folders), ETH-study numbers corrected, what earns a place, GIGO exception, size range 60–200, /doctor, diff-based refresh
- [[draft-only-email]] — connector OAuth scopes vs injected emails, shopping agent that cannot log in or press Buy, "don't paste the AI"
- [[grill-me]] — classic prompt and upstream rename, plan-mode vs grill yield, interview rules, decision tree and when to stop, warm-up, never alone, sources, business grill, QA.md
- [[hallucination]] — extra triggers, "context, not prompt" plus reality-checking tools, proof links and citation grounding, bixonimania training-data poisoning, glossary and marker names
- [[jwt-identity]] — authorization in the tool rather than the prompt, JWT-isolated two-phase conversation, cache-key gotcha, insider logic-bomb case
- [[observability-mcp]] — trace navigation, safe DB access (read-only user, replica, rate limit), schema comments, never feed raw logs ($5–10k/day), PII hashing caveat, alarms at birth
- [[ollama]] — open-weight vs open-source, GPU economics, autumn-2026 open-model table (DeepSeek/GLM/Kimi), Vast.ai, hedged price-decay heuristic
- [[policeman-agent]] — plan-stage gate variant; same gate seen in the reference workflow many companies converge on
- [[profiling-agents]] — heap-dump-without-source data points, CI 42→20 min, SQL plan tuning beating DBAs, "the nerd is dead" feedback-loop framing
- [[progressive-disclosure]] — @imports are eager (not progressive), lazy sub-folder loading per docs, path-scoped rules and Copilot applyTo, push-down ladder, loading canary
- [[vibe-fixer]] — devibing moves, the 10,000-line business PR options and autumn consensus, business shift-left levels, prototype-and-polish-live
- [[agent-skill]] — description limits per docs (1,024/1,536 chars, no minimum), skill-count mechanisms, rules vs skills vs commands, goals-not-steps, scripts, Snyk stats.
- [[ai-addiction]] — agency depletion, "AI psychosis", day-shape compression, budgets (2 agents/1 deep, 3–4 h/day), ride-the-agent; cache TTL fixed.
- [[cognitive-debt]] — collapse at 4th–5th increment, 87%-coverage-zero-asserts story, brain-burn compression, Hawthorne/prototype-lab/AI-free-week countermeasures.
- [[context-window]] — measured "hi" overhead, tool output ≈90%+ of context, thinking-block and compaction facts corrected, 200k vs 1M, cache costs, prices.
- [[effort-setting]] — "xhigh for everything" replaced by effort-by-phase table; documented levels/defaults (Opus 5.5 medium); max discouraged not banned.
- [[field-reality-diagram]] — capture mechanisms (OTel agent, @WithSpan, browser-started traces), N+1/race/cost catches, practices, C2 aggregation validating draw.io.
- [[judge-llm]] — harness content filters as judges, layered public-chatbot stack with RLS as the real boundary, LLM as aggregator/explainer.
- [[llm-wiki]] — enterprise code-mined wikis (≈$10k mining, weekly diff gardening, MCP + grep, code wins over Confluence), one wiki per bounded context.
- [[ralph-loop]] — tasks.md as resumable ticket, Stop-hook enforcement, spec-the-day/code-the-night recipes, autumn 2026 shift to Opus orchestrators.
- [[skill-erosion]] — RCT/METR evidence, never-approve-the-unknown tactics, learning output styles, brain budgets, what knowledge survives, AI-era interviews.
- [[worktree]] — concrete line-offset race, ≈5% contract drift after merge, port collisions: Compose network per copy or two clones beat five worktrees.
- [[codeowners-elders]] — Branch-protection caveat, guardrail files and hooks under CODEOWNERS, six-eyes, two tiers, GUARDRAILS.md, libraries-not-versions, layered enforcement.
- [[context7]] — Interview-question framing, "only tunnel you need" in offline banks, hedged report that it may have gone paid.
- [[decision-context]] — Lifecycle: ≈1 PR in 20 adds a line, product decisions to tracker, specs expire after ≈3 months into choice/why/rejected table.
- [[headroom]] — Canonical repo, wider scope; PointFive (not JetBrains) measured +48% billed cost; invasive; marketplace rollout hook.
- [[jailbreak-ladder]] — Sixth rung (computed adversarial suffixes/perturbations), sourced emoji/poetry/many-shot attacks, model-swap evasion, Gandalf drills, JWT-scoped tools.
- [[memory-layers]] — Fixed "most specific wins" (concatenated, not overridden), official Claude Code scopes, product layer, top-down/bottom-up flows, user-memory trap.
- [[openspec]] — Install/portability, new+continue over propose, tasks.md as ticket, archive as living spec; corrected postinstall claim per npm metadata.
- [[prompt-caching]] — Rewrote TTL (1h subscription main thread, 5m API/sub-agents), invalidation rules, current-price cost cliff, warm-compaction habits, corrected history.
- [[secret-zero]] — Env vars and mcp.json as leak paths, REST-on-host broker, live fake-key proxy demo, "everything except the last step", saved connections.
- [[token-economy]] — Restructured: baseline-before-hi numbers, tool results dominate, output/thinking cost, model/effort choice, measuring; fixed RTK, thinking, effort, prices.
- [[tripwire]] — Guardrail vs tripwire, script objective rules in hooks/CI, don't mention the enemy, hooksPath/Husky activation, check ordering.
- [[acceptance-test-bdd]] — BDD's failed dream (business validates, not writes), scenario rules, glue/data cheats, holdout tests (StrongDM per F25), agentified QA, semantic coverage; strategy split out.
- [[agent-auth]] — non-human identity per agent, RFC 8693 on-behalf-of tokens, anonymous→authenticated chatbot, CLI-login blast radius, prod data agent.
- [[agent-lifecycle-hooks]] — full hook catalogue with uses, skill-extension via Skill matcher, hook-writing rules, deny/pattern limits corrected per F9.
- [[agentic-review-pipeline]] — converged 8-reviewer pipeline, reconciled gate ordering, orchestrator roles, start-small cost advice, human-step tactics.
- [[ci-green-loop]] — gh run watch mechanics, CI CLIs, local vs remote CI reconciled, cache-TTL cost trap per F1, weaker models cheat more.
- [[cross-repo-knowledge]] — what goes in the central repo, distribution mechanisms, rule promotion, @import is eager (corrected), read-only security.
- [[feedback-loop]] — "you shot blind", build-the-instrument-first, the reproduce→failing e2e→fix→video bug loop, UI loops, loop speed-ups.
- [[lethal-trifecta]] — product examples by leg, code-execution variant, why filtering can't fix injection, draft-only and anonymous/authenticated splits.
- [[non-goals]] — live over-delivery examples, the two common patterns, concrete non-goal lists, UX as unofficial contract, "out of scope" as career skill.
- [[sampling]] — Haiku-summarises-Jira use case; WebFetch is only an analogy, and Claude Code docs don't list sampling support.
- [[agentic-corporation]] — StrongDM named (fact F25), hidden acceptance tests, review-decay path to dark/grey factory, ticket-board-as-IDE, team-shape changes.
- [[agents-md]] — Claude Code reads AGENTS.md natively since v2.1.277 only without CLAUDE.md; `@AGENTS.md` replaces symlink; Copilot file nuance; Ferrari test.
- [[bug-heatmap]] — Extended signal set (cognitive complexity, instability, bug-fix commits), review targeting, pre-merge PR risk classifier, Code City.
- [[caveman]] — Definition fixed (compresses output, fact F22); JetBrains 8.5% vs 65% claim; "be brief" captures most of it.
- [[code-review]] — Defences before the human, sorting keys, 45-min budget, pairing as review, gradual trust (20 impeccable PRs), fix-the-markdown loop.
- [[docker-sandboxing]] — Containment ladder, docker-in-docker problem, Docker Sandboxes microVM on macOS via `sbx` (fact F16), fake credentials, hedged escape stories.
- [[harness]] — Autumn harness ranking, Copilot family/IDE modes/ghost text, billing (dated), don't build your own, harness as security surface; symlink advice fixed.
- [[headless-claude]] — Jira2PR via Claude GitHub App, "claude -p is already a chatbot harness", whose key (Max API credits, fact F23).
- [[plan-mode]] — Five-rung weapon ladder replaces three-tier table, prompt-quality ladder, getting a good plan, cache TTL corrected (fact F1).
- [[three-amigos]] — Each amigo reviews their own spec markdown; meeting mechanics with agents (prep, hold back, 35–40 min, transcribe); escalating business inputs.
- [[agents-md-discipline]] — When to add a line, phrasing, volumetry, harness-dependent loading (Claude Code v2.1.277 AGENTS.md, .claude/rules), shrinking order, doc gardening.
- [[cli-vs-mcp-tradeoff]] — Autumn rule (CLI+skill locally, MCP for cloud/auth), output-format tokens, scripted-skill third way, DB CLIs, tool-messup failure mode.
- [[code-graph]] — Precision ≠ token savings (measured +20–30% vs grep); LSP/IDE/Serena/CodeGraphContext alternatives; branches-per-flow trick.
- [[drift]] — Agents trust markdown over code; pre-push regeneration hook, single-source generators, nightly doc gardener.
- [[guardrails]] — Guard outside the model, off-the-shelf judges, authorize at the tool (RLS), semantic-sense filter, new jailbreak rungs, Gandalf.
- [[handover]] — Timing (≈50% / 100–150k), thinking-block fact corrected, chaining tools/people via markdown, resume staleness, rewind, /loop bookkeeping.
- [[model-context-protocol]] — Why MCP (elicitation, sampling, auth), stdio per-client cost, safe installation and secrets, MCP gateways, server design rules.
- [[sub-agents]] — Context firewall numbers, inheritance rules, cost and fork mode (cache, TTL), archetypes, scoping, adversarial sub-agents.
- [[tool-calling]] — Tool descriptions say when NOT to use them; state-dependent tool exposure.
- [[wispr-flow]] — Throughput numbers, language choice (hedged), what to dictate, lossy-voice caveats, free native alternatives, Victor's stack.
- [[agentic-engineering]] — Run records reframed as vanity metric; new sections on prompting by pain, habits, DevEx ritual, ROI, org rollout, changing roles.
- [[data-governance]] — Shadow AI/personal subscriptions, commercial vs consumer training terms, Schrems II correction, contractual risks, dictation/search egress, in-house bots.
- [[data-quality-validator]] — Oracle independence: verify against ticket formulas, not code; one-record-first workflow.
- [[dont-run-in-home-folder]] — Downloads as most dangerous folder, one-click trust prompt, cookies/Kerberos, --add-dir, macOS privacy-prompt trick.
- [[dumb-zone]] — Threshold table by window, compaction trigger per docs (≈967K/200K), recall-at-500k myth removed, symptoms, compaction-as-smell, SessionStart hook.
- [[knowledge-cutoff]] — Cited cutoffs, agent prefers memory so force online research with quotes, "Stack Overflow apocalypse".
- [[mob-spec-review]] — Calibration (≈3 h × 5 devs), Hawthorne effect, voices per paragraph, OCD++ quota, no end-of-day review, learning time.
- [[openapi-test-fixtures]] — Contract testing at scale: Pact and bi-directional contract testing from published OpenAPI.
- [[productionalize]] — "Vibe code fixer" job postings already exist.
- [[review-by-images]] — Script-generated zero-token views: oasdiff, migration ERD, neighbour-only diffs, narrated video, visual regression, OTel traces, hand-laid draw.io.
- [[supply-chain-attack]] — Untangled chalk/debug, Shai-Hulud, Axios (3 h, RAT, no worm); OpenSpec/bin fix; MCP npx@latest; hardening checklist; protestware; skill stats.
- [[sycophancy]] — RLHF origin, "challenge my prompts" memory line, "make no mistakes" anti-pattern with studies, steering and recovery techniques.
- [[system-prompt]] — Turn-1 inventory adds dynamic harness context and auto-memory (200 lines/25KB); ≈40k baseline, target under 25k.
- [[toon]] — JSON→TOON post-tool hook for chatty MCPs; diverging savings claims; RTK measured-cost precedent.
- [[agent-permissions]] — Auto mode as default (89% vs 13.6% catch rate), documented compound-command/deny semantics, hooks, guard-the-guards config, key scoping, three real sandboxes, Copilot naming.
- [[archunit-drift-control]] — PlantUmlArchCondition diagram-as-ruleset, code-vs-diagram reconciliation via CODEOWNERS, four escapes, draw.io PNG XML tests, contract chain, hook gotcha.
- [[chop-ai-waiter]] — Zero-paste maturity target, four ways to connect an agent to systems, Playwright-as-CLI, DB access ladder, terminal hygiene, persona-agent trap.
- [[figma-mcp]] — "AI is bad at design" revised: Stitch/Figma Make for exploration, Figma+MCP for design systems, design-change propagation, human live UX polish.
- [[multi-model-review]] — Dice math and independence conditions, specialist reviewers grown on evidence, built-in commands per current docs (REVIEW.md scope), one round/report-only, cost data.
- [[pg-trgm]] — Index-killer red flags in AI-written search (UPPER, OR chains, leading wildcard, JOIN+DISTINCT) and diacritic-insensitive options to record in AGENTS.md.
- [[rtk]] — Rewritten around measured cost: JetBrains +7.6% at low effort, PointFive/Quesma break-even or worse; prefer native terse flags; fixed input/output mix-up.
- [[self-improving-knowledge]] — Feedback skill/SessionEnd hook, /insights retros, conversation retention (cleanupPeriodDays), PR-comment mining, "which markdown let it through", doc gardening.
- [[two-layer-memory]] — Home memory corrected to ~/.claude/CLAUDE.md; auto memory (200 lines/25KB, per git repo) as hidden layer; project knowledge belongs in git; output styles.
- [[vibe-coding]] — Definition moved to "shipping a solution you don't understand"; blast-radius rules, business vibing, vibe addiction, context-anxiety hedged, StrongDM dark factory.

**Navigation:** [[wiki/index|index]] gained every new page in its section (🆕) and refreshed 15 stale one-liners; [[overview]] gained §13 ("Six months of workshops, merged").

**Stripped:** client and participant names, client-internal URLs and client-named petclinic branches (links repointed to `main` after checking the path exists, else dropped). Client names were later removed from sources too — see the next entry.

## [2026-10-14] lint | Anonymise every source — no client or participant names anywhere

The repo and site are public, so client identities came out of the sources as well, not only the concept prose.

- **Slugs renamed by sector** (49 sources): e.g. `2026-09-07-ai-bank`, `2026-06-22-ai-sports-betting`, `2026-05-18-secure-coding-payments`; every citation across the wiki rewritten. Public events keep their names (agentic.how, Devoxx Belgium, JAX London).
- **Raw summaries anonymised in place** (the one sanctioned edit to `raw/`): company names, participant and client-employee names (→ roles), client-internal code names, URLs, wiki folders and Drive links; petclinic links on client-named branches repointed to `main` where the path exists there, otherwise unlinked.
- **Source-page titles and tags** name the sector ("AI-Assisted Coding @ a bank"); [[wiki/index|index]] source list regenerated from them; older log headings generalised.
- **Schema:** `CLAUDE.md` gained a Privacy section so future ingests anonymise before writing `raw/`.
- **Git history** squashed to a single commit so earlier versions with names are no longer reachable from the repo.
