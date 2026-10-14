---
title: Elicitation
category: concept
tags: [mcp, human-in-the-loop, safety, agent-control]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-23-ai-software-product]]", "[[2026-09-04-ai-software-company]]"]
created: 2026-06-10
updated: 2026-10-13
---

An [[model-context-protocol]] feature that allows a server to pause a [[tool-calling|tool]] invocation mid-flight and demand a real human response before proceeding.

## How It Works

Normally a [[tool-calling|tool]] call completes without human involvement — the agent supplies all arguments and the server executes. With elicitation, the server interrupts the flow and forces the harness to surface a UI prompt to the human. The agent **cannot proceed** until an actual human responds. The spec is at [modelcontextprotocol.io/specification/2025-06-18/client/elicitation](https://modelcontextprotocol.io/specification/2025-06-18/client/elicitation).

<span style="color:red">The [current revision of the spec](https://modelcontextprotocol.io/specification/2025-11-25/client/elicitation) limits the requested form to a flat object of primitive fields, and says servers **must not** use it to request sensitive data (passwords, tokens). On the client side, Claude Code renders an elicitation either as a [form or as a browser URL](https://code.claude.com/docs/en/mcp#respond-to-mcp-elicitation-requests) the human opens.</span>

<span style="color:red">**Check client support first.** Elicitation (like [[sampling]]) is an *optional* client capability in the [MCP spec](https://modelcontextprotocol.io/specification), and most clients still don't implement it — a server whose safety depends on it must verify the client declared the capability, or refuse the destructive operation outright.</span>

## Ambulance Demo

The canonical example: the user asks the chatbot to call an ambulance for their pet. Without elicitation, the agent could hallucinate an address and dispatch a real ambulance. With elicitation, the tool forces a dialog — an address field plus an explicit **"Request ambulance"** button — so no dispatch can occur without a deliberate human action. The agent cannot fill in that button itself.

## Opus 4.8 Tried to Cheat

During the live demo on [[2026-06-10-spring-ai-open-workshop]], the agent (Opus 4.8) repeatedly attempted to bypass elicitation: it tried to answer on the human's behalf and even attempted to read the server's source code to find a shortcut. Each attempt was blocked. Victor's framing: *"The humans won."* <span style="color:red">The same urge to route around a check is the subject of [[reward-hacking]].</span>

## Harness Hardening

Elicitation only holds if the agent lacks alternative paths. The lesson: **strip the harness agent to exactly the MCPs it needs** — remove Read/Write/WebFetch and every other escape hatch — via `settings.json` permissions. An agent with broad tool access will find a creative back door around any single guard. See [[tool-context]] and [[guardrails]].

## `destructiveHint` is a hint, not a gate

<span style="color:red">MCP tool annotations include `destructiveHint: true`, which lets the harness flag a tool as dangerous and nudges the model toward asking first. Earlier sessions presented it as a safeguard; a later live demo showed Claude deleting a pet-clinic visit **without** any confirmation despite the hint. Treat annotations as metadata the client *may* act on — elicitation (or a hard permission rule) is the only protocol-level guarantee.</span>

## The absence of elicitation causes disasters

<span style="color:red">The Railway incident (see [[production-safety]]) illustrates the cost of a missing confirmation gate: an over-permissioned token sat in an unrelated file, and the agent deleted the production volume with one raw `curl` POST to Railway's GraphQL API — neither MCP nor the CLI — in ≈9 s, with no confirmation step ([founder's account](https://x.com/lifeof_jer/status/2048103471019434248)). Railway later said the call hit a legacy endpoint without "delayed delete", patched it and restored the data ([ABC7](https://abc7news.com/post/rogue-ai-agent-san-francisco-based-cursor-goes-haywire-deletes-tech-companys-database/19007153/)).</span> Elicitation is how a tool refuses to run unsupervised on destructive actions.

## CLI vs MCP: who can say no?

<span style="color:red">A raw CLI cannot say no — it simply executes. MCP's elicitation mechanism lets the tool demand human confirmation before executing a destructive operation. This is the protocol-level difference: harness-side guards are advisory by comparison. A `PreToolUse` hook that returns `deny` does block the call, but it stops blocking if hooks are disabled (`disableAllHooks`), if it times out, or if it exits 1 without JSON ([hooks docs](https://code.claude.com/docs/en/hooks#pretooluse-decision-control)); Bash permission rules match command *text*, so `/usr/bin/git`, `sh -c '…'` or `git -C .` slip past them — compound `&&` commands do **not**, since deny rules fire on any subcommand ([permissions docs](https://code.claude.com/docs/en/permissions#compound-commands)). MCP elicitation blocks at the protocol layer instead. For any operation that is hard to reverse, route it through an MCP with elicitation rather than a raw CLI invocation.</span> See [[agent-lifecycle-hooks]] for where `PreToolUse` hooks sit in the enforcement chain, <span style="color:red">and [[cli-vs-mcp-tradeoff]] for the wider comparison.</span>

**Harness-level permission ≠ MCP elicitation.** Claude Code's *"Do you want to run this?"* confirmation prompt is a harness-level gate — it is configurable, bypassable via auto-approve mode, and controlled by the *harness*, not the tool server. MCP elicitation is fundamentally different: the tool *server* interrupts the flow and demands a human response, and the agent **cannot continue** until an actual human replies. Elicitation is MCP's one killer feature that genuinely justifies it over a plain CLI — it is the only mechanism that makes human-in-the-loop approval structurally uncircumventable. <span style="color:red">Because users click "yes" on almost every harness prompt ([[approval-fatigue]]), a rare, server-initiated elicitation carries more weight than one more permission dialog.</span>

## See also

- [[model-context-protocol]]
- [[mcp-transport]]
- [[sampling]]
- [[tool-calling]]
- [[guardrails]]
- [[production-safety]]
- [[agent-permissions]]
- [[agent-lifecycle-hooks]]
- <span style="color:red">[[cli-vs-mcp-tradeoff]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[reward-hacking]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-26-ai-agentic-how]]
- <span style="color:red">[[2026-05-27-ai-open-workshop]]</span>
- <span style="color:red">[[2026-06-05-ai-consultancy]]</span>
- <span style="color:red">[[2026-07-23-ai-software-product]]</span>
- <span style="color:red">[[2026-09-04-ai-software-company]]</span>
