---
title: Wrap an Internal Tool as CLI + Skill
category: pattern
tags: [skills, cli, integration, reverse-engineering, playwright, secrets, plugin-marketplace]
sources: ["[[2026-07-30-ai-safety-critical]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Wrapping an internal tool as CLI + skill means giving an agent access to an in-house system that has no public API or MCP by having the agent reverse-engineer the tool's calls, then shipping the result as a small token-efficient CLI plus a `SKILL.md` that teaches any agent to use it.

## The recipe

As walked through in [[2026-07-30-ai-safety-critical]]:

1. **Start local, on a sample.** Hand the agent a manually saved log or export and ask for a script that turns it into something useful — e.g. a navigable HTML. Decide where the code lives later.
2. **Learn the integration from existing code.** Point the agent at the tool's IDE plugin or client library sources — or let it decompile the jar — so it imitates the exact auth and calls the official client makes.
3. **Plan B — no sources at all.** Dictate the mouse steps you take; the agent repeats them with [Playwright CLI](https://playwright.dev/agent-cli/introduction) (see [[browser-mcp]]), captures the browser's HTTP calls, and writes its own script against those endpoints.
4. **Crawl cheaply.** Exploring an unknown API is token-intensive, so make the agent *write scripts* and send cheap [[sub-agents]] to do the crawling ([[model-hierarchy]]).
5. **Ship as CLI + `SKILL.md`.** Explicitly require the CLI's output to be **token-efficient** — terse, no noise, the same goal [[rtk]] pursues for standard commands. Distribute it through the team [[plugin-marketplace]].
6. **Close the conversation with artifacts.** Skill + scripts + an output template, so tomorrow the job runs without re-deriving anything ([[determinism-over-llm]]).

## Skills, not MCPs

Even where an MCP server exists for a similar product, the advice is to **clone it only to learn** how the service is called — e.g. reading [mcp-atlassian](https://github.com/sooperset/mcp-atlassian) to see the HTTPS calls with a personal access token — then package that know-how as a skill for the team. A skill plus CLI loads on demand and costs no permanent tool-schema tokens; see [[cli-vs-mcp-tradeoff]] and [[authenticated-cli]].

## Auth and secrets

- A **session cookie copied from the browser** often reaches more endpoints than the official API key. Acceptable while exploring.
- Then move secrets into a `.env` read by the scripts — **never in the prompt, never in an `.md` on disk, never in git** (see [[secret-zero]], [[gitleaks]]).
- The agent's token gets **fewer rights than you**, and the scopes are ticked off by two people.

## See also
- [[agent-skill]]
- [[authenticated-cli]]
- [[cli-vs-mcp-tradeoff]]
- [[browser-mcp]]
- [[determinism-over-llm]]
- [[plugin-marketplace]]
- [[secret-zero]]
- [[rtk]]
- [[sub-agents]]
