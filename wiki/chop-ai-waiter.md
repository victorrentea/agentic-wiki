---
title: CHOP / AI-Waiter Anti-Pattern
category: pattern
tags: [anti-pattern, feedback-loop, copy-paste, chat, agentic-engineering]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-14-ai-industrial]]", "[[2026-10-01-ai-bank]]", "[[2026-10-07-agentic-reconversion-devoxx]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-23
updated: 2026-10-13
---

CHOP — **Ch**at-**O**riented **P**rogramming — is the anti-pattern of copy-pasting between a chat web UI and your editor, with *you* acting as the waiter: ferrying code and errors back and forth so the AI can never see the result of its own work.

## The failure

A developer chats in a web UI, gets back a method that turns out to be deprecated, and has to *manually* discover the deprecation and tell the AI it was wrong. The human is doing the legwork an agent should do for itself — checking the build, reading the warning, looping. *"We are not the servants of AI."* <span style="color:red">Later sessions used blunter names for the same loop — Ask mode + copy-paste + Run + paste the error back is *"the AI enslaves you"* ([[2026-10-01-ai-bank]]), or **"slave mode"** ([[2026-08-31-ai-software-vendor]]).</span>

## The fix: embed and give it a feedback loop

- **Embed the agent in the project**, not a detached chat — so it can read files, run the build, and run tests.
- **Give it a way to check its own work**: *"build the project so the deprecation warning surfaces,"* then let it loop until the warning is gone. This is a [[feedback-loop]].
- For **learning a new API**, don't chat about it — have the agent build a **runnable prototype** with a real feedback loop, so its claims are grounded in execution rather than recalled (and possibly [[knowledge-cutoff|out-of-date]]) memory.
- <span style="color:red">**"Rule of the year": don't tell it what to fix — give it a feedback loop and a way to verify its own result** (*"check in the browser that you see the owners in the grid"*). A bug fixed with **no editor open**: reproduce in Playwright → Gherkin e2e test ([[acceptance-test-bdd]]) → fix → a before/after [video](https://playwright.dev/docs/videos) — *"the video doesn't let it lie"*. Before pressing Enter, imagine the failure modes yourself: validation only in the frontend, what "recent" means, a pet without a birthday, February 31 ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">**Don't run the tests and paste the output** — say *"fix tests"* and it reruns only the failures. The one exception raised in the room: 15-minute integration suites, where handing it the failing names saves a run ([[2026-10-13-ai-bank]]).</span>

## Zero paste: connect, don't carry

<span style="color:red">The maturity target is **zero paste in the terminal** — *"true agentic engineers never paste text"*. Every paste is a missing integration: pasting from Jira? integrate Jira; from the build? integrate the build. *"What you haven't integrated, you carry by hand"* ([[2026-08-31-ai-software-vendor]], [[2026-07-01-ai-java-community]], [[2026-09-14-ai-industrial]], [[2026-10-13-ai-bank]]). Victor's talk makes the same point: [don't paste text to the agent — connect it to your tools](https://www.youtube.com/watch?v=tuY2ChJIx48&list=PLggcOULvfLL8zIYIX6gnNr4bNRSsoMYdz&index=142&t=409s) ([[2026-10-07-agentic-reconversion-devoxx]]). *"You don't copy-paste knowledge to the agent, the agent retrieves it"* — an Anthropic lead's challenge is to spend a whole workday inside the agent CLI with no other program open.</span>

<span style="color:red">**Four ways to connect an agent to a system** ([[2026-07-22-ai-agentic-how]]):</span>

| <span style="color:red">Route</span> | <span style="color:red">Notes</span> |
|---|---|
| <span style="color:red">[[model-context-protocol\|MCP]]</span> | <span style="color:red">richest, costs context per tool ([[cli-vs-mcp-tradeoff]])</span> |
| <span style="color:red">CLI</span> | <span style="color:red">cheapest in tokens; the agent can extend it itself when a command is missing</span> |
| <span style="color:red">API / `curl`</span> | <span style="color:red">for anything with an endpoint and a token</span> |
| <span style="color:red">Driving the browser in your logged-in session</span> | <span style="color:red">e.g. reading production Grafana ([[browser-mcp]])</span> |

<span style="color:red">*"No one can stop you from connecting the agent to anything you can access"* — but take the scariest steps last: your inbox, a team bot, an AI repairing production to cut MTTR. For mail, stay at [[draft-only-email|draft]].</span>

<span style="color:red">**Any internal web GUI can become a CLI.** Script it once with [Playwright headless](https://playwright.dev/docs/api/class-browsertype) and it costs zero tokens afterwards: Gmail or WhatsApp Web over a dedicated logged-in browser profile, or — in one team — a vendor that truncates staging monthly, where QA has no DB access, so a nightly Playwright run dumps the data to JSON and repopulates it in the morning ([[2026-09-02-ai-insurer]], [[2026-09-14-ai-industrial]]). *"What tools don't give you, build in-house"* — a CLI or your own MCP ([[internal-tool-cli-skill]]). But check what exists first: one developer nearly hand-built a cross-repo PR dashboard that [GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects) already provides. *"Crazy ideas are now worth more than deep analysis."*</span>

<span style="color:red">**Database access ladder**, safest first ([[2026-07-30-ai-safety-critical]]): a `.mcp.json` committed in the project **without passwords** → a small JDBC utility the agent calls from the terminal → `npm i pg` plus a script the agent writes. *"'Third-party MCP servers disabled' is corporate psychosis — if *you* can connect to the DB from the project, so can the agent."*</span>

## Terminal hygiene

- <span style="color:red">Don't search for files yourself — ask the agent to grep.</span>
- <span style="color:red">**Disable IDE ghost-text completion while writing prompts**: a small LLM completing your prompt for a frontier model degrades it.</span>
- <span style="color:red">Start the services it needs yourself, so it doesn't burn quota fumbling with them ([[2026-07-30-ai-safety-critical]]).</span>
- <span style="color:red">Show it your best-tested service and it imitates its spirit.</span>
- <span style="color:red">Long analysis docs: summarise to markdown, then **start a fresh session** — ≈75% of one SharePoint export was noise ([[token-economy]], [[2026-10-13-ai-bank]]).</span>

## Why it persists

CHOP feels safe because the human stays "in the loop" — but it taxes the scarcest resource (your attention, see [[caveman]]) and removes the agent's ability to self-correct. The whole discipline of [[agentic-engineering]] is the opposite: integrate, don't copy-paste; let the agent close its own loops. The [[cli-vs-mcp-tradeoff]] "connect the agent to the issue tracker instead of retyping the ticket" rule is the same instinct applied to requirements.

<span style="color:red">A related trap is the **persona-only [[custom-agent]]** ("Java 25 expert"): a separate context that only loses information. A specialist sub-agent earns its keep only with **one maniacal task** — *"You are the merge implementation reviewer"*, which refuses when asked about a frontend bug — and if its description is too thin (under ≈60 tokens, per one session) the orchestrator forgets it exists ([[2026-10-01-ai-bank]], [[sub-agents]]).</span>

## See also
- [[feedback-loop]]
- [[agentic-engineering]]
- [[caveman]]
- [[knowledge-cutoff]]
- [[cli-vs-mcp-tradeoff]]
- <span style="color:red">[[ai-calisthenics]]</span>
- <span style="color:red">[[internal-tool-cli-skill]]</span>
- <span style="color:red">[[browser-mcp]]</span>
- <span style="color:red">[[draft-only-email]]</span>
- <span style="color:red">[[custom-agent]]</span>
- [[2026-06-23-ai-device-maker]]
