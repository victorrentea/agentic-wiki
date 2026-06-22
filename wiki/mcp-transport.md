---
title: MCP Transport
category: concept
tags: [mcp, transport, sse, http, json-rpc, streaming, stdio]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]"]
created: 2026-06-10
updated: 2026-06-12
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
