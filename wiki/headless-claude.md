---
title: Headless Claude (claude -p / --resume)
category: tool
tags: [claude-code, headless, non-interactive, multi-turn, runtime]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-12
updated: 2026-10-13
---

`claude -p` runs Claude Code in **non-interactive / headless mode**, enabling a product to fake multi-turn conversations without building a custom agent harness.

## The pattern

A company avoids building its own agent runtime by reusing the Claude Code CLI:

1. Kick off the first turn with `claude -p "…"`.
2. Display Claude's reply in the product's own UI.
3. Take the user's typed response.
4. Pipe it back with `claude --resume <session-id> "…"` to continue the existing conversation.

The product *looks* like it has a multi-turn agent — but it delegates all the hard parts (permission model, state/context management, tool plumbing, resume/retry logic) to the hardened CLI runtime. This is valuable precisely because building a reliable agent harness is dangerous work that is easy to get wrong. <span style="color:red">Restated in October 2026: *don't build a chatbot harness — `claude -p` with `--resume` already is one*: one web message = one `claude -p` turn, resumed by session ID ([[2026-10-09-ai-jax-london]]).</span>

## Why not the raw API?

Using the raw model API + a hand-rolled conversation loop gives you more control but forces you to own:
- Permission/approval flows for tool calls.
- Context compaction and handover.
- Resuming interrupted sessions.
- The security posture of any tools you wire in.

Reusing `claude -p` inherits all of that for free. The trade-off is that you're constrained to the CLI's feature set and must keep up with its version.

## <span style="color:red">Jira2PR: the first thing companies agentify</span>

<span style="color:red">The most common headless use case seen in companies is "Jira2PR" — a ticket goes in, a PR comes out — met under exactly that name at 8 companies. Before building it, check the native route: the [Claude GitHub App](https://code.claude.com/docs/en/github-actions) (`/install-github-app`) already does it — tag Claude on an issue or PR and it works on GitHub's machines ([[cloud-review-workflow]]). The lesson: **don't build a harness over models; build the extraction of business expertise into markdown** — the [[agent-skill|skills]] and [[agents-md|AGENTS.md]] the agent runs with are where your value is ([[2026-08-27-ai-it-services]], [[harness]]).</span>

## <span style="color:red">Whose key?</span>

<span style="color:red">A headless agent in CI (e.g. a PR reviewer via `claude -p`) raises the question *"on whose key?"* ([[2026-09-30-ai-enterprise]]). As of October 2026, Max 5x / 20x plans include $100 / $200 per month in API credits, but these apply only to API, Agent SDK or `claude -p` runs with a Console API key — not to interactive Claude Code sessions or extra usage ([support article](https://support.claude.com/en/articles/17154008-monthly-api-credits-for-max-and-team-plans)). A shared pipeline should run on a service-account key with its own budget, not on a developer's personal login ([[token-economy]], [[data-governance]]).</span>

## Agent auth integration

When wiring to a backend, the headless session needs both a service-account API key (for the CLI itself) and a per-user bearer token injected into the [[mcp-transport]] headers each turn. See [[agent-auth]].

## See also

- [[harness]]
- [[agent-auth]]
- [[model-context-protocol]]
- [[ralph-loop]]
- [[agent-permissions]]
- <span style="color:red">[[cloud-review-workflow]]</span>
- <span style="color:red">[[token-economy]]</span>
- [[2026-06-11-ai-mobile-gaming]]
