---
title: Model Context Protocol
category: concept
tags: [mcp, tool-calling, agent-integration, protocol, mcp-gateway]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-10
updated: 2026-10-13
---

An open protocol that connects agents to external tools and data by exposing three primitive types over a standard wire format.

<span style="color:red">MCP is an **API written for the agent**, not for a browser. It dates from November 2024. Note where the intelligence sits: **the LLM lives in the MCP client** (Claude Code, Copilot), not the server — the server is just a process with tools.</span>

## What MCP Exposes

An MCP server publishes three primitive types to the connecting agent:

- **Resources** — static reference data (e.g. a list of veterinary specialties). Read-only, no parameters. <span style="color:red">Roughly a GET.</span>
- **Tools** — named, described, parameterised methods the agent can invoke (e.g. `createVisit`, `cancelVisit`, `callAmbulance`). Tools are self-describing: the server lists them with their full JSON Schema on connect, so **no Swagger/WSDL is needed** — the agent discovers capabilities dynamically without a separate API spec. <span style="color:red">Roughly POST/PUT/DELETE — potentially destructive.</span>
- <span style="color:red">**Prompts** — reusable prompt templates; roughly equivalent to a [[agent-skill|skill]] exposed over the wire.</span>

## <span style="color:red">Why MCP and not plain REST or a CLI</span>

<span style="color:red">REST wasn't designed for AI (verbose payloads) and scraping HTML burns tokens. What MCP adds, as sessions listed it through 2026: tools that **self-publish** with descriptions that steer when to use what; **response streaming** the agent can abort mid-stream ("stop after N rows"); [[elicitation]] (server-initiated human approval mid-call); prompt templates; [[sampling]] (the server borrows the client's LLM without needing its own key — rarely implemented, security-sensitive); and **[authorization](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization)** — OAuth in the browser with transient tokens instead of API keys.</span>

<span style="color:red">The autumn-2026 consensus narrowed this to **three callbacks into your session — elicitation, sampling and authorization**. Without one of those, a script on the API, a well-documented OpenAPI the agent can `curl`, or a known CLI does the job more cheaply: a stdio MCP is technically just a CLI. The detailed trade-off is in [[cli-vs-mcp-tradeoff]].</span>

## Trust Boundary

The API key authenticates the **application** (a service account), not the human. One persistent connection is multiplexed across all concurrent user sessions, so the backend sees "the chatbot app is calling" but not *which* human. Propagating real user identity must happen **out-of-band via a request header** — never as a [[tool-calling|tool]] argument, because arguments are model-chosen and forgeable. See [[jwt-identity]] and [[dual-leg-rule]]. <span style="color:red">With MCP authorization, sub-agents can obtain on-behalf-of tokens on the fly from the identity provider without re-login; sessions recommended lifetimes of ≈5 minutes or one-shot tokens.</span>

<span style="color:red">**JWT scopes hide or show whole tools, never single fields** — field-level and row-level authorization is the server's job ([[mcp-tenant-isolation]]).</span>

## Local (stdio) MCPs

stdio MCPs run as **child processes** of the agent, communicating over stdin/stdout. Because they inherit the agent's sandbox, you can apply filesystem and network isolation to them — a meaningful risk reduction when the MCP is a third-party `npx` package. See the [Claude Code sandboxing docs](https://code.claude.com/docs/en/security) and [[mcp-sandbox-inheritance]].

<span style="color:red">**stdio servers are booted per client:** 12 Claude Code sessions = 12 copies of the server (3 sessions = 3 Playwright processes). Keep stdio servers lightweight; a Streamable HTTP server runs once and is shared.</span>

## Remote MCPs

Remote MCPs (SSE / Streamable HTTP) live outside the agent's sandbox and must be treated as external services: they need their own authentication, authorization, and rate-limiting. The agent's [[os-sandbox]] does not extend to a remote MCP process. <span style="color:red">Streamable HTTP has replaced SSE as the transport for already-running servers (SSE itself is an HTML5-era standard from ≈2011, much older than MCP). Context7 is a typical shared remote server ([[context7]]); see [[mcp-transport]].</span>

## Dynamic tool discovery

No Swagger is needed — the agent asks the server `tools/list` at connect time. See [[dynamic-tool-discovery]] for pitfalls (too many tools confuse the model) and the [[cli-vs-mcp-tradeoff]] for when a CLI is cheaper. <span style="color:red">Copilot caps at **128 tools** across all MCP servers, and overlapping tool definitions (Jira + Linear + GitHub issues) confuse the model ([code execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp)).</span>

## Agent authentication

Two credentials travel together on an MCP call: a **service-account API key** (app identity) and a **bearer token** (user identity). See [[agent-auth]] for the full pattern.

The full protocol spec lives at [modelcontextprotocol.io](https://modelcontextprotocol.io/).

## <span style="color:red">Installing servers safely</span>

- <span style="color:red">**MCP servers run internet code on your machine**, many community ones with file-system or shell access. Prefer servers from the official [github.com/mcp](https://github.com/mcp) catalog and **pin the version** — `claude mcp add chrome-devtools --scope user npx chrome-devtools-mcp@latest` is the convenient form, not the safe one ([[supply-chain-attack]]).</span>
- <span style="color:red">For a community server such as [mcp-atlassian](https://github.com/sooperset/mcp-atlassian): clone → scan → tweak → publish internally with a README — or use the vendor CLI with your personal access token. Corporate networks often block on-the-fly `npx` downloads anyway: download outside, run locally.</span>
- <span style="color:red">**Secrets:** a tracker token goes in a user-level, gitignored env file that the MCP config references by variable — never the value in project config, and never committed in `.mcp.json` (as one demo did). Tokens → user scope; project rules → project scope. Without an AGENTS.md rule plus a pre-commit hook ([[gitleaks]]), "don't commit this" is just discipline the agent forgets.</span>
- <span style="color:red">MCP config changes load only on harness restart.</span>
- <span style="color:red">A read-only database MCP should point at a **replica, not prod**.</span>

## <span style="color:red">MCP gateways and specialist agents</span>

<span style="color:red">The enterprise pattern is an **MCP gateway** ([Glean MCP Gateway](https://docs.glean.com/administration/platform/mcp/mcp-gateway), [Docker MCP gateway](https://docs.docker.com/ai/mcp-catalog-and-toolkit/mcp-gateway/)): agents run sandboxed in a standard Docker image with one default connector to a single MCP endpoint, where you authorize **per human and per agent profile** which tools each sees — "the agent that read the web doesn't reach the prod logs." A [[firefighter-agent]] gets prod + Grafana and no internet.</span>

<span style="color:red">Near-production agents are **tied by the hands**: a [[custom-agent]] such as a `support-chatbot` sub-agent on Sonnet with **only the application's MCP tools** — no bash, WebFetch or Task — and its limits enforced **in the tool, not the prompt**. That specialist is the one you can bring close to prod. One bank runs a chatbot over ≈40 MCP servers on 15 microservices, with zero forms in front: keep your REST well documented and anyone can crochet an MCP over it.</span>

## <span style="color:red">Designing an MCP server</span>

- <span style="color:red">**Don't ask for parameters a human can't know** (a `petId`). Bind the server to the user via the key or token — no `owner_id` parameter; it knows who you are.</span>
- <span style="color:red">**Make errors actionable** ("your pets are Whiskers, Mittens — pick one") so the agent self-corrects without a human round-trip. Say in each tool description when *not* to use it ([[tool-calling]]).</span>
- <span style="color:red">Exposing one is trivial in Spring (`@Tool` on a method) — but *"an agent can do a lot more harm than a user."* Demos derail too: one create-visit tool went straight to Postgres and lost track of the owner — *"famous last words: I shall not touch the database."*</span>
- <span style="color:red">**Who owns the mapping** of a customer's concepts onto your schema is strategic: theirs (ship the strict schema plus a skill so *their* agent scripts the transform) or yours (one mapping folder per customer, × thousands). Either way validate at the gate (ISO dates, invariants) — an anti-corruption layer on the server side.</span>

## <span style="color:red">Product decision: embed an LLM or expose an MCP?</span>

<span style="color:red">If your clients are humans, build a chatbot; if they are businesses running their own agents, **expose an MCP instead of embedding an LLM in your product** ("I wouldn't run an LLM myself internally; I'd rather expose an MCP"). Going fully headless / MCP-only bets that every customer drives an agent — risky for less AI-literate users, and agent-driven clients spend tokens on every test run; yet a UI over a good MCP can be vibe-coded in an hour.</span>

## Integration "paradise" — and its blast radius

Real MCP integrations are the road to a self-driving developer: a [[figma-mcp|Figma MCP]] (designs straight into the app, *if* they're truly maintained there), [[adb-automation|`adb`]] for mobile UI loops, a Jira MCP (e.g. mcp-atlassian) or the Atlassian CLI, and a TeamCity/CI CLI. <span style="color:red">One team had already wired Jira + Confluence via the [Atlassian Rovo MCP](https://support.atlassian.com/rovo/docs/getting-started-with-the-atlassian-remote-mcp-server/), Grafana, Figma, Microsoft 365 (mail, chat, meeting transcripts, SharePoint/OneDrive documents converted to markdown) and CI. The rule behind it: **"pasting text into the agent is illegal"** — connect every source. Where MCP is not allowed, a CLI with a personal access token or a headless browser fills the gap (shadow tooling seen at three banks).</span> Two cautions: when a CLI already covers it, the CLI is far cheaper than the MCP ([[cli-vs-mcp-tradeoff]] — the GitHub MCP burns ≈1,300 tokens for what `gh` does free); and **mind which checkboxes you tick when you mint the agent's API key** — don't hand it the power to delete a Jira project or take down prod ([[agent-permissions]]).

## See also

- [[mcp-transport]]
- [[elicitation]]
- [[sampling]]
- [[tool-calling]]
- [[jwt-identity]]
- [[supply-chain-attack]]
- [[mcp-sandbox-inheritance]]
- [[mcp-tenant-isolation]]
- [[dynamic-tool-discovery]]
- [[cli-vs-mcp-tradeoff]]
- [[agent-auth]]
- [[custom-agent]]
- [[firefighter-agent]]
- [[figma-mcp]]
- [[adb-automation]]
- [[agent-permissions]]
- [[context7]]
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
