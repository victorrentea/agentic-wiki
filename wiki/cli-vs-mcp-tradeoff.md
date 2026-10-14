---
title: CLI vs MCP Trade-off
category: concept
tags: [mcp, cli, token-economy, tooling, agent-design]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-12
updated: 2026-10-13
---

When both a CLI and an MCP server exist for the same tool, prefer the CLI if the agent's base model already knows it from training — the CLI costs ≈0 context tokens whereas the MCP floods the context with tool schemas on every session start.

<span style="color:red">By autumn 2026 the rule had sharpened: **on your own machine a CLI (wrapped in a skill) beats an MCP; MCP earns its cost for cloud or locked-down agents, and through per-agent authorization.** "The agent doesn't need MCP — it's enough that *you* are logged into your CLI."</span>

## The token math

An MCP server's tool list is loaded eagerly at session start (part of the [[system-prompt]] payload). For a well-specified MCP, that can run to **300–600 tokens** — before you've typed anything. A CLI the model was trained on costs essentially nothing: the agent already knows the flag syntax; a thin skill wrapper takes ≈60 tokens to remind it of any project-specific conventions. <span style="color:red">Asking a CLI for `--help` costs ≈10 tokens, on demand ([Claude Code best practices](https://code.claude.com/docs/en/best-practices#use-cli-tools)); big servers are far heavier — the Grafana MCP pours ≈65–80 tools into the prompt.</span>

The [[dynamic-tool-discovery]] mechanism means an unfamiliar MCP still has to load all those schemas even if the agent uses only one or two tools from it. <span style="color:red">The newest session noted that Claude Code now defers MCP tool schemas until they are searched for, while some other harnesses have no tool search — so the eager-load cost depends on the harness. Either way, disable servers you don't use; servers such as `mcp-grafana` let you choose which tools to expose.</span>

<span style="color:red">Output format matters as much as the schema. CLIs pipe through `grep`/`jq`/`sed` and have `--quiet`; MCP tools tend to return JSON. The same content cost **86 tokens as JSON vs 38 as TSV/flat text** — so a tool you write should return minimal text, TSV, YAML or [[toon|TOON]], never verbose JSON; and not binary (protobuf) either, because you must be able to sniff, debug and mock it with WireMock.</span>

## When MCP wins

MCP is the right choice when:
- **Auth isolation** matters — the MCP holds credentials the agent never sees directly (see [[secret-zero]]). <span style="color:red">MCP authorization gives a fresh OAuth bearer **on behalf of the user, injected per task**. A CLI once logged in (`gh auth login`) stays logged in forever on that box — a problem on a shared machine or inside a sandbox, and a `gh` logged in as a human hands the agent that human's full rights.</span>
- **Sandbox inheritance** matters — a local stdio MCP is a child process that inherits the agent's OS sandbox (see [[mcp-sandbox-inheritance]]).
- The tool has no stable CLI or the CLI isn't in the model's training data, making [[dynamic-tool-discovery]] necessary.
- The tool provides **typed tool definitions** with safe parameter validation.
- <span style="color:red">**Cloud or remote agents** — they run on a VM that has none of your local CLIs, often with short-lived SSO tokens to prod (e.g. expiring in 30 minutes).</span>
- <span style="color:red">**You hand the agent to an end user.** MCP is a cage: the agent can't drop the schema if no `dropSchema` tool is exposed, every call is validated and audit-logged. Letting an agent write SQL directly for end users is "like the frontend writing straight into the DB".</span>
- <span style="color:red">**[[elicitation]] or [[sampling]]** are needed — e.g. internal tooling with destructive steps that require proof of life mid-call. See [[model-context-protocol]].</span>

## When CLI wins

- The CLI is years old and well-represented in training data (e.g. GitHub CLI, Atlassian CLI).
- The agent runs on a developer's machine where bash access is already allowed.
- You want zero eager-load overhead — every token of tool schema is a token stolen from your usable window.
- <span style="color:red">**Your organisation bans MCP.** Banks block it because an `.mcp.json` with `npx` means *downloading an internet library and running it on a company machine*. Every major tool already ships a CLI, often already approved (MongoDB CLI, say).</span>

The canonical worked example: the **GitHub MCP costs ≈1,300 tokens** of tool descriptions for things the **`gh` CLI** already does — and every LLM *"wakes up dreaming about `gh`"* because it's so well-represented in training data. So disable the GitHub MCP and let the agent use `gh` directly. The same logic drives "connect the agent to the issue tracker instead of retyping the ticket" (`i want to work on gh issue #1`) — see [[agentic-corporation]] for the blackboard pattern, and [[chop-ai-waiter]] for why retyping is the servant anti-pattern.

<span style="color:red">**The CLI inventory** sessions kept returning to: `gh` / [`glab`](https://docs.gitlab.com/cli) (issues, pipelines), Azure `az` ([boards](https://learn.microsoft.com/en-us/cli/azure/boards): work items *and* why the last build failed) and `azd`, Atlassian ACLI or [`jira-cli`](https://github.com/ankitpokhrel/jira-cli), `jenkins-cli`, TeamCity, `kubectl`, `aws`, `curl`, the [M365 CLI](https://pnp.github.io/cli-microsoft365/) for Teams and mail, Salesforce CLI, Playwright CLI for a real browser (reproduce bugs, screenshot, read the console, read your timesheets on the intranet), and database clients (below). A brand-new CLI such as Linear's may need teaching in 4–5 lines — though newer models are already "born knowing it". Homework prompt: *"install this CLI, then tell me why the last build failed"* — companies cut 15–30% of build time this way. *"I want to get the corpse into the terminal. Once it's in the terminal, it's dead."*</span>

## <span style="color:red">The third way: a skill that scripts it</span>

<span style="color:red">Beside MCP and a known CLI there is the **"poor-man's MCP"**: a script plus a `SKILL.md` documenting how to invoke it — same effect, no server ([[agent-skill]], [[internal-tool-cli-skill]]).</span>

- <span style="color:red">**A custom CLI with 3–4 well-documented commands** needs only a good `--help`, a quiet flag and one line in CLAUDE.md saying it exists. Tune it for tokens and run an eval on 3–5 models.</span>
- <span style="color:red">**A skill that teaches which REST endpoint to `curl`.** If you have an API you need neither MCP nor CLI. Example: Playwright analysed an internal Kibana's network traffic → a `.sh` script with the session cookie → direct backend calls, no browser automation. For tools only developers use locally: skip MCP, expose REST.</span>
- <span style="color:red">**Conversion works both ways:** OpenAPI spec ⇒ generated MCP, or existing MCP ⇒ CLI skill. [mcptools](https://github.com/f/mcptools) wraps an existing MCP as a CLI with no code; a DB CLI skill born from the Postgres MCP was then deliberately run on the weakest model to prove it worked.</span>
- <span style="color:red">**Distill a working session into a scripted skill:** *"extract what you learned into a `<tool>`-cli skill, scripting as much as you can"*, then verify it in a *fresh* session. Prefer Python over Java for skill scripts (portable, no JVM start-up), build a **generic connector** rather than a skill bound to one page, keep the script in the skill folder and the token in an uncommitted `.env`. A Confluence CLI skill (REST + personal access token) took ≈10 minutes to build ([confluence.py](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/confluence-cli/confluence.py)); one `jira-cli`-style skill with a personal API key can cover Jira, Jenkins and Confluence ([jenkins.py](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/jenkins-cli/jenkins.py)). On Windows the agent rewrites such a skill in PowerShell itself.</span>
- <span style="color:red">**Never let an agent repeat the same web flow twice.** Once it works: "now write me a CLI for this, no LLM involved" — rediscovering the page costs ≈100×. Microsoft's own [`playwright-cli`](https://github.com/microsoft/playwright-cli) README argues CLI + skill over MCP because it does not force page data into the model. For logins, have the agent write the login script once, then "execute this, don't read it".</span>
- <span style="color:red">**Install tools in natural language**: *"clone the repo, build it, install it, and use it to fetch my Jira tickets and the deploy page from Confluence"* — the second half forces it to actually search.</span>
- <span style="color:red">**It acts in your name: it drafts, you publish** — [[draft-only-email]] generalised to Confluence and trackers.</span>

<span style="color:red">With ≈50 skills, the descriptions alone crowd the context too — so the same budget discipline applies to skills ([[progressive-disclosure]]). MCP configs and skills both travel across harnesses (the same `.mcp.json` loads into [[kiro]]).</span>

## <span style="color:red">Databases</span>

<span style="color:red">Use the **vendor's CLI** — [SQLcl](https://docs.oracle.com/en/database/oracle/sql-developer-command-line/) for Oracle (`brew install sqlcl`; CSV output mode, credentials in the OS keychain so the agent never sees the password), `psql` for Postgres, `sqlcmd` for SQL Server. The vendor CLI already knows every dialect quirk, which raw JDBC doesn't. Combined with automated `EXPLAIN PLAN`, this is the most promising route to AI-driven query tuning (one engineer tuned three years-old queries over a weekend). Example skill: [db-cli](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/db-cli/SKILL.md) — one prompt produced an Excel pie chart of owners and pets.</span>

<span style="color:red">A **DDL dump committed to git** greps faster than live schema queries and spares the reflection calls. **Ask for a script, not an opinion**: a scripted pass over 400 tables made zero mistakes where an eyeballing agent would have. The MCP side of the debate: least privilege (the main agent never touches the DB, table-level permissions) plus elicitation. "The more basic, the better it works" — benchmark, don't assume.</span>

## <span style="color:red">Failure mode: the tool messup</span>

<span style="color:red">`gh` fails once → the agent opens GitHub in the browser and clicks through at ≈100× the tokens and time → auto-memory records the detour and it becomes the default. Fix: keep the Chrome extension [disconnected by default](https://code.claude.com/docs/en/chrome#enable-chrome-by-default); add a user-level CLAUDE.md line "never use Claude in Chrome/Playwright instead of the GitHub tool"; surgically cut the memorized line; install `gh`; uninstall the GitHub MCP. Periodically ask Claude in your main folder: *"any colliding or confusing tools you see?"* — overlapping tool sets confuse the model ([[tool-calling]]).</span>

## <span style="color:red">If you do write an MCP</span>

<span style="color:red">It is the first genuinely new thing in the codebase — review it obsessively, line by line. **Tool definitions are Javadoc for agents**: name, "use this when…", typed parameters; the model knows only what the schema says. Return flat text, not JSON (see above). And mind the secrets: MCP configs end up containing passwords (a Postgres MCP wired to staging — and staging has real data).</span>

## Gateways

Glean is an example where the MCP / governed-door story wins despite the cost: it's the controlled gateway to sensitive corporate resources (Jira, Slack, codebase search) that you don't want to expose via raw bash. <span style="color:red">The pattern generalises to **one MCP gateway proxying every company tool, enabled per employee and per agent** — e.g. one internal DevOps MCP already wired to GitLab, Jira, SonarQube, ServiceNow and Confluence. Details in [[model-context-protocol]].</span>

## See also

- [[token-economy]]
- [[model-context-protocol]]
- [[dynamic-tool-discovery]]
- [[mcp-sandbox-inheritance]]
- [[agent-skill]]
- [[internal-tool-cli-skill]]
- [[harness]]
- [[agentic-corporation]]
- [[chop-ai-waiter]]
- [[draft-only-email]]
- [[toon]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
