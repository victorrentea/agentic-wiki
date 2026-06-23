---
title: Agentic Corporation (Blackboard / Dark Factory)
category: pattern
tags: [multi-agent, blackboard, async, issue-tracker, future-of-work, orchestration]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-23
new_in: 2026-06-23
---

<span style="color:red">🆕 New in Garmin 2026-06-23</span>

The agentic corporation is the horizon where agents coordinate *through shared artifacts* rather than through humans — the issue tracker becomes a "blackboard" agents read from and write to asynchronously.

## The blackboard pattern

When an agent hits a genuine ambiguity it can't resolve, the move isn't to block on a human in the chat — it's to **post a clarifying comment on the issue** and move on. *"In a truly agentic corporation, another agent picks that up and answers asynchronously; the issue is just the blackboard."* The tracker (GitHub/Jira) is the shared memory: requirements, clarifications, and decisions accumulate there, where any agent or human can act on them later. This is why you **connect the agent to the issue tracker instead of retyping the ticket** (`i want to work on gh issue #1`) — see [[cli-vs-mcp-tradeoff]] for preferring the `gh` CLI over a heavier MCP.

The blackboard is the natural endpoint of [[grill-me|grilling]]: the questions a grill surfaces get posted for the business to answer async, rather than stalling the run.

## The "dark factory"

The provocative real-world signal: a security-software company announced (early 2026) they *"don't read code anymore"* — requirements in on the left, code out on the right, humans only tuning knobs. The **"dark factory"** is the manufacturing metaphor (a lights-out factory with no humans on the floor). Whether or not it generalizes, it marks the direction of travel the rest of this wiki guards against running blindly: it only works on top of disciplined [[spec-driven-development]], [[acceptance-test-bdd|acceptance tests]], [[multi-model-review]], and the anti-[[cognitive-debt]] safeguards — not raw [[vibe-coding]].

## Supervisor multi-agent layers

Even today's harnesses hint at it: an "Autopilot supervisor" that auto-approves an agent's choices is itself a supervisor-LLM multi-agent pattern — and a weak supervisor (a cheap model rubber-stamping sloppy reasoning — *"I smell Haiku!"*) is its failure mode. The same orchestrator/specialist structure powers [[sub-agents]] and the [[firefighter-agent]]; the agentic corporation is that structure scaled to a whole org.

## See also
- [[sub-agents]]
- [[cli-vs-mcp-tradeoff]]
- [[grill-me]]
- [[spec-driven-development]]
- [[cognitive-debt]]
- [[firefighter-agent]]
- [[model-hierarchy]]
- [[2026-06-23-ai-garmin]]
