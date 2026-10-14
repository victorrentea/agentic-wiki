---
title: Context Window
category: concept
tags: [context-window, token-economy, lost-in-the-middle, memory, performance]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-10
updated: 2026-10-13
---

The context window is the finite token budget a model sees in one call; bigger is not always better — it costs money, degrades quality on cheap models, and suffers from "lost in the middle."

## What fills the window before you type

**What the model actually sees each turn:** conversation history + `AGENTS.md`/`CLAUDE.md` + the front-matter *descriptions* of your skills + the *schemas* of your MCP tools + the invisible *harness prompt* the CLI injects (a big reason a stronger [[harness]] beats a weaker one). <span style="color:red">The harness prompt alone is ≈250 lines wrapped around yours; add `~/.claude/CLAUDE.md`, the project memory hierarchy, the `/ide` reference (open file + selected lines), tool and MCP definitions and every skill's description. *"Write one 'A' and you re-send and re-pay the whole flood"* ([[2026-08-06-ai-bank]], [[2026-08-31-ai-software-vendor]]).</span>

<span style="color:red">The fixed overhead of a bare "hi" kept growing through 2026 as people installed MCPs and skills: ≈8% of the window in April (system instructions ≈1.4%, tool definitions **6–14%**), then ≈20k, ≈27k, ≈50k, and 60–70k tokens by September — 60k on Victor's own machine, up from 25k a few weeks earlier ([[2026-04-16-ai-safety-critical]], [[2026-07-30-ai-safety-critical]], [[2026-08-31-ai-software-vendor]], [[2026-09-04-ai-software-company]], [[2026-09-28-ai-gaming-tech]]). Don't trust any fixed number — **measure with `/context`**. Targets: keep the always-loaded part **under ≈25% of the window**, ideally **under 35–40k tokens**.</span>

<span style="color:red">Reference sizes: 60 skills ≈ 3k tokens of descriptions; the Grafana MCP alone exposes 65 tools ≈ 10k tokens ("give it a hammer and everything's a nail"); Anthropic's own example of GitHub + Slack + Sentry + Grafana + Splunk ≈ 55k tokens of definitions. Too many tools and the agent also confuses them — e.g. a Gmail MCP vs a Gmail CLI on the same task ([[2026-08-31-ai-software-vendor]], [[2026-09-04-ai-software-company]], [[2026-09-28-ai-gaming-tech]]).</span>

<span style="color:red">What is *not* in the window: **hooks are never sent to the LLM** — they react programmatically to harness events — and path-scoped rules stay out until a matching file is touched ([[agent-lifecycle-hooks]], [[2026-08-10-ai-consultancy]]).</span>

## Tool output is most of the bill

<span style="color:red">The fixed overhead is not where conversations get expensive. **Tool calls and their results are ≈90–95% of a working context** — in one measured conversation, 350k of 500k tokens were tool output — and **file reads, not your prompts, are the biggest sink** ([[2026-08-10-ai-consultancy]], [[2026-09-28-ai-gaming-tech]]). Corollaries:</span>

- <span style="color:red">**A clean codebase is cheaper to work in.** Unfamiliar patterns make the agent nervous and it reads far more "just in case" — repay tech debt instead of writing "don't do that" ten times ("garbage in, tokens out") ([[2026-09-04-ai-software-company]]).</span>
- <span style="color:red">**Context can matter more than the model:** Sonnet with the right context beats Opus bombarded with irrelevant knowledge, which "distracts" the agent ([[2026-07-20-ai-telecom]]).</span>
- <span style="color:red">A [[sub-agents|sub-agent]] only pays off when it reads dozens of files and returns ≈1,000 tokens; otherwise it reloads the system prompt and reopens the same files ([[2026-09-07-ai-bank]]).</span>
- <span style="color:red">General-purpose questions belong in the chat app, **not the coding agent**: a 60-token question there still costs ≈20k paid tokens of system prompt, and "answer in 10 lines" only trims the output ([[2026-09-04-ai-software-company]]).</span>

## Why cap what you send

Three independent reasons to keep context lean:

1. **Cost.** Every token in and out is billed. [[chat-memory]] defaults to a window of 6 messages in the demo (≈60 in production) for exactly this reason.
2. **The [[dumb-zone]].** Not just a small-model problem: *every* model degrades as the window fills. <span style="color:red">The sessions' rule of thumb is ≈65% full (≈130k for a 200k Sonnet, ≈300–400k for Opus), but that is a heuristic, not a published measurement. A 1M window does **not** mean 1M of *usable reasoning*: single-fact lookup holds up well, while **multi-fact retrieval and reasoning degrade with length** ("context rot", [Chroma](https://www.trychroma.com/research/context-rot)). Anthropic's own figure for its 8-needle MRCR v2 test at 1M is 76% for Opus 4.6, vs 18.5% for Sonnet 4.5 ([Anthropic](https://www.anthropic.com/news/claude-opus-4-6)). An earlier version of this page claimed recall "drops to ≈50% around half a million tokens"; no source supports that number ([[2026-10-13-ai-bank]]).</span> The bigger window buys room to leave empty, not memory.
3. **Lost in the middle.** Research ([arxiv 2307.03172](https://arxiv.org/abs/2307.03172)) shows models attend best to the **start and end** of the context; information buried in the middle is reliably degraded even in models marketed as long-context.

## 200k or 1M?

<span style="color:red">Sessions disagreed. One said **200k is the healthy default — don't enable 1M** ("doesn't have the strength to reason across such big") and don't buy the 1M window to escape the dumb zone ([[2026-07-01-ai-java-community]], [[2026-09-04-ai-software-company]]). Another recommended **Opus with 1M for legacy work or refactors touching dozens of files** — at 200k you fill the window and slide into hallucination ([[2026-07-30-ai-safety-critical]]). Both hold: lean context for normal tasks, a wide window for wide legacy sweeps.</span>

<span style="color:red">The facts have since moved. Current models (Opus 4.7+, Sonnet 5+, Haiku 5.5, Fable) run with a native 1M window in Claude Code, and Claude 4.6+ models (except Haiku 5.5) bill the full 1M at **standard per-token pricing**, with no long-context premium ([pricing](https://platform.claude.com/docs/en/about-claude/pricing)). So the real lever is no longer "enable 1M" but **when you compact or clear** — and you can set it with `/autocompact <size>` or `CLAUDE_CODE_AUTO_COMPACT_WINDOW` (100K–1M), or opt out with `CLAUDE_CODE_DISABLE_1M_CONTEXT=1` ([docs](https://code.claude.com/docs/en/model-config#set-the-auto-compact-window)). In May 2026 the quoted sizes were Sonnet 200k, Opus 1M, GPT-5 1M, Codex 400k ([[2026-05-04-ai-telecom]]).</span>

## Tool definitions eat tokens silently

[[tool-calling]] schemas are injected into the context on every turn. A workspace with 100+ MCP tools can consume thousands of tokens before a single user message. The Claude Code "tool-search-tool" loads only tool *names* by default and fetches full schemas on demand — trading a small accuracy risk for significant token savings. See [[token-economy]].

<span style="color:red">[Tool search](https://code.claude.com/docs/en/mcp#configure-tool-search) is **on by default** in recent Claude Code (since ≈v2.1.221, per one session), kicking in once MCP tools would exceed ≈10–15% of the context depending on the version; Anthropic reports it cuts definition tokens by >85%. You still should **deny-rule unused plugins, MCPs and built-in tools** in `settings.json` and audit with `/context` ([[2026-09-04-ai-software-company]], [[2026-09-28-ai-gaming-tech]]). Claude Code also warns above **10k tokens** of output from one MCP call and caps it at **25k** by default ([docs](https://code.claude.com/docs/en/mcp#mcp-output-limits-and-warnings)).</span>

## Reasoning across turns

Because LLMs are stateless, the illusion of memory is the whole transcript re-sent every turn. <span style="color:red">Earlier versions of this page said the agent's reasoning is discarded at turn end. That describes older models only. On Claude Opus 4.5+, Sonnet 4.6+, Haiku 5.5 and Fable, the API **keeps all previous turns' thinking blocks in context and bills them as input**; earlier models (Sonnet 4.5, Haiku ≤4.5, Opus ≤4.1) kept only the last turn's thinking — within a turn, across its tool calls, thinking is always sent back ([thinking docs](https://platform.claude.com/docs/en/build-with-claude/thinking#thinking-block-preservation-by-model); [[2026-09-18-ai-agentic-how]], [[2026-09-28-ai-gaming-tech]]). `/resume` reloads the transcript saved under `~/.claude/projects/…` ([[2026-05-04-ai-telecom]]).</span>

<span style="color:red">The practical advice survives for older models and other vendors: the agent's **final report is long because it writes it for itself too**, to remember what it decided. So **don't compress that summary** with a terse-output skill like [[caveman]] — "be brief" in your prompt gets ≈80% of the benefit without the risk. Watch the first 2–3 reasoning blocks plus the tool calls to catch drift early; weak models reason a lot, Opus says little because it understands directly ([[2026-09-07-ai-bank]], [[2026-09-28-ai-gaming-tech]]).</span>

## Compaction, `/clear` and handovers

<span style="color:red">Three ways to manage a filling window: a new conversation, auto-compaction (lossy), or [[sub-agents]] with their own window ([[2026-04-16-ai-safety-critical]]).</span> For an *agent's* window, **auto-compaction is itself a failure** — <span style="color:red">by the time the harness summarizes, the decisions made just before were already taken inside the [[dumb-zone]].</span> Prefer a [[handover]] file or [[sub-agents]] over compaction, and `/clear` between unrelated tasks. Run `/context` at the start of a sprint: a fresh chat can already burn ≈20k tokens on MCP + [[agent-skill|skills]] before you type.

<span style="color:red">**When does compaction fire?** Sessions reported ≈65%, ≈67%, ≈85% and ≈90% — all observations, none a documented constant. The ≈65% figure is Copilot's VS Code context wheel turning **yellow** — the cue to hand over *before* compaction, not after ("yellow/red = it's lying to you"). Claude Code by default compacts when the conversation reaches the model's limit: ≈967K tokens on a native 1M window, the full 200K otherwise. `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` can only make it fire *earlier* ([docs](https://code.claude.com/docs/en/model-config#set-the-auto-compact-window); [[2026-05-13-ai-it-services]], [[2026-05-18-secure-coding-payments]], [[2026-05-27-ai-open-workshop]], [[2026-08-06-ai-bank]]).</span>

<span style="color:red">Rules that live only in conversation turns can be **compacted away** in a long session; the system prompt and memory files are never compacted, so keep key rules there ([[claude-md]], [[2026-04-27-ai-healthcare-software]], [[2026-05-04-ai-telecom]]).</span>

*Don't mix unrelated tasks in one conversation* — `/clear`. <span style="color:red">Many people keep one conversation open all day — don't. But `/clear` has a price too: you lose the cache, re-process ≈20k+ tokens and re-discover the codebase. So don't clear between microscopic polish tasks in the same area; **clear once ≈100–150k tokens have accumulated, or when the next task touches different files** ([[2026-07-20-ai-telecom]], [[2026-09-04-ai-software-company]], [[2026-09-28-ai-gaming-tech]]).</span> A practical ceiling cited: uneasy past 400K tokens, "doing it wrong" past 500K.

## What a turn costs

<span style="color:red">Every turn re-sends the whole window, so cost depends on whether the [[prompt-caching|cache]] is warm. On an API key the cache lives 5 minutes; on a Pro/Max subscription the main conversation gets 1 hour, while subagents get 5 minutes ([docs](https://code.claude.com/docs/en/prompt-caching#which-ttl-each-request-gets)). Claude inside Copilot was measured expiring after 5 minutes. Data points from sessions: saying "hi" into a 154k conversation cost **$1.5**; after lunch a "hi" cost **$20**, the next one **$0.50**; a 200k context idle for an hour costs ≈$2 to wake on an API key. **Never say "hi" to a session idle over an hour** — hand over or `/clear` instead ([[2026-07-30-ai-safety-critical]], [[2026-09-28-ai-gaming-tech]]).</span>

<span style="color:red">Current list prices (input / output per MTok): Opus 5.5 $4 / $20, Sonnet 5.5 $2 / $10, Haiku 5.5 $0.10 / $0.50 up to 100K tokens; output is 5× input on every row ([pricing](https://platform.claude.com/docs/en/about-claude/pricing)). Hence Opus plans and reviews (reads much, writes little) and Sonnet codes on a budget ([costs docs](https://code.claude.com/docs/en/costs#choose-the-right-model); [[2026-09-04-ai-software-company]], [[model-hierarchy]]).</span>

<span style="color:red">Subscriptions hide all of this. One session estimated a $100 plan burning ≈$6,000 at API prices; the only sourced measurement found is $1,588 of API-equivalent usage in 30 days on a $200 Max 20x plan ([source](https://www.productcompass.pm/p/claude-code-pricing)) — "often several to tens of times the subscription price"; measure your own with `ccusage`. **Enjoy subscription pricing while it lasts, but plan for API rates** ([[2026-09-28-ai-gaming-tech]], [[token-economy]]).</span>

<span style="color:red">**Watch the burn:** a status line showing context size, 5-hour/weekly quota pace, conversation and last-turn cost, **minutes since the last turn (is the cache still warm?)** and the model class of spawned sub-agents ([victor-statusline](https://github.com/victorrentea/victor-statusline), [CodexBar](https://codexbar.app)). Retrospect weekly with [AgentsView](https://github.com/wesm/agentsview) and Claude Code's `/insights`, which spots failing tools and wasted prompts ([[2026-09-28-ai-gaming-tech]]).</span>

## Implications for [[chat-memory]]

The sliding window in [[chat-memory]] is the primary defense against context bloat. Production systems augment it with **summarization / compaction** of evicted turns so the bot retains a gist of early context without re-sending the raw messages. [[rag]] is the complementary strategy: pull relevant knowledge on demand rather than pre-loading it.

Source: [[2026-06-10-spring-ai-open-workshop]]

External: ["Lost in the Middle" paper](https://arxiv.org/abs/2307.03172) · <span style="color:red">[Karpathy — Intro to LLMs (1 h talk)](https://www.youtube.com/watch?v=zjkBMFhNj_g) · see tokens with the [OpenAI tokenizer](https://platform.openai.com/tokenizer) (`print(`, `AND`, `_ING` are separate tokens)</span>

## See also

- [[chat-memory]]
- [[advisor]]
- [[token-economy]]
- [[rag]]
- [[tool-calling]]
- [[system-prompt]]
- [[dumb-zone]]
- [[prompt-caching]]
- [[handover]]
- [[harness]]
- <span style="color:red">[[sub-agents]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[model-hierarchy]]</span>
- <span style="color:red">[[caveman]]</span>
- <span style="color:red">[[effort-setting]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
