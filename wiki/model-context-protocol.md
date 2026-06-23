---
title: Model Context Protocol
category: concept
tags: [mcp, tool-calling, agent-integration, protocol]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-10
updated: 2026-06-23
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

stdio MCPs run as **child processes** of the agent, communicating over stdin/stdout. Because they inherit the agent's sandbox, you can apply filesystem and network isolation to them — a meaningful risk reduction when the MCP is a third-party `npx` package. See the [Claude Code sandboxing docs](https://code.claude.com/docs/en/security) and [[mcp-sandbox-inheritance]].

## Remote MCPs

Remote MCPs (SSE / Streamable HTTP) live outside the agent's sandbox and must be treated as external services: they need their own authentication, authorization, and rate-limiting. The agent's [[os-sandbox]] does not extend to a remote MCP process.

## Dynamic tool discovery

No Swagger is needed — the agent asks the server `tools/list` at connect time. See [[dynamic-tool-discovery]] for pitfalls (too many tools confuse the model) and the [[cli-vs-mcp-tradeoff]] for when a CLI is cheaper.

## Agent authentication

Two credentials travel together on an MCP call: a **service-account API key** (app identity) and a **bearer token** (user identity). See [[agent-auth]] for the full pattern.

The full protocol spec lives at [modelcontextprotocol.io](https://modelcontextprotocol.io/).

## Integration "paradise" — and its blast radius

<span style="color:red">Real MCP integrations are the road to a self-driving developer: a [[figma-mcp|Figma MCP]] (designs straight into the app, *if* they're truly maintained there), [[adb-automation|`adb`]] for mobile UI loops, a Jira MCP (e.g. mcp-atlassian) or the Atlassian CLI, and a TeamCity/CI CLI. Two cautions: when a CLI already covers it, the CLI is far cheaper than the MCP ([[cli-vs-mcp-tradeoff]] — the GitHub MCP burns ≈1,300 tokens for what `gh` does free); and **mind which checkboxes you tick when you mint the agent's API key** — don't hand it the power to delete a Jira project or take down prod ([[agent-permissions]]).</span>

## See also

- [[mcp-transport]]
- [[elicitation]]
- [[sampling]]
- [[tool-calling]]
- [[jwt-identity]]
- [[supply-chain-attack]]
- [[mcp-sandbox-inheritance]]
- [[dynamic-tool-discovery]]
- [[cli-vs-mcp-tradeoff]]
- [[agent-auth]]
- [[figma-mcp]]
- [[adb-automation]]
- [[agent-permissions]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
