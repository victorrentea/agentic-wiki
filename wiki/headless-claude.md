---
title: Headless Claude (claude -p / --resume)
category: tool
tags: [claude-code, headless, non-interactive, multi-turn, runtime]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
---

`claude -p` runs Claude Code in **non-interactive / headless mode**, enabling a product to fake multi-turn conversations without building a custom agent harness.

## The pattern

A company avoids building its own agent runtime by reusing the Claude Code CLI:

1. Kick off the first turn with `claude -p "…"`.
2. Display Claude's reply in the product's own UI.
3. Take the user's typed response.
4. Pipe it back with `claude --resume <session-id> "…"` to continue the existing conversation.

The product *looks* like it has a multi-turn agent — but it delegates all the hard parts (permission model, state/context management, tool plumbing, resume/retry logic) to the hardened CLI runtime. This is valuable precisely because building a reliable agent harness is dangerous work that is easy to get wrong.

## Why not the raw API?

Using the raw model API + a hand-rolled conversation loop gives you more control but forces you to own:
- Permission/approval flows for tool calls.
- Context compaction and handover.
- Resuming interrupted sessions.
- The security posture of any tools you wire in.

Reusing `claude -p` inherits all of that for free. The trade-off is that you're constrained to the CLI's feature set and must keep up with its version.

## Agent auth integration

When wiring to a backend, the headless session needs both a service-account API key (for the CLI itself) and a per-user bearer token injected into the [[mcp-transport]] headers each turn. See [[agent-auth]].

## See also

- [[harness]]
- [[agent-auth]]
- [[model-context-protocol]]
- [[ralph-loop]]
- [[agent-permissions]]
- [[2026-06-11-ai-playtika]]
