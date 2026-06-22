---
title: Dynamic Tool Discovery
category: concept
tags: [mcp, tool-calling, protocol, discoverability]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
---

[[model-context-protocol]] has no Swagger or WSDL — the LLM asks the server *"what tools do you have?"* at connection time and receives their names, descriptions, and JSON-schema parameters on the fly.

## How it works

On connect, the MCP client queries the server's `tools/list` endpoint. The server returns a list of tool definitions with names, human-readable descriptions, and JSON Schema for each parameter set. The agent then holds this capability inventory for the session — no out-of-band API spec needed.

## Pitfall: too many tools

Exposing dozens of tools at once confuses the model — it mis-selects tools or hallucinates non-existent parameters. The practical fix is to add **MCP Prompts** on top: prompt templates that teach the agent which tools to sequence for common tasks, reducing the decision space.

This also motivates the [[cli-vs-mcp-tradeoff]]: a CLI the model already knows from training data requires zero discovery tokens, whereas a bespoke MCP forces runtime discovery of every tool on every session start.

## Teaching new CLIs

Dynamic discovery is also the mechanism for **teaching an agent a CLI it doesn't know yet** — wrap the CLI calls behind MCP tools with good descriptions, and the agent learns the interface without any training-data knowledge. This is a bridge pattern: use it until a newer base model ships that already knows the CLI natively.

## See also

- [[model-context-protocol]]
- [[mcp-transport]]
- [[cli-vs-mcp-tradeoff]]
- [[elicitation]]
- [[token-economy]]
- [[2026-06-11-ai-playtika]]
