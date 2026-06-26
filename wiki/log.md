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

## [2026-06-23] ingest | AI@Garmin (Day 1)

Fourth delta ingest. Source: [[2026-06-23-ai-garmin]] (Day 1 of a 2-day AI-Assisted / Agentic Coding workshop for mobile engineers). All AI@Kambi red marks (25 pages with spans/badges + 9 `new_in` pages) promoted to normal text first; the [[overview]] §8 red span was promoted too.

**Created 15 pages** (red 🆕 badge + `new_in: 2026-06-23`):

*Source:*
- [[2026-06-23-ai-garmin]] — source page.

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

**Stripped:** client name ("Garmin"), the internal company-layer product name ("Glide"), participant names (Mihai/Titus), and "today we…" / war-story phrasing generalized out of concept prose — provenance kept here and in `sources:` frontmatter. No session-artifact-only pages created. Note: the source-page prose still names the workshop for citation, per convention.

## [2026-06-24] ingest | Garmin workshop Day 2

Fifth delta ingest. Source: [[2026-06-23-ai-garmin]] (Day 2 added to the existing Day-1 file). All Day-1 Garmin red marks (15 new pages + spans in 29 pages + §9 overview movement) promoted to normal text first; zero red marks remain from prior ingests.

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
- [[multi-model-review]] — spawn fresh reviewers each round; surface coder–reviewer disagreements first; Day-1 Garmin span promoted.
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
- [[2026-06-23-ai-garmin]] — source page expanded to cover Day 2; title updated.
- [[overview]] — §9 Garmin Day-1 span promoted; new §10 "reviewless code pipeline" added and red-flagged.

**Navigation:** [[wiki/index|index]] gained 9 new pages across patterns and concepts sections; source list updated. [[overview]] gained §10.

**Red-flagged new today:** the 9 new pages above (🆕 badge) plus the §10 overview movement and every red `<span>` listed under the 18 updates. All older red marks were promoted to normal text first.

**Stripped:** "Garmin", "Bogdan" participant name removed from concept prose; "sarmale" lunch story, IoT-device anecdote, and other session-specific war stories not carried into concept pages. No session-artifact-only pages created. Provenance lives in `sources:` frontmatter and this log entry.

## [2026-06-25] ingest | Kambi Day 2

Sixth delta ingest. Source: [[2026-06-22-ai-kambi]] (Day 2 added to the existing Day-1 file; Day 2 = June 25, 2026, sections 6–10 and key points marked 2️⃣). All Garmin Day-2 red marks (9 new pages + spans in 25 pages + §10 overview movement) promoted to normal text first; zero red marks from prior ingests remain.

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
- [[sprint-design-quiz]] — knowledge-spreading / bus-factor framing from Kambi; +source.
- [[memory-layers]] — two-layer personal-style split; +source; +see-also.
- [[observability-mcp]] — latency metric for incremental pipelines; data-quality-validator companion; +source.
- [[2026-06-22-ai-kambi]] — source page title updated to Days 1 & 2; body expanded with Day-2 concept inventory.

**Navigation:** [[wiki/index|index]] gained 11 new pages across new "Patterns — agentic review pipeline" section, patterns cluster, security cluster, knowledge-engineering cluster, and new "Concepts — data quality & observability" section; source list updated. [[overview]] gained §11 ("Frameworks, the full review pipeline, and containment").

**Red-flagged new today:** the 11 new pages above (🆕 badge) plus the §11 overview movement and every red `<span>` listed under the 22 updates. All older red marks from Garmin Day 2 were promoted first.

**Stripped:** "Kambi", participant names (Luiza, Sorin, Cip, Alexandra, Stelian, Miha, Maria, Boris Cherny), and session-specific anecdotes generalized out of concept prose. The "Tina love story" (AI customer support) excluded as session colour, not a durable concept. Provenance lives in `sources:` frontmatter and this log entry.

## [2026-06-26] ingest | AI@agentic.how

Seventh delta ingest. Source: [[2026-06-26-ai-agentic-how]] (one-day remote workshop for ≈25 engineers, distilling the two-day flagship into a single intensive). All Kambi Day-2 red marks (11 new pages + spans in 22 pages + §11 overview movement) promoted to normal text first; zero red marks remain from prior ingests.

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
- [[cross-repo-knowledge]] — parent-folder session trick (open session in root of all clones); green-team-ai-central symlink as production alternative; VPS/CI limitation of the "clone-all" approach.
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
- [[overview]] — §11 Kambi Day-2 span promoted; new §12 "Anger-driven knowledge, self-improving wikis, and red-teaming" added and red-flagged.
- Source citations added to all touched pages.

**Navigation:** [[wiki/index|index]] gained 2 new security entries (skill-marketplace-security, prompt-injection-canary), 2 new pattern entries (grilling-in-reverse, bug-heatmap), 2 new knowledge-engineering entries (decision-context, self-improving-knowledge), and the new source entry. [[overview]] gained §12.

**Red-flagged new today:** the 7 new pages above (🆕 badge + red first-line) plus the §12 overview movement and every red `<span>` listed under the 22 updates.

**Stripped:** participant names (Edwin, "the researcher"), the "three to four parallel Claude Code instances" personal setup colour, the JIRA/Confluence workflow war stories, the Yann LeCun/HashiCorp factual corrections (recorded in the source raw file), the Tailscale+tmux side conversation, and the "spinning thinking verb" micro-demo. No session-artifact-only pages created. Provenance lives in `sources:` frontmatter and this log entry.
