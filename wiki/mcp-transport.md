---
title: MCP Transport
category: concept
tags: [mcp, transport, sse, http, json-rpc, streaming, stdio]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-10
updated: 2026-06-24
---

The wire layer that carries [[model-context-protocol]] messages between agent and server, built on HTTP and Server-Sent Events rather than a bidirectional socket.

## HTTP + SSE Shape

The endpoint returns `Content-Type: text/event-stream` — a [Server-Sent Event](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events) stream. The resulting shape on the wire is asymmetric:

- **One persistent GET** — the SSE channel the server holds open and pushes notifications down.
- **Many short POSTs** — one per outgoing message from the client.

[JSON-RPC](https://www.jsonrpc.org/specification) sits on top: every request carries an **`id`** field so that responses arriving asynchronously on the push channel can be correlated to the originating call. Because every POST is a separate HTTP request, per-request headers fire on each one — making headers the clean place to carry out-of-band identity (see [[jwt-identity]]).

## SSE vs WebSocket

SSE is unidirectional (server → client only), which is exactly the MCP pattern: the server streams results while the client POSTs requests. The practical advantage over WebSockets is that SSE is **plain HTTP**, so it passes through corporate proxies, load balancers, and auth layers that frequently choke on WebSocket upgrade handshakes.

## Streamable HTTP

[Streamable HTTP](https://modelcontextprotocol.io/specification/2025-03-26/basic/transports) is the newer MCP transport that supersedes the original HTTP+SSE design. It unifies the two-channel model into a single connection. The HTTP+SSE transport is still widely deployed but should be considered legacy for new implementations.

## Local stdio transport

The second major transport is **local stdio**: the MCP client (the agent harness) spawns the MCP server as a child process and communicates over stdin/stdout. This is the transport used by tools like Playwright MCP and code-graph. The key security property: a stdio MCP is a child process, so it inherits the agent's [[os-sandbox]] — see [[mcp-sandbox-inheritance]].

Remote (SSE/HTTP) MCPs live outside the agent's sandbox and need their own authz and rate-limiting. Local stdio MCPs inherit the agent's sandbox automatically — this is the security-relevant distinction between the two transports.

## Token cost of tool descriptions

Every tool a server advertises has a description that is loaded into the agent's context at connection time — this is the token cost of MCP vs CLI. When a server exposes dozens of tools, the descriptions alone can consume hundreds of tokens per turn, and too many tools lead the agent to pick the wrong one (e.g. confusing Playwright MCP with Chrome DevTools MCP). Design principle: **expose only the tools the agent will actually use in a given workflow**, and use [[progressive-disclosure]] to load specialized toolsets on demand. See [[cli-vs-mcp-tradeoff]] for the quantified comparison.

MCP servers can also request **human-in-the-loop** approval via the [[elicitation]] mechanism — *"you're about to delete your database — are you sure?"* — making destructive operations safe to expose without removing them from the agent's toolbox.

## See also

- [[model-context-protocol]]
- [[elicitation]]
- [[sampling]]
- [[jwt-identity]]
- [[tool-calling]]
- [[mcp-sandbox-inheritance]]
- [[agent-auth]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
