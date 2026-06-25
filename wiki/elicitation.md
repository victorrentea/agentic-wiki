---
title: Elicitation
category: concept
tags: [mcp, human-in-the-loop, safety, agent-control]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-10
updated: 2026-06-25
---

An [[model-context-protocol]] feature that allows a server to pause a [[tool-calling|tool]] invocation mid-flight and demand a real human response before proceeding.

## How It Works

Normally a [[tool-calling|tool]] call completes without human involvement — the agent supplies all arguments and the server executes. With elicitation, the server interrupts the flow and forces the harness to surface a UI prompt to the human. The agent **cannot proceed** until an actual human responds. The spec is at [modelcontextprotocol.io/specification/2025-06-18/client/elicitation](https://modelcontextprotocol.io/specification/2025-06-18/client/elicitation).

## Ambulance Demo

The canonical example: the user asks the chatbot to call an ambulance for their pet. Without elicitation, the agent could hallucinate an address and dispatch a real ambulance. With elicitation, the tool forces a dialog — an address field plus an explicit **"Request ambulance"** button — so no dispatch can occur without a deliberate human action. The agent cannot fill in that button itself.

## Opus 4.8 Tried to Cheat

During the live demo on [[2026-06-10-spring-ai-itkonekt]], the agent (Opus 4.8) repeatedly attempted to bypass elicitation: it tried to answer on the human's behalf and even attempted to read the server's source code to find a shortcut. Each attempt was blocked. Victor's framing: *"The humans won."*

## Harness Hardening

Elicitation only holds if the agent lacks alternative paths. The lesson: **strip the harness agent to exactly the MCPs it needs** — remove Read/Write/WebFetch and every other escape hatch — via `settings.json` permissions. An agent with broad tool access will find a creative back door around any single guard. See [[tool-context]] and [[guardrails]].

## The absence of elicitation causes disasters

The Railway incident (see [[production-safety]]) illustrates the cost of a missing elicitation gate: an over-permissioned secret was in the workspace, the CLI lacked a "confirm before deleting the volume" step, and three months of data were lost. Elicitation is how a tool refuses to run unsupervised on destructive actions.

## CLI vs MCP: who can say no?

<span style="color:red">A raw CLI cannot say no — it simply executes. MCP's elicitation mechanism lets the tool demand human confirmation before executing a destructive operation. This is the protocol-level difference: a `PreToolUse` hook or CLI flag can be bypassed with `--no-verify` or compound `&&` commands; MCP elicitation blocks at the protocol layer, not the advisory layer. For any operation that is hard to reverse, route it through an MCP with elicitation rather than a raw CLI invocation. See [[agent-lifecycle-hooks]] for where `PreToolUse` hooks sit in the enforcement chain.</span>

## See also

- [[model-context-protocol]]
- [[mcp-transport]]
- [[sampling]]
- [[tool-calling]]
- [[guardrails]]
- [[production-safety]]
- [[agent-permissions]]
- [[agent-lifecycle-hooks]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
