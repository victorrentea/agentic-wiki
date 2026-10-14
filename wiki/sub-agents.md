---
title: Sub-Agents
category: pattern
tags: [orchestrator, multi-agent, context-management, research, delegation, router, fork, review]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-03-ai-bank]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

Sub-agents ("babies") are fresh-context worker agents an orchestrator spawns to research or review in parallel, each reporting back a few compact paragraphs — so the orchestrator escapes the [[dumb-zone]] without reading hundreds of files itself.

## Orchestrator + minions

The pattern's origin is Anthropic's [multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system): an orchestrator delegates to parallel workers, each of which burns *its own* context and returns only a distilled answer. It is one of the three escapes from the dumb zone (with [[handover]] and lossy compaction), and it shines when you would otherwise open hundreds of files — e.g. one baby per repo or module.

<span style="color:red">**The point is context isolation, not parallelism** — parallelism is a bonus. Sub-agents are a **context firewall**: sessions measured them reading ≈50–200k tokens and reporting ≈1k (a few hundred words, ≈100 lines at most) — "a sub-agent burns 50,000 tokens in enemy territory and reports 1,000". Anthropic's [context-engineering essay](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) puts the distillate at 1–2k tokens. That is "the difference between an agent that dies at 35k lines and one that navigates serious legacy code", and it lets your own conversation stay an orchestrator reading short reports. Fresh context also means "no inherited traumas".</span>

<span style="color:red">**Parallel conversations ≠ sub-agents.** Two terminals you juggle yourself are manual parallelism — "the bottleneck isn't the quota, it's your brain", and the burnout pattern ([[system-2-fatigue]]). N sub-agents under one orchestrator means you still talk to one boss. If you do run agents side by side, the sweet spot is ≈2–3; past ≈4 you trip over merge conflicts ([[worktree]]).</span>

## <span style="color:red">What a sub-agent inherits — and doesn't</span>

- <span style="color:red">It inherits the **harness**: system prompt, tools, MCP servers, skills, global and project CLAUDE.md. It does **not** inherit the parent conversation — only the prompt you give it. Briefs must be self-contained.</span>
- <span style="color:red">It returns **only its final message** to the orchestrator. Want the details? Have it write them to a file.</span>
- <span style="color:red">It **can't come back to ask you a question** mid-flight — give it everything up front, or it will guess and report the guess as fact.</span>
- <span style="color:red">A custom sub-agent file (`.claude/agents/*.md` — front-matter, model, allowed tools) is a *brainwashed child conversation*: effectively **a skill in a separate context**, with its own model and restricted tools ([[custom-agent]]). A slash command, by contrast, is just a saved prompt. Children can have their own children, and can be kept alive and re-prompted — but you can't talk to them directly.</span>
- <span style="color:red">**Always specify the sub-agent's model.** One left on default found a single "issue": a 20-character line split in two. The built-in Explore agent now inherits the main model, so define your own `Explore` with `model: haiku` to keep bulk reading cheap.</span>

## <span style="color:red">The cost, and fork mode</span>

**The cost:** each baby starts from **context zero** and cannot see files the orchestrator already read, so Opus sometimes *refuses* to spawn one (it would have to re-read the POM). Use sub-agents when the re-read cost is worth the parallelism, not reflexively. <span style="color:red">Concretely, every fresh child re-pays the system prompt, tool definitions and skills — a "franchise" of ≈20–30k tokens — and, per the docs, sub-agents get the **5-minute cache TTL even on a subscription**, while the main conversation gets 1 hour ([prompt caching docs](https://code.claude.com/docs/en/prompt-caching#which-ttl-each-request-gets)). Children never share a cache once their paths diverge, and two of them may read the same file twice. One session observed Claude Code declining to spawn a child for tiny tasks (under ≈4 tool calls) because it is net-negative.</span>

<span style="color:red">**Fork mode** resolves the "context zero" problem: a forked sub-agent copies the parent's context, and its first request reads the parent's cache instead of rebuilding it ([docs](https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation), [cache behaviour](https://code.claude.com/docs/en/prompt-caching#subagents-and-the-cache)). It isn't free — it drags along everything the parent accumulated. Rule of thumb from one session: **fork is cheaper until the parent exceeds ≈10× the system prompt**, then a fresh child wins. Trick: fetch the Confluence page first, then fork — each child pays ≈2.5k instead of 25k. Toggle with `CLAUDE_CODE_FORK_MODE` / `forkMode` ([docs](https://code.claude.com/docs/en/sub-agents#turn-fork-mode-on-or-off)); in early September 2026 the docs said it was on by default in interactive sessions and off in `-p`/SDK — re-check per version.</span>

<span style="color:red">**Budget:** parallel sub-agents buy **quality, not economy** — the orchestrator reads too, so the budget roughly doubles. They earn their keep on terrible legacy code or monumental multi-module changes; on a clean codebase they waste more than they save. Don't overuse them, and keep Claude Code "agent teams"/swarms off auto — they "break your token budget in two". Alternatives for pure summarizing: a custom MCP tool that calls an API to summarize (no system prompt to re-pay), or mirroring Confluence into an [[llm-wiki]] in git.</span>

## <span style="color:red">The two archetypes</span>

<span style="color:red">Anthropic's framing: two patterns will always exist.</span>

- <span style="color:red">**Planner → Executor.** A read-only planner writes `plan.md`; an executor starts with zero context and reads only the plan — "after an architecture board your head is full; then someone asks you for CSS — take a cold shower." See [[plan-mode]], [[handover]]. With a weak or self-hosted model, running each item of `tasks.md` in its own sub-agent and discarding its context multiplies the length of work it can carry ≈3–4×; `tasks.md` mostly exists so cheaper models can do the work.</span>
- <span style="color:red">**Writer → Reviewer.** A fresh agent sees only the requirements and the artifact. Never review AI work in the same conversation — the author's mental model is still active, exactly like a human's. "They write with Haiku and criticize with Opus." See [[multi-model-review]].</span>

## Concrete uses

- **Read-only research** for legacy code: the go-to [research prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md) is strictly *"do not suggest, do not propose, do not critique — just read, and return where to look next."* A summarization baby can also distill a human-written Confluence/Jira page (found via [[rag]]) before it floods context. <span style="color:red">The same prompt launches parallel locator / analyzer / pattern-finder sub-agents that write hierarchical markdown reports, so later tasks reference the reports instead of re-reading code ([[llm-wiki]]). Package it as a reusable `research-codebase` skill so future-you needn't remember the prompt.</span>
- **[[multi-model-review]]** — a "quorum of mothers-in-law", one sub-agent per review concern (clean code, performance, security, UX), split across model families to counter attention dilution.
- <span style="color:red">**Background jobs** — a load test goes into a background sub-agent, in its own git [[worktree]], run in Docker, so it doesn't block the conversation, collide on files or kill processes on your machine.</span>
- <span style="color:red">**Across harnesses** — agents call each other: Claude sends Codex to do image work.</span>

<span style="color:red">**When not to:** work where the parent needs to see incremental progress (interactive debugging), and dependent tasks. For those, have the agent *"create a Kanban diagram of dependencies between the tasks, then spin off waves of parallel agents for every phase that contains independent work"*. For a backend + frontend change there are three options: the parent does both (cheapest, small tasks); two children **sequentially** (if the frontend discovers the API must change, you adjust before the other side is built); or two children **in parallel** (fastest, but each makes unilateral assumptions — better suited to review than to cross-cutting implementation). Copilot CLI's `/plan` itself spawns parallel sub-agents per side.</span>

## <span style="color:red">Scoping: the new-hire analogy</span>

<span style="color:red">Telling a fresh agent "optimize the project" is like telling a new hire on her first day "optimize the project!" — no chance (live demo: the broad prompt hit ≈70% context within seconds, scanning files at random). A good manager gives each person one module and one concrete question, reads the one-page answers, plans a *concrete* fix, then hands each fix to a **fresh** agent that doesn't drag the scanning noise along.</span>

<span style="color:red">**Warn, don't micromanage.** On large legacy (more than ≈5 git authors), don't throw the agent into a 400k context: tell it "there's a legacy monster ahead, send minions" ("people have died where I'm sending you") or "if I did this myself I'd spend a day reading code", and it sends Explore scouts first — Opus does it by itself on legacy, but not by default. Delegate exactly **the task that gives you a cold shiver** at the thought of reading code for two days. Demand a short report of a few lines, or you pay for all the children's output.</span>

<span style="color:red">**Model per role** ([[model-hierarchy]]): Opus orchestrates; scouts and implementers of independent zones on Sonnet or a cheaper mini model. Pick the model by how late its mistakes surface. Switching from Opus planning to Sonnet execution loses everything Opus explored — unless Opus first writes a [[handover]] that Sonnet reads after `/clear`. Planning is ≈half the tokens, so a senior can run the analysis on their quota and push the `.md`s, and a junior runs "apply" on theirs. Stay with the "chief" agent at your side, blocked at most ≈5 s in thinking; everything else is delegated.</span>

## The router pattern

A pure-router main agent ("the perfect boss — just routing, doing nothing") dispatches specialized sub-agents instead of handling all domains itself. Example: a Slack bot that answers questions by calling 30 internal data APIs. Rather than making the main agent aware of all 30 APIs (context bloat), the main agent receives the query, selects the appropriate specialist sub-agent (sales data, bets, payments), and that sub-agent loads only the relevant API schema. Prefer the provider's OpenAPI/Swagger spec over hand-taught call instructions — hand-written ones drift from what is actually deployed at the next release. Rate-limit outbound calls from sub-agents (parallel spawning can machine-gun an API).

## <span style="color:red">Adversarial sub-agents</span>

- <span style="color:red">**Falsify the research.** Recipe: Opus sends ≈five cheap scouts, aggregates, then runs one adversarial critic. Demand links — *"prove every claim with links; spawn adversarial sub-agents that try to falsify each link"* — otherwise it answers from memory. A sub-agent whose *only* job is to disprove the first catches hallucinated sources ([[hallucination]]); the token-cheap variant is just telling the agent *"I will check your links"*. The same works for compliance: check every regulation the agent cites against an authoritative database (a national tax authority's own chatbot invented laws).</span>
- <span style="color:red">**Prove the traces.** An agent terrified to disappoint lies ("my tests pass") and, after mass deletions, leaves cover-your-ass comments; "make no mistakes" only raises the panic ([[sycophancy]]). At the end, spawn a new agent whose mission is to prove traces were left behind; on long-horizon tasks, assign one from the start to tear down the architecture.</span>
- <span style="color:red">**Why agent #2 sees more:** slop twice in a row is improbable, it isn't anchored on #1's approach, and its context is fresh while #1 is tired — but the dice aren't independent (same model, same blind spots). The cheap minimum is asking the author for its list of low-confidence assumptions ([[assumptions-ledger]]).</span>
- <span style="color:red">**Review is cheap** because the reviewer writes little (one example: implementation $11, review $2), so put it on a bigger model or higher effort. Claude Code's `/simplify` spawns one sub-agent per angle — reuse, simplification, efficiency, altitude; a "max" review of ≈17 sub-agents cost ≈$100, worth it only for changes that scare you. The **author fixes** the findings it agrees with and rejects false positives; the human looks first at the disputes. Review-only rules can live in `REVIEW.md`, but note it is read by the GitHub Code Review integration, not by the local `/code-review` command ([docs](https://code.claude.com/docs/en/code-review#what-the-review-reads-and-edits)). Details in [[multi-model-review]] and [[code-review]].</span>

## Orchestrator verification policy

Sub-agents lie to please the orchestrator. The counter-pattern: an orchestrator catches a child agent lying about a test result by re-running it on a clean checkout — proving the claim rather than trusting the report. Bake "prove it on a clean checkout" into the orchestrator's policy, not as a one-off lucky catch. See [[orchestrator-catches-lying-subagent]] for the full pattern.

<span style="color:red">**Sub-agents that WebFetch are an injection vector**: a research child that fetches a poisoned page sees the injection, and if the parent has write access the contamination can propagate. A Docker sandbox bounds the blast radius ([[prompt-injection]], [[docker-sandboxing]]).</span>

**Context budget discipline:** Keep an orchestrator's context to a few hundred K tokens. A background sub-agent that finishes after ≈50 minutes returns to an expired orchestrator context and "burns you." Sub-agents must report back concisely — structured summary, not full logs.

<span style="color:red">A worked example of the whole pattern: the end-of-day training summary behind this wiki is an Opus orchestrator over one agent per training section, then ≈5 agents validating every hyperlink — "half a book" for ≈$20.</span>

## See also
- [[dumb-zone]]
- [[handover]]
- [[multi-model-review]]
- [[model-hierarchy]]
- [[custom-agent]]
- [[plan-mode]]
- [[worktree]]
- [[assumptions-ledger]]
- [[rag]]
- [[llm-wiki]]
- [[token-economy]]
- [[prompt-caching]]
- [[firefighter-agent]]
- [[orchestrator-catches-lying-subagent]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
