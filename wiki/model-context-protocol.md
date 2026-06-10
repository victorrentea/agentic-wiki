---
title: Model Context Protocol
category: concept
tags: [mcp, tool-calling, agent-integration, protocol]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

An open protocol that connects agents to external tools and data by exposing three primitive types over a standard wire format.

## What MCP Exposes

An MCP server publishes three primitive types to the connecting agent:

- **Resources** — static reference data (e.g. a list of veterinary specialties). Read-only, no parameters.
- **Tools** — named, described, parameterised methods the agent can invoke (e.g. `createVisit`, `cancelVisit`, `callAmbulance`). Tools are self-describing: the server lists them with their full JSON Schema on connect, so **no Swagger/WSDL is needed** — the agent discovers capabilities dynamically without a separate API spec.
- **Prompts** — reusable prompt templates; roughly equivalent to a [[grill-me|skill]] exposed over the wire.

## Trust Boundary

The API key authenticates the **application** (a service account), not the human. One persistent connection is multiplexed across all concurrent user sessions, so the backend sees "the chatbot app is calling" but not *which* human. Propagating real user identity must happen **out-of-band via a request header** — never as a [[tool-calling|tool]] argument, because arguments are model-chosen and forgeable. See [[jwt-identity]] and [[dual-leg-rule]].

## Local (stdio) MCPs

stdio MCPs run as **child processes** of the agent, communicating over stdin/stdout. Because they inherit the agent's sandbox, you can apply filesystem and network isolation to them — a meaningful risk reduction when the MCP is a third-party `npx` package. See the [Claude Code sandboxing docs](https://code.claude.com/docs/en/security).

The full protocol spec lives at [modelcontextprotocol.io](https://modelcontextprotocol.io/).

## See also

- [[mcp-transport]]
- [[elicitation]]
- [[sampling]]
- [[tool-calling]]
- [[jwt-identity]]
- [[supply-chain-attack]]
- [[2026-06-10-spring-ai-itkonekt]]
