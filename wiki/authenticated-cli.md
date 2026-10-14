---
title: Authenticated CLI in Bash
category: pattern
tags: [cli, bash, auth, mcp-alternative, credentials, corporate]
sources: ["[[2026-07-03-ai-bank]]", "[[2026-09-24-ai-bank]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Give the agent a CLI you have already logged into (`az`, `gh`, `psql`, a Jira CLI) and let it call it through Bash, so it acts with your identity without the token ever entering its context.

## How it works

`az login`, `gh auth login` or a PAT-configured CLI stores the credential in your home directory. The agent never sees the secret in its [[context-window]], but it can **invoke the CLI** and inherit your authentication. "Use `az` in bash to read the ticket I'm on" is enough — no MCP server, no separate credentials, no setup [[2026-07-03-ai-bank]].

It works for Azure DevOps (`az`), Postgres (`psql`), Oracle (SQL\*Plus — reading explain plans, rewriting 300-line stored procedures), Jira and Splunk. In locked-down corporate environments where MCP installs are blocked, a CLI is often still installable — through the company software center when pip/PyPI is blocked. This is the security face of the [[cli-vs-mcp-tradeoff]].

## Burn the discovery into a script

The first time, the agent spends many tool calls discovering the right command sequence. Once it works, have it write a shell script (or a skill — see [[agent-skill]] and [[internal-tool-cli-skill]]) so the discovery isn't repeated. In one session an `az` exploration of ≈20 tool calls became a ≈200-line script answering in ≈3 s — the [[determinism-over-llm]] move [[2026-07-03-ai-bank]].

## Caveats

- **The credential file still sits on disk.** Without an [[os-sandbox]] or a container the agent *could* `cat` it; pair the pattern with [[dont-run-in-home-folder]] and sandboxing. Never put tokens in environment variables the agent can print.
- **Blast radius = you.** The CLI carries your full permissions: after `az login` the agent could delete every ticket you can delete. Prefer a dev/staging-scoped login, or a **throwaway session** that only exports what is needed to markdown and is then killed, leaving the long-running agent to work from the export [[2026-09-24-ai-bank]].
- **No agent identity.** `gh` carries only *your* token, so the server cannot tell the agent from you and cannot authorise the agent itself — the gap that RFC 8693 on-behalf-of tokens close (see [[agent-auth]]) [[2026-09-30-ai-enterprise]].

## Where it sits

The cheap, local cousin of [[secret-zero]]'s MCP-as-proxy: zero infrastructure, but the secret lives on the same machine as the agent. Use it to get going on a laptop; move to a proxy or a non-human identity when the agent runs unattended or touches shared systems ([[production-safety]], [[agent-permissions]]).

## See also

- [[cli-vs-mcp-tradeoff]]
- [[secret-zero]]
- [[agent-auth]]
- [[dont-run-in-home-folder]]
- [[os-sandbox]]
- [[agent-permissions]]
- [[agent-skill]]
- [[internal-tool-cli-skill]]
- [[determinism-over-llm]]
