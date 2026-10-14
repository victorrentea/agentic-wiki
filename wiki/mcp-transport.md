---
title: MCP Transport
category: concept
tags: [mcp, transport, sse, http, json-rpc, streaming, stdio]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-08-ai-security-tools]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-31-ai-software-vendor]]"]
created: 2026-06-10
updated: 2026-10-13
---

The wire layer that carries [[model-context-protocol]] messages between agent and server, built on HTTP and Server-Sent Events rather than a bidirectional socket.

## <span style="color:red">The one distinction that matters</span>

<span style="color:red">The whole MCP taxonomy reduces to one question: **who starts the server?**</span>

- <span style="color:red">**stdio** — the harness spawns the server as its own child process on a pipe (`npx some-mcp@latest`). Four open terminals = four `npx` processes in memory; three Claude instances = three MCP processes. "Connected" in `/mcp` only means the process answered.</span>
- <span style="color:red">**Streamable HTTP** — you attach to something that is already running: a hosted server like [[context7|Context7]], or your own app's MCP endpoint on `localhost:8080`.</span>

<span style="color:red">In spec revision 2025-06-18 these are the only two standard transports; the original "HTTP + SSE" pair described below is legacy. Inventory and toggle servers with `/mcp list`, `/mcp enable <server>`, `/mcp disable <server>`.</span>

<span style="color:red">**Rule: a local MCP is stdio started by the harness**, not a process you keep alive by hand. One team's IDE plugin booted MCPs inside manually started Docker containers and was flaky ("tool not found", container identity lost on IDE restart); expecting several agents to share one Docker-hosted local MCP is the wrong mental model. "Remote" is legitimate only when the server really is remote.</span>

## HTTP + SSE Shape

<span style="color:red">(The legacy transport — still widely deployed, and the clearest way to see what Streamable HTTP later unified.)</span> The endpoint returns `Content-Type: text/event-stream` — a [Server-Sent Event](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events) stream. The resulting shape on the wire is asymmetric:

- **One persistent GET** — the SSE channel the server holds open and pushes notifications down.
- **Many short POSTs** — one per outgoing message from the client.

[JSON-RPC](https://www.jsonrpc.org/specification) sits on top: every request carries an **`id`** field so that responses arriving asynchronously on the push channel can be correlated to the originating call. Because every POST is a separate HTTP request, per-request headers fire on each one — making headers the clean place to carry out-of-band identity (see [[jwt-identity]]).

## SSE vs WebSocket

SSE is unidirectional (server → client only), which is exactly the MCP pattern: the server streams results while the client POSTs requests. The practical advantage over WebSockets is that SSE is **plain HTTP**, so it passes through corporate proxies, load balancers, and auth layers that frequently choke on WebSocket upgrade handshakes.

## Streamable HTTP

[Streamable HTTP](https://modelcontextprotocol.io/specification/2025-03-26/basic/transports) is the newer MCP transport that supersedes the original HTTP+SSE design. It unifies the two-channel model into a single connection. The HTTP+SSE transport is still widely deployed but should be considered legacy for new implementations.

## <span style="color:red">Stateless MCP (spec 2026-07-28)</span>

<span style="color:red">The [2026-07-28 spec revision](https://modelcontextprotocol.io/specification/2026-07-28/changelog) makes the protocol itself stateless: it removes the `initialize` handshake and the `Mcp-Session-Id` header, so **any request can land on any instance** behind a plain load balancer ([announcement](https://blog.modelcontextprotocol.io/posts/2026-07-28/)). Old SSE servers that pin a client to a persistent connection don't scale horizontally. Nuance: SDKs offered a `stateless_http=True` mode since May 2025 — what is new is that the protocol became plain request/response. When you have an agent build a server, ask explicitly for this revision; its training data defaults to older ones ([[knowledge-cutoff]]).</span>

## Local stdio transport

The second major transport is **local stdio**: the MCP client (the agent harness) spawns the MCP server as a child process and communicates over stdin/stdout. This is the transport used by tools like Playwright MCP and code-graph. The key security property: a stdio MCP is a child process, so it inherits the agent's [[os-sandbox]] — see [[mcp-sandbox-inheritance]].

Remote (SSE/HTTP) MCPs live outside the agent's sandbox and need their own authz and rate-limiting. Local stdio MCPs inherit the agent's sandbox automatically — this is the security-relevant distinction between the two transports.

## Token cost of tool descriptions

Every tool a server advertises has a description that is loaded into the agent's context at connection time — this is the token cost of MCP vs CLI. When a server exposes dozens of tools, the descriptions alone can consume hundreds of tokens per turn, and too many tools lead the agent to pick the wrong one (e.g. confusing Playwright MCP with Chrome DevTools MCP). Design principle: **expose only the tools the agent will actually use in a given workflow**, and use [[progressive-disclosure]] to load specialized toolsets on demand. See [[cli-vs-mcp-tradeoff]] for the quantified comparison. <span style="color:red">Some APIs are notorious context bloat through any MCP — Jira is the usual example — and are better wrapped as a small CLI (REST under `sh`) the agent composes with `grep` and pipes ([[internal-tool-cli-skill]]).</span>

MCP servers can also request **human-in-the-loop** approval via the [[elicitation]] mechanism — *"you're about to delete your database — are you sure?"* — making destructive operations safe to expose without removing them from the agent's toolbox.

## See also

- [[model-context-protocol]]
- [[elicitation]]
- [[sampling]]
- [[jwt-identity]]
- [[tool-calling]]
- [[mcp-sandbox-inheritance]]
- [[agent-auth]]
- <span style="color:red">[[cli-vs-mcp-tradeoff]]</span>
- <span style="color:red">[[internal-tool-cli-skill]]</span>
- <span style="color:red">[[context7]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
- <span style="color:red">[[2026-08-03-ai-telecom-ops-qa]]</span>
- <span style="color:red">[[2026-08-06-ai-bank]]</span>
- <span style="color:red">[[2026-08-31-ai-software-vendor]]</span>
