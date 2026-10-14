---
title: Token Economy
category: pattern
tags: [context-window, tool-calling, cost-optimization, agent-workflow, mcp, output-tokens]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-10
updated: 2026-10-13
---

The discipline of treating context tokens as a finite, expensive resource — deliberately choosing tools, transport, and restart strategies to avoid burning the [[context-window]] on overhead rather than reasoning.

<span style="color:red">The money side — subscriptions vs API keys, Copilot credits, estimating tasks in dollars, negotiating quota — has its own page: [[quota-economics]]. The cache mechanics are in [[prompt-caching]].</span>

## Why it matters

Tool definitions are injected into context on every turn. A developer who installs 10+ MCPs can easily accumulate 100+ tool schemas in a fresh conversation before writing a single line of code. Claude Code's `/context` command makes this visible. The practical fix hierarchy:

1. **Prefer CLI over MCP** — a shell command costs near-zero tokens; the equivalent MCP tool drags in its full JSON schema. CLI is often **10x cheaper** in context cost. <span style="color:red">Measured: a CLI wrapped in a skill costs ≈105 tokens until used ([example](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/jenkins-cli/jenkins.py)), while a Grafana MCP carries 65–81 tools and >10,000 tokens on every turn ([[2026-09-30-ai-enterprise]], [[2026-08-10-ai-consultancy]]). *"The MCP sits in context always; the CLI only when called"* ([[2026-09-14-ai-industrial]]; see [[cli-vs-mcp-tradeoff]]).</span>
2. **Tool-search-tool** — Claude Code's built-in mechanism that loads only tool *names* up front and fetches the full schema on demand. <span style="color:red">An earlier estimate here was ≈4k tokens saved per session; in MCP-heavy setups it saves tens of thousands — Anthropic reports tool definitions reaching 134k tokens in one setup and the [Tool Search Tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool) cutting them by 85% ([advanced tool use](https://www.anthropic.com/engineering/advanced-tool-use); [[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]]) — at the cost of a slightly higher chance the agent picks the wrong tool.</span> See [[dynamic-tool-discovery]].
3. **Watch the first two thoughts + first tool call.** If the agent reaches for a heavy MCP you didn't intend (e.g. opening a browser when you wanted a web fetch), the wrong-tool spiral compounds rapidly. The correct response is `/clear` and restart — sunk cost logic doesn't apply when you can reset for free.

This discipline compounds with the [[ralph-loop]] pattern: each overnight batch enters with a clean context, preserving the model's "smart zone." It also motivates [[profiling-agents]] — the agent that profiled a build suite spent tokens efficiently because its tool surface was tightly scoped. Misconfigured [[model-context-protocol]] servers with dozens of unused tools are the most common culprit.

Two specific tools to weigh on the token budget: [[context7]] injects fresh library docs (useful, but they cost context), and [[claude-code-router]] lets you point the harness at a cheaper provider when the per-token price — not just the count — is what matters.

## The baseline before "hi"

<span style="color:red">Run `/context` on a fresh chat to see what you pay before typing anything. Claude Code's docs put a typical agentic CLI's start-up overhead at ≈10–25k tokens, reaching 40–80k only with many MCPs and skills ([costs docs](https://code.claude.com/docs/en/costs); [[2026-09-10-testing-ai-bank]]). Readings in the rooms ran from ≈24k to ≈70k ([[2026-07-03-ai-bank]], [[2026-08-03-ai-telecom-ops-qa]], [[2026-09-18-ai-agentic-how]], [[2026-10-09-ai-jax-london]]) — 32,500 in a fresh Copilot chat ([[2026-10-01-ai-bank]]), up to 49k of system prompt on one setup ([[2026-09-14-ai-industrial]]). Where it goes:</span>
- <span style="color:red">**MCP tool definitions** — the biggest lever. One tool description is "javadoc for agents", ≈30–40 tokens for a lean one and ≈265 words for a verbose one; 500 tools × ≈40 tokens = 20,000 tokens before hello ([[2026-05-13-ai-it-services]]). Measured servers: Atlassian ≈5–15k, Grafana ≈10–20k, a forgotten IntelliJ MCP ≈30k, Playwright MCP ≈5k; a GitHub + Slack + Sentry + Grafana + Splunk setup ≈55k ([[2026-04-27-ai-healthcare-software]], [[2026-07-23-ai-software-product]], [[2026-09-14-ai-industrial]], [[2026-09-18-ai-agentic-how]]). Copilot reportedly errors above 128 tools, and Junie warns by itself at 104.</span>
- <span style="color:red">**Skill descriptions** — ≈100 tokens of metadata each, inside a listing budget of 1% of the context window ([docs](https://code.claude.com/docs/en/skills)); 50 skills ≈5k, 100 skills ≈6–10k tokens every turn ([[2026-10-01-ai-bank]], [[2026-09-30-ai-enterprise]]). Don't install 60–80 skills ([[2026-08-10-ai-consultancy]]).</span>
- <span style="color:red">**Instruction files** — home and project CLAUDE.md/AGENTS.md; keep the project file under ≈200 lines ([[2026-08-06-ai-bank]]; see [[agents-md-discipline]]).</span>

<span style="color:red">Keep the fresh-conversation baseline under **≈20–30k tokens** ([[2026-09-07-ai-bank]]). Audit monthly or every sprint with `/context all`, `/mcp` and `/skills`: removing unused MCPs cut one baseline from 30k to 20k, a forgotten IntelliJ MCP took a system prompt from 80k to 46k, one unused plugin took another from 70k to 60k ([[2026-07-01-ai-java-community]], [[2026-07-30-ai-safety-critical]], [[2026-09-14-ai-industrial]], [[2026-09-30-ai-enterprise]]). Disable unused MCPs in `.mcp.json` **and restart**, or the definitions stay loaded; move globally installed MCPs to **project scope**; enable connectors per task ([[2026-05-04-ai-telecom]], [[2026-07-23-ai-software-product]]). Lifecycle hooks are programmatic and cost **zero** tokens, unlike skills and MCPs ([[2026-07-01-ai-java-community]]; see [[agent-lifecycle-hooks]]).</span>

## Most tokens live in tool results

**Most tokens live in tool results, not your messages.** File reads, test logs, and stack traces accumulate and ride along on *every* subsequent turn because the LLM is stateless. A single `pom.xml` burns ≈3,000 tokens of XML noise. <span style="color:red">Tool calls and tool results are ≈90–95% of the tokens after an hour of work; of a 900k context, ≈700k are typically tool results ([[2026-08-27-ai-it-services]], [[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]]). Public evidence of the order of magnitude: Anthropic's [context editing](https://claude.com/blog/context-management) (clearing old tool results) cut token use 84% in a 100-turn eval, and Cursor moving MCP output to files cut tokens per agent by 46.9% ([dynamic context discovery](https://cursor.com/blog/dynamic-context-discovery)). Your own prompt of a few hundred tokens is noise — don't micro-optimise it.</span>

The pattern: teach the agent once how to extract what it needs from a structured output, then have it write a deterministic script that does the extraction without burning model tokens — the "write yourself a letter" move. <span style="color:red">*"Don't explain to it tomorrow what to do — have it write today the deterministic script it will run tomorrow"* ([[2026-09-07-ai-bank]]); for systematic refactors that script is an [[openrewrite|OpenRewrite]] recipe ([[determinism-over-llm]]).</span> This is what [[agent-skill]] does at the workflow level. <span style="color:red">Concrete moves:</span>
- <span style="color:red">**Never make the agent swallow logs.** *"Making the agent swallow thousands of log lines is agent abuse"*: read 20 lines → write a filter script → run → read 20 more; 1 GB becomes 100 KB ([[2026-08-03-ai-telecom-ops-qa]]). Tell it *where* the log is instead of pasting it. Stories of a top model reading a whole log, or free-scanning Splunk, end at ≈$10,000 in a day ([[2026-07-03-ai-bank]], [[2026-09-25-ai-bank-migration]]). Scope log platforms to the incident's time window.</span>
- <span style="color:red">**Let the agent run the tests itself** — it greps the 2 lines that matter where you would paste 300 ([[2026-08-03-ai-telecom-ops-qa]]).</span>
- <span style="color:red">**Quiet by default.** Use quiet flags (`git status -s`), give your own CLIs a `-s` flag, and make tripwire scripts *keep silent if OK* ([[2026-09-02-ai-insurer]]; see [[tripwire]]). Trim chatty tool output with a `PostToolUse` hook — keep the 3 fields you need out of 50, or convert to [[toon]] (≈50% fewer tokens, per [[2026-05-18-secure-coding-payments]]).</span>
- <span style="color:red">**Fetch once, grep many.** Dump a ticket to markdown on disk and grep the file instead of re-calling the MCP; query token-heavy wikis (Confluence) only through a [[sub-agents|sub-agent]] that returns a summary; do write-backs (updating Jira) inside a sub-agent so the garbage context dies with it ([[2026-08-10-ai-consultancy]], [[2026-09-30-ai-enterprise]]).</span>
- <span style="color:red">**Point at the files.** Credits burn on *searching*: name the files involved and write a specific prompt — vague prompts cause expensive "repent-and-correct" turns ([[2026-09-25-ai-bank-migration]]).</span>
- <span style="color:red">**Sub-agents only when they save.** Each re-loads ≈20k of system prompt and may re-open files you already paid for; a parent that re-reads its sub-agent's files doubles the bill. The canonical good sub-agent reads 200k and returns 500 ([[2026-09-02-ai-insurer]], [[2026-09-30-ai-enterprise]]).</span>

<span style="color:red">Output-compression proxies are the tempting shortcut, and the measurements are sobering: JetBrains found [[rtk]] **+7.6% more expensive** at low effort (p = 0.004) and ±0% at high effort, with quality unchanged — more turns and cache reads ate the savings ([JetBrains, Jul 2026](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings/)); a vendor-authored study found [[headroom]] +48% on the bill. An earlier version of this page promised "≈30% overall" from RTK; *tokens removed ≠ money saved*, and the `/clear` habit is worth far more ([[2026-07-30-ai-safety-critical]], [[2026-09-02-ai-insurer]]).</span>

## Output and thinking cost more than input

**Output costs ≈5× input.** You pay for input, but generated tokens are roughly five times more expensive — <span style="color:red">true across Anthropic's price list (e.g. Opus 5.5 $4/$20, Sonnet 5.5 $2/$10 per MTok — [pricing](https://platform.claude.com/docs/en/about-claude/pricing)), but not a universal cross-vendor rule ([[2026-09-10-testing-ai-bank]]). *"Let the smart models read much and speak little."*</span> [[toon]] reshapes JSON collections into a compact CSV-for-AI form — but benchmark end-to-end, since an unfamiliar format can cost more reasoning than it saves.

- <span style="color:red">**Thinking is billed as output** ([costs](https://code.claude.com/docs/en/costs#adjust-extended-thinking)) and, on current models, stays in context on later turns (see [[prompt-caching]]) — so higher [[effort-setting|effort]] is directly an output-token cost ([[2026-08-03-ai-telecom-ops-qa]], [[2026-09-30-ai-enterprise]]). An earlier claim that thinking is "thrown away" between turns described pre-Opus-4.5 models only.</span>
- <span style="color:red">**Don't let the agent rewrite whole files** — Opus writing a file from scratch (and leaving a `.bak`) instead of editing is the fastest way to burn output ([[2026-05-27-ai-open-workshop]]). Watch for `Edit failed` repeated in the trace: failed tool calls burning tokens for nothing ([[2026-08-03-ai-telecom-ops-qa]]).</span>
- <span style="color:red">**Shorter prose, same code.** "Be brief", the [[caveman]] skill and Claude Code's `concise` [output style](https://code.claude.com/docs/en/output-styles) all cut narration; one session measured ≈30% fewer words with the same code ([[2026-10-09-ai-jax-london]]), while JetBrains measured Caveman at 8.5% fewer output tokens. But *don't compress the weakest link — your understanding*: prefer the `concise` style, or `learning` on new terrain, which stops and makes you write part of the code ([[2026-08-27-ai-it-services]]). Earlier this page called terse output an anti-pattern for your attention; the current advice is to choose the style deliberately.</span>

## Model and effort choice

<span style="color:red">Cost is **per task, not per token** — rework costs more than tokens ([[2026-10-01-ai-bank]]). Current list prices per MTok in/out: Opus 5.5 $4/$20, Sonnet 5.5 $2/$10, Haiku 5.5 $0.10/$0.50 up to 100K-token prompts, Fable 5.1 $10/$50; the previous generation was Opus 4.5–5 $5/$25, Sonnet 4.5–4.6 $3/$15, Haiku 4.5 $1/$5. From 4.6 on (except Haiku 5.5) the full 1M window is billed at the standard rate ([pricing](https://platform.claude.com/docs/en/about-claude/pricing)). Take prices from that page, not from a session slide — rooms quoted conflicting ratios.</span>
- <span style="color:red">The headline ratio misleads: one session estimated Opus thinks ≈3× more than Sonnet on the same task, making the real gap larger than the price list ([[2026-05-27-ai-open-workshop]]). Hence "Sonnet as daily driver, Opus for orchestration, design and review, Haiku for fan-out scanners" ([[model-hierarchy]]).</span>
- <span style="color:red">The counter-view by autumn 2026: practitioners code with **Opus at medium or low effort**, cheaper overall than a weaker model's rework; *"a well-documented Sonnet beats an Opus without context"* ([[2026-10-01-ai-bank]]). Drop to a cheaper model only when the agent can prove its own result — on non-trivial tasks the rework ate the savings ([[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">Set model and effort **up front**: switching either mid-conversation invalidates the [[prompt-caching|cache]]; **auto model selection is a trap** if it starts weak and must escalate, re-processing the whole context ([[2026-09-07-ai-bank]]). `/plan` can tag tasks with the model to run them (e.g. `[SONNET]`), so the orchestrator delegates to cheaper sub-agents ([[2026-07-09-ai-live-casino]]).</span>
- <span style="color:red">A smaller context window caps cost: keep the spec in a 272k-window chat and implement in a new one; move to 1M deliberately — you don't pay for what you don't fill ([[2026-10-01-ai-bank]]).</span>

## Where the money is lost

<span style="color:red">In order of damage ([[2026-07-20-ai-telecom]], [[2026-08-27-ai-it-services]]): unclear requirements (→ [[grill-me]]) · tasks too big, derailing halfway into a revert · no AGENTS.md, so the agent rescans every task · "LGTM ×10 followed by regret" · oversized CLAUDE.md files and too many skills or tools without Tool Search · a conversation kept open all day · failing tool calls being retried · and above all, **tool output**. A spec is cheap by comparison: ≈2,000 tokens of markdown against ≈100,000 tokens of code ([[2026-10-01-ai-bank]]).</span>

<span style="color:red">Three modes of use, in ascending cost ([[2026-07-03-ai-bank]]): **Explain** (generic "how do Angular signals work?" — use a chat app, not the agent quota), **Find** (cheap), **Do** (write → build → test → fix loops, where both the bill and the power are). Warm up and brainstorm in a cheap chatbot before burning Opus ([[2026-08-31-ai-software-vendor]]).</span>

## Format and language efficiency

**Format efficiency hierarchy:** XML → JSON → YAML → TOML, from worst to best. Putting raw XML or JSON in the context is expensive noise — <span style="color:red">"XML is the definition of waste of tokens"; CSV halves a JSON payload ([[2026-07-01-ai-java-community]], [[2026-07-22-ai-agentic-how]])</span>. Agents don't read big files top-to-bottom — they grep and cherry-pick; they almost never ingest a full `pom.xml`.

- <span style="color:red">**A token is ≈3.5 English characters** for Claude, not a word — a 1,000-word page is well over 1,000 tokens ([glossary](https://platform.claude.com/docs/en/about-claude/glossary); [[2026-08-12-ai-bank]]). Count a prompt with a tokenizer before guessing.</span>
- <span style="color:red">**Images** cost ≈ width × height / 750 tokens, capped around 1.6k after resizing ([vision docs](https://platform.claude.com/docs/en/build-with-claude/vision); [[2026-10-01-ai-bank]]) — use them deliberately; video is far worse, so send screenshots ([[2026-07-30-ai-safety-critical]]).</span>
- <span style="color:red">**Language:** prompts in a lower-resource language (Romanian in these sessions) cost more tokens for the same idea — estimates ranged from 5% to 20% — and drift from the training distribution, so architecture terms translate crookedly ([tokenizer unfairness, arXiv 2305.15425](https://arxiv.org/abs/2305.15425); [[2026-08-31-ai-software-vendor]], [[2026-09-30-ai-enterprise]]). Write the hard design and architecture prompts in clean English; dictate in your mother tongue when exploring or tired — *"better in Romanian and alive than in English and dead"* ([[2026-07-20-ai-telecom]], [[2026-07-30-ai-safety-critical]]). Verbose programming languages cost more too: one estimate put Java at ≈3× Python's tokens for the same information ([[2026-05-04-ai-telecom]]).</span>

**A conversation is a stateless API.** Every turn re-sends the entire prefix as input; <span style="color:red">thinking blocks and</span> tool outputs accumulate. [[prompt-caching]] makes this affordable — <span style="color:red">a cache read costs ≈10% of base input or less — but the cache expires (5 minutes on an API key, 1 hour on a subscription's main thread), so an idle fat terminal re-pays full price.</span> <span style="color:red">The [[effort-setting]] is the other dial; the earlier advice "use **xhigh**, never **max**" is out of date — Claude Code now defaults Opus 5.5 to `medium`, which Anthropic says matches Opus 5 at `high`, and `max` is flagged as prone to overthinking ("test before adopting it"), not banned ([docs](https://code.claude.com/docs/en/model-config#adjust-effort-level)).</span>

## Measure it

<span style="color:red">Keep cost on screen. A status line with model · effort · context used · 5-hour quota · turn and session cost — *"if I see 300k, I wake up"*; **a two-digit session cost is the signal to stop and ask what happened** ([[2026-07-09-ai-live-casino]], [[2026-08-10-ai-consultancy]]). Tools seen in sessions:</span>
- <span style="color:red">`/context` (and `/context all`), `/cost`, `/usage`; `Ctrl+O` shows the tool calls and what a turn cost ([[2026-08-27-ai-it-services]]).</span>
- <span style="color:red">[`npx ccusage@latest`](https://github.com/ryoppippi/ccusage) reads Claude Code's local logs — useful when a corporate LLM proxy exposes no quota endpoint; [claude-powerline](https://github.com/Owloops/claude-powerline) for session/week tokens and cost in the status line ([[2026-04-27-ai-healthcare-software]]).</span>
- <span style="color:red">[CodexBar](https://codexbar.app/) for all AI-coding limits in the macOS menu bar; [AgentsView](https://github.com/kenn-io/agentsview) for local per-session analytics ([[2026-08-10-ai-consultancy]], [[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">`/insights` over your conversation logs shows where you wasted tokens — asked to be brutal, it surfaced "confident claims without verification" and costly dictation mistranscriptions ([[2026-07-09-ai-live-casino]], [[2026-09-02-ai-insurer]]).</span>

<span style="color:red">Anthropic's own [token-saving guide](https://claude.com/blog/maximizing-the-value-of-your-claude-code-sessions) (Aug 2026) sums up the habits: set model and effort up front; review with the top model; `/clear` or a new session **between** tasks; compact before a long pause; script deterministic work; use CLIs with quiet flags; pick an output style deliberately ([[2026-08-31-ai-software-vendor]]). The two habits rooms rate highest: `/clear` between tasks — *"the biggest, most banal win"* — and an AGENTS.md line `always start with the simplest solution that could work` ([[2026-08-06-ai-bank]]).</span>

## Burning tokens as an adoption metric

**Token-burn on useful work is the *first* adoption metric.** Counter-intuitively, the engineers who blow through their quota — get it raised, and burn that too — are the ones extracting real leverage; spending tokens (in the smart zone, on real tasks) is the leading indicator of adoption, not waste. <span style="color:red">The named origin is Steve Yegge's "token churn rate" dashboard ([[2026-05-06-ai-ecommerce]]). Two caveats: where management watches usage logs, some developers *token-pad* to look AI-active; and the logs can be used well (spot laggards and train them) or badly (prompt-level micro-management) — see [[quota-economics]] for team-level budgets.</span> The flip side is the [[model-hierarchy]] discipline: spend the expensive tokens where intelligence is needed, and farm the rest to cheap models.

## See also

- [[ralph-loop]]
- [[context-window]]
- [[model-context-protocol]]
- [[profiling-agents]]
- [[tool-calling]]
- [[context7]]
- [[claude-code-router]]
- [[prompt-caching]]
- <span style="color:red">[[quota-economics]]</span>
- [[rtk]]
- [[headroom]]
- [[toon]]
- [[effort-setting]]
- [[caveman]]
- [[model-hierarchy]]
- <span style="color:red">[[cli-vs-mcp-tradeoff]]</span>
- <span style="color:red">[[dynamic-tool-discovery]]</span>
- <span style="color:red">[[sub-agents]]</span>
- <span style="color:red">[[determinism-over-llm]]</span>
- <span style="color:red">[[openrewrite]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
