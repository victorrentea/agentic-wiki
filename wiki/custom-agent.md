---
title: Custom Agent
category: concept
tags: [sub-agents, specialist, model-pinning, restricted-tools, context-window, review]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-06-23-ai-device-maker]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-23
updated: 2026-10-13
---

A custom agent is a single-purpose specialist (e.g. a security reviewer, a clean-code maniac) defined with its **own context window**, a **pinned model**, and a **restricted tool set** — distinct from the project's ground-rules memory file.

## Custom agent vs `AGENTS.md`

- **`AGENTS.md`** = ground rules injected into *any* agent that touches that folder (see [[agents-md-discipline]]). It shapes every agent equally.
- **A custom agent** = a named role you invoke deliberately. It carries:
  - its **own context window** — it doesn't pollute the orchestrator's context, and isn't polluted by it;
  - a **pinned model** from the [[model-hierarchy]] (`haiku` for trivial, `opus` for long-horizon);
  - **restricted tools** — a security reviewer needs read + grep, not deploy.

## It's just a markdown file

<span style="color:red">**A custom agent is a markdown persona, not a rewritten agent loop** — identity, purpose, tools, model, in a `.md` file under `.claude/agents/`. "Never say you *developed* an agent" ([[2026-10-09-ai-jax-london]]). Rewriting Claude Code yourself is hubris: you rediscover bugs the harness already solved ([[2026-09-30-ai-enterprise]]).</span>

<span style="color:red">Mechanics: frontmatter `description` (when it auto-activates), `tools` and `model`; start it as the main agent with `claude --agent <name>` (or `/agent <name>`), or ask a running chat to spawn it ([[2026-08-03-ai-telecom-ops-qa]], [[2026-10-09-ai-jax-london]]). Example: [reviewer-efficiency.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-efficiency.md) — one concern only, Opus, no web search; it deliberately ignores 100-line methods because those are not its concern.</span>

<span style="color:red">Live gotcha: **without a date/time tool the agent doesn't know what time it is** — one couldn't schedule anything "in one hour" ([[2026-08-03-ai-telecom-ops-qa]]).</span>

## Sub-agent, skill, custom agent

<span style="color:red">Two concepts hide under one name. A **sub-agent** is a spawned background worker with isolated context that needs spoon-fed instructions and reports back to its parent; a **custom agent** is a persistent persona file (rules, tool selection, pinned model) that can be auto-activated by its description — and can itself run as a sub-agent ([[2026-04-16-ai-safety-critical]]).</span>

<span style="color:red">Four ways to share a prompt, ordered by when it loads ([[2026-04-07-ai-product-team]], [[2026-04-27-ai-healthcare-software]]):</span>

| <span style="color:red">Mechanism</span> | <span style="color:red">Loads</span> | <span style="color:red">Shape</span> |
|---|---|---|
| <span style="color:red">[[claude-md]]</span> | <span style="color:red">always, every boot</span> | <span style="color:red">ground rules</span> |
| <span style="color:red">slash command</span> | <span style="color:red">on demand, manual `/x`</span> | <span style="color:red">a saved prompt</span> |
| <span style="color:red">[[agent-skill]]</span> | <span style="color:red">lazily, when its description matches</span> | <span style="color:red">knowledge poured into the current context</span> |
| <span style="color:red">custom agent</span> | <span style="color:red">spawned by a human or a parent</span> | <span style="color:red">own context, model and tools; returns a summary</span> |

<span style="color:red">*"A skill is poured-in knowledge — you can't unpour it. A custom agent is a separate process with its own memory."* Load 20 skills and the agent knows everything but nothing well; a custom agent is "born with a single goal in life" (reuse hunter, performance maniac, UX obsessive). So three review concerns — clean code, performance, security — should be three agents, not three skills pouring into one context ([[2026-04-27-ai-healthcare-software]], [[2026-07-03-ai-bank]]). **Progression: start with skills, then materialise the recurring ones into dedicated single-purpose agents.**</span>

## Why it's useful

Custom agents are the named, reusable form of the [[sub-agents]] pattern: instead of spawning an ad-hoc worker, you keep a stable cast of specialists. The restricted tool surface keeps each one at the right altitude (a reviewer that can't write code can't "fix" instead of flag), and the pinned model encodes the cost/quality trade-off so it isn't improvised each run. A review pipeline assembles several of them — one per concern — into a [[multi-model-review]] quorum.

Their boundaries are a [[token-economy]] win (no cross-contamination) and a safety win (least-privilege tools, the same instinct as [[agent-permissions]]).

## Tool restriction as a security boundary

<span style="color:red">**If you gave it `bash`, you gave it everything.** The closer an agent sits to production, the more you cut from its toolbox ([[2026-08-03-ai-telecom-ops-qa]]). Worked examples:</span>

- <span style="color:red">**HTML-scraper agent as a [[prompt-injection]] filter** — on the cheapest model (Haiku), allowed only `curl localhost:*`, it follows links and returns a **sitemap** (structured data) to the parent instead of raw HTML. Poisoned page content can't escape its toolbox, and the parent's context stays clean. Gotcha: Claude first put it in the skills folder instead of an agent definition ([[2026-04-07-ai-product-team]]). See [[geo-steganography]].</span>
- <span style="color:red">**Read-only `architect` reviewer**, context-forked, with Read only — deliberately no WebFetch: "I don't want to download hacks from the internet" ([[2026-07-01-ai-java-community]]).</span>
- <span style="color:red">**Support chatbot** with only the application's MCP tools — no Bash, no WebFetch — which booked a real vet visit through `create_visit` ([[2026-09-30-ai-enterprise]]).</span>

<span style="color:red">For MCP-backed agents, the MCP server receives the already-authenticated user (`Authorization: Bearer <JWT>`, on-behalf-of — see [[jwt-identity]]), and `.mcp.json` can be **generated at boot outside the folder the agent can read**, so the agent never sees its own credentials ([[2026-08-03-ai-telecom-ops-qa]]; see [[secret-zero]]).</span>

## See also
- [[sub-agents]]
- [[agents-md-discipline]]
- [[model-hierarchy]]
- [[multi-model-review]]
- [[agent-skill]]
- [[agent-permissions]]
- <span style="color:red">[[claude-md]]</span>
- <span style="color:red">[[prompt-injection]]</span>
- <span style="color:red">[[jwt-identity]]</span>
- <span style="color:red">[[secret-zero]]</span>
- <span style="color:red">[[harness]]</span>
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-04-07-ai-product-team]]</span>
- <span style="color:red">[[2026-04-27-ai-healthcare-software]]</span>
- <span style="color:red">[[2026-07-03-ai-bank]]</span>
- <span style="color:red">[[2026-08-03-ai-telecom-ops-qa]]</span>
- <span style="color:red">[[2026-09-30-ai-enterprise]]</span>
- <span style="color:red">[[2026-10-09-ai-jax-london]]</span>
