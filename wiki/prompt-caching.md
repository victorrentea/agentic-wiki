---
title: Prompt Caching
category: concept
tags: [token-economy, cost-optimization, context-window, stateless-api, cache, ttl]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

Prompt caching is the provider-side mechanism that stores the conversation prefix you already sent so that re-sending it on the next turn costs a fraction of full input price — both the savior and the trap of the [[token-economy]].

## Why it exists

A conversation is a **stateless API**: every turn re-sends *everything before you* as input tokens, prefixed to your new prompt. So one tiny new token on top of a 65k-token history still pays for the whole prefix. <span style="color:red">Tool calls and their outputs accumulate forever — and so does thinking: on current models (Opus 4.5+, Sonnet 4.6+, Haiku 5.5, Fable 5.x) the API keeps all previous turns' thinking blocks in context and bills them as input; only older models dropped them between turns ([docs](https://platform.claude.com/docs/en/build-with-claude/thinking#thinking-block-preservation-by-model)).</span> Caching the unchanged prefix is what makes long agentic sessions affordable — <span style="color:red">a cache read costs **0.1×** base input on most Claude models, **0.05×** on Opus 5.5 and Sonnet 5.5, **0.025×** on Fable/Mythos 5.1 ([pricing](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)). Sessions quoted OpenAI/Codex as discounting a cached prefix only to ≈50% — check your vendor ([[2026-05-06-ai-ecommerce]], [[2026-05-27-ai-open-workshop]]).</span>

<span style="color:red">What is cached is the model's **KV cache** for the prefix ([vLLM's explanation of prefix caching](https://docs.vllm.ai/en/latest/design/prefix_caching.html)), not some "state of the network" ([[2026-10-13-ai-bank]]). It is built [prefix by prefix](https://code.claude.com/docs/en/prompt-caching#how-the-cache-is-organized), each prefix expiring a TTL after its last use and **refreshed at no cost on every hit** ([[2026-09-04-ai-software-company]]). Anthropic's own engineering view is that ["prompt caching is everything"](https://claude.com/blog/lessons-from-building-claude-code-prompt-caching-is-everything) — they page engineers when the hit rate drops. Cache breakpoints are placed explicitly in each API request, so the **harness** decides the strategy: Claude Code does it carefully, simpler harnesses more naively, and the difference shows in the monthly bill ([[2026-05-25-ai-government]]).</span>

## TTL: 5 minutes or 1 hour — who gets which

<span style="color:red">On the API the cache lasts **5 minutes by default**, with an opt-in **1-hour TTL** (`cache_control.ttl`). A 5-minute cache *write* costs 1.25× base input, a 1-hour write 2× ([docs](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)). Five minutes is the default, not a cap ([[2026-09-18-ai-agentic-how]]).</span>

<span style="color:red">Claude Code picks the TTL per request ([docs](https://code.claude.com/docs/en/prompt-caching#which-ttl-each-request-gets)):</span>
- <span style="color:red">**Pro/Max subscription, within plan usage:** the **main conversation gets 1 hour**; sub-agents, compaction and titles get 5 minutes.</span>
- <span style="color:red">**API key, cloud provider, or once usage credits (overage) kick in:** everything gets 5 minutes.</span>
- <span style="color:red">**Override:** the `promptCacheTtl` / `subagentPromptCacheTtl` settings (changelog v2.1.243) or `CLAUDE_CODE_PROMPT_CACHE_TTL`; earlier, `ENABLE_PROMPT_CACHING_1H` (v2.1.108) opted API-key users into 1 hour.</span>

<span style="color:red">So the "5-minute trap" is an **API-key and sub-agent trap**; on a subscription the trap springs after an hour — typically lunch ([[2026-09-02-ai-insurer]], [[2026-09-07-ai-bank]], [[2026-09-30-ai-enterprise]]). Copilot's TTL depends on org settings; one room observed ≈5 minutes on Opus ([[2026-09-07-ai-bank]]). An earlier version of this page said the TTL "was 1 hour until ≈April, then silently dropped to 5 minutes", and one session dated the drop to 2 April 2026. The record says otherwise: subscribers saw 1-hour writes from ≈1 Feb 2026, they fell back to 5 minutes around 6–7 March (server-side experiment gating, per Anthropic in [issue #46829](https://github.com/anthropics/claude-code/issues/46829)), and v2.1.129 (6 May 2026) fixed a silent 1-hour → 5-minute downgrade. No 2 April change is documented.</span>

<span style="color:red">If the cache has expired when you reply, you **re-pay full price** for the entire prefix — plus the write premium — so caching only wins when you genuinely reuse the prefix soon.</span>

The canonical failure: **four terminals open**, two of them carrying 500k of accumulated context, all neglected past the cache window. Idle fat terminals are the worst case both for the cache *and* for the [[dumb-zone]] — *"you're not a 10× developer, you're a 10× sucker."* <span style="color:red">An especially brutal case: a long build or test run that outlasts the TTL — an 8-minute Spring test run on a 5-minute cache, a 1h05 Jenkins build on a subscription — and you re-pay full input price on the entire accumulated context ([[2026-09-02-ai-insurer]]).</span> <span style="color:red">The 1-hour TTL is worth paying for exactly in long human-review loops: *"never leave a 300k-token agent idle while you review its code"* ([[2026-07-22-ai-agentic-how]]).</span>

## What lets the cache expire

<span style="color:red">The cache does **not** expire while the agent is busy inside its own loop — every turn is a hit that refreshes it, so an agent can work for hours on a warm cache. It expires while the conversation **waits** ([[2026-05-27-ai-open-workshop]], [[2026-06-05-ai-consultancy]], [[2026-09-04-ai-software-company]]):</span>
- <span style="color:red">for **you** — a question, a Stop, a Slack message that pulls you out of flow;</span>
- <span style="color:red">for a **tool or build** — a `sleep`-based CI-watch loop costs no tokens while waiting, but its result lands on an expired cache ([[2026-08-31-ai-software-vendor]]);</span>
- <span style="color:red">for a **sub-agent** that runs longer than the TTL — the idle orchestrator re-pays everything when it returns (a sub-agent building the backend for 1h15 kills even a subscription's 1-hour cache) ([[2026-08-27-ai-it-services]], [[2026-09-02-ai-insurer]]);</span>
- <span style="color:red">for anything that reports back late into a big conversation: background shell monitors, [cross-session messages](https://code.claude.com/docs/en/cross-session-messaging), scheduled tasks, goal check-ins ([why usage climbs in a long session](https://code.claude.com/docs/en/costs#why-usage-climbs-in-a-long-session); [[2026-08-13-ai-insurer]]). Forgotten parallel terminals re-pay their whole grown context on the next Enter.</span>

<span style="color:red">To push "build passed/failed" into a waiting session the official mechanism is [channels](https://code.claude.com/docs/en/channels) ([[2026-09-02-ai-insurer]]).</span>

## What invalidates the cache

<span style="color:red">The cache is an **exact-prefix** match, ordered `tools → system → messages`; a change at one level invalidates everything after it ([docs](https://platform.claude.com/docs/en/build-with-claude/prompt-caching#what-invalidates-the-cache); [[2026-08-13-ai-insurer]]):</span>
- <span style="color:red">**Adding or removing an MCP server or tool** mid-session, or rewriting skill descriptions that sit high in the system prompt ([[2026-09-07-ai-bank]]). Keeping tool schemas out of the prefix is one more reason the tool-search tool helps ([[2026-05-06-ai-ecommerce]]; see [[dynamic-tool-discovery]]).</span>
- <span style="color:red">**Switching model** — each model has its own cache — and, on most models, **switching effort** ([docs](https://code.claude.com/docs/en/prompt-caching#actions-that-invalidate-the-cache); [[2026-09-14-ai-industrial]]). Choose both at the start; above ≈200k tokens a switch re-processes everything. This is why "plan with Opus, execute with Sonnet" is done in a **new conversation** fed with the design markdown, never by switching the model in place ([[2026-04-16-ai-safety-critical]], [[2026-05-04-ai-telecom]]; see [[model-hierarchy]]).</span>
- <span style="color:red">**Auto model routers**: if the router answers your opening "hi" with a cheap model and later has to escalate, the whole context is re-processed — or it sticks with the weak model to protect the cache. Open with the real task ([[2026-10-13-ai-bank]]).</span>
- <span style="color:red">**An edit early in the conversation** (rewind, edited message) re-pays everything after it.</span>

## Cost cliff in practice

<span style="color:red">**Worked example at current list prices** (Opus 5.5: $4/MTok input, cache read 0.05× = $0.20/MTok — [pricing](https://platform.claude.com/docs/en/about-claude/pricing)), for a 900k-token conversation: a warm "hi" reads the prefix for ≈**$0.18**; a cold one re-writes it at ≈**$4.50** (5-minute write, 1.25×) or ≈**$7.20** (1-hour write, 2×) — a **25–40× cliff** on a single Enter. On Opus 5 ($5/MTok, read 0.1×) the same pair is ≈$0.45 vs ≈$5.60.</span>

<span style="color:red">Observed in sessions, across Opus versions and context sizes (anecdotes, not benchmarks):</span>
- <span style="color:red">600k context, idle >1 h: a "hi" cost **$6** vs **$0.30** inside the window ([[2026-08-13-ai-insurer]]).</span>
- <span style="color:red">320k: **$3** after a long pause vs **$0.30** ([[2026-09-07-ai-bank]]); a conversation that had burned $42 the day before cost ≈$3 for one more prompt the next morning ([[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">900k after lunch: **$2 per "hello"**, in a session that cost $147 in total; 270k: $19 → $21 after one Enter past an hour ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">Cold "hi" at 120–150k ≈ **$1–1.10** ([[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]); one "hi" after an hour moved a weekly subscription quota from 63% to 69% ([[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">The earlier headline on this page — a single Opus 1M-context prompt left idle cost **$20**, the same prompt within the window **$0.50**, a 40× difference — was retold at sizes from ≈90k to ≈900k tokens. A session fact-check found the $20 only fits older Opus pricing ($15/MTok input plus the write premium); at Opus 5 prices it comes out at ≈$4.5 ([[2026-09-16-ai-safety-critical]]). The ratio survives; the absolute number is stale — *"calculate before you hit Enter"*.</span> <span style="color:red">The operational lesson: `/clear` between tasks, lazy-load [[agent-skill|skills]], keep [[claude-md]] tight, and never run a heavyweight model on trivial tasks like CSS. (Output-compression proxies such as [[rtk]] and [[headroom]] were once on this list; measured bills show them cost-neutral or worse.)</span>

<span style="color:red">**Don't leave a fat agent waiting on you.** On an API key, sessions moved the threshold down to ≈**150k** tokens ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-06-ai-bank]]); on a subscription, the pain starts at ≈400–500k after an hour away ([[2026-09-02-ai-insurer]]).</span> Either reply quickly, or `/clear` and start fresh.

## Habits that keep the cache warm

- <span style="color:red">**`/compact` while the cache is still warm** — before going home or a long lunch. Compaction costs the most on a cold cache, because the summary request re-reads the whole history uncached ([docs](https://code.claude.com/docs/en/prompt-caching#compacting-the-conversation)); compacting the next morning is a full miss. Or write a [[handover]] ([[2026-09-04-ai-software-company]], [[2026-09-07-ai-bank]], [[2026-09-24-ai-bank]]).</span>
- <span style="color:red">**`/clear` when you switch area, not within one.** Backend → frontend: clear. Same area: keep the context — a new conversation restarts at ≈30k tokens and **loses the "golden tokens" of exploration**; ask for the deeper test cases *while the context is still warm* ([[2026-09-02-ai-insurer]], [[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">**Get called back in time.** A Stop hook that plays a sound (escalating bell, Slack ping, phone) so you answer inside the window; a global hook that **beeps when a conversation passes 400k tokens** ([[2026-07-30-ai-safety-critical]]); a status line showing **time since last turn** ([victor-statusline](https://github.com/victorrentea/victor-statusline)) or `cost.total_cost_usd` ([statusline JSON](https://code.claude.com/docs/en/statusline#cost-and-duration-tracking)). See [[agent-lifecycle-hooks]].</span>
- <span style="color:red">**Babysit one agent well, don't pretend to manage four**: at most 1–2 concurrent agents — one long-horizon Opus plus one light 40–50k task ([[2026-05-27-ai-open-workshop]], [[2026-06-02-ai-open-workshop]], [[2026-06-08-ai-security-tools]]).</span>
- <span style="color:red">**Look at the numbers:** `/usage`, `/context`, `/cost`; [CodexBar](https://codexbar.app) for quotas and API-equivalent cost in the macOS menu bar; [AgentsView](https://www.agentsview.io/) for per-session cost over past sessions, at zero tokens ([[2026-09-07-ai-bank]]).</span>

## Subscription vs API key

<span style="color:red">Because the cache TTL, quotas and subsidies all differ by billing route, *check which plan you're on before discussing token economy* ([[2026-08-13-ai-insurer]]). An earlier version of this page said a subscription is "≈10× cheaper per effective token"; sessions since then quoted anything from 10× to 60× — but the only sourced measurement is ≈8× ($1,588 of API-equivalent usage in 30 days on a $200 Max plan, [Paweł Huryn](https://www.productcompass.pm/p/claude-code-pricing)), and the higher multipliers are unverified. The honest statement: **often several to tens of times cheaper, depending on usage — measure your own with `ccusage`**. Reserve API keys for programmatic pipelines and CI agents; use a subscription for interactive harness sessions. Details, Copilot credits and quota tactics: [[quota-economics]].</span>

## See also
- [[token-economy]]
- <span style="color:red">[[quota-economics]]</span>
- [[context-window]]
- [[dumb-zone]]
- [[plan-mode]]
- [[ai-addiction]]
- <span style="color:red">[[model-hierarchy]]</span>
- <span style="color:red">[[effort-setting]]</span>
- <span style="color:red">[[handover]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[dynamic-tool-discovery]]</span>
- <span style="color:red">[[sub-agents]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
