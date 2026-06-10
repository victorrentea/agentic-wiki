---
title: Elicitation
category: concept
tags: [mcp, human-in-the-loop, safety, agent-control]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
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

## See also

- [[model-context-protocol]]
- [[mcp-transport]]
- [[sampling]]
- [[tool-calling]]
- [[guardrails]]
- [[2026-06-10-spring-ai-itkonekt]]
