---
title: AI Chores Around the Code
category: concept
tags: [use-cases, roi, automation, flaky-tests, documentation, adoption]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-10-01-ai-bank]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

The highest-ROI agent uses are often not new features but "taking out the trash" — the boring, recurring chores around the code that agents can do with a clear feedback signal.

## Where companies actually deploy AI (2026)

1. **Customer-support chatbots** — the easiest ROI to sell.
2. **Internal BI and incident analytics** — natural-language questions over a database.
3. **Developer productivity** — the biggest ROI, because developers are among the best-paid people in the company.

Within developer productivity, the first capacity to spend is on **repaying debt before features**: speed up the build, kill flaky tests, raise coverage, finish old refactorings.

## The chores catalog

**From tickets**
- Jira → PR for trivial changes (add a field, remove a button).
- Jira → spec ([[openspec]] markdowns from a ticket); Jira → manual test plan, optionally clicked through with a [[browser-mcp]].
- Deduplicate newly filed bugs against existing ones via a Jira MCP.

**Build and CI**
- **Fix flaky tests in a loop** — the perfect agentic task, because the signal is binary. A large software company left agents looping over its flaky suite for a weekend: 3 of 7 fixed after ≈20 builds. Step one is connecting the agent to the CI so it sees past failures (e.g. Jenkins CLI to find the cause of the last failure and mine flaky tests from past builds). See [[ci-green-loop]].
- **Auto-fix dependency-bot MRs** failing on transitive Maven dependencies, via `/loop` or a CI job calling `claude -p` ([[headless-claude]]) — the blocker is whose quota pays.

**Documentation**
- Release notes and changelogs — "one company fell in love with AI the day it realised it would never write release notes again"; release documentation composed from scattered artifacts.
- **User docs with screenshots regenerated after every change.**
- A documentation writer agent first wrote *how/what* instead of *why*; it was fixed by giving it an **example doc** plus tickets plus code ([[example-driven-prompting]]).

**Testing**
- QA browser exploration → Cypress/Playwright scripts; legacy test-case documents → automated suites ([[test-case-doc-automation]]).
- Empty test methods generated from free-text requirements, plus a coverage review that each test really tests the spec ([[semantic-coverage]]).
- Writing "passed" results from a test report back into the requirements tool, through an internal library wrapped as a CLI + skill ([[internal-tool-cli-skill]]).
- An HTML dashboard generated from a test-tool log, as a navigable bug report.
- Desktop-app test automation by clicking through an old tool.

**Understanding and operations**
- Reverse-engineering business rules from code ([[legacy-migration-stages]]).
- Production incident triage ([[firefighter-agent]]).
- Discovering new servers from cloud inventory and registering them in the remote-desktop manager; auto-renewing an expiring proxy token for every developer; a one-command local environment; a local dashboard over production log metrics.
- A **cross-team knowledge bridge**: `git blame` finds the most knowledgeable author who isn't swamped, and the AI drafts the question only after mining the code itself.

## In-house agents seen at companies

- A pipeline **code-review bot** posting MR comments — the blocker is *whose token does the bot use?* ([[agent-auth]]).
- **Multi-perspective reviewers**: clean code, performance, security, and **test quality** ("this test asserts nothing — delete") — see [[multi-model-review]].
- A **support assistant** that briefs the agent on a customer when they call.
- A **newcomer trainer** that builds a personalised weekly upskilling plan for each new joiner — Victor's favourite.
- A **team bot in channels**: one company forbade 1:1 DMs so every discussion happens in team channels with the bot present — a mineable team memory. Beware: anyone posting hostile text there injects the bot ([[prompt-injection]]).

## Rolling a chore out

Ship it as a **local skill first** (you don't own the pipeline), then distribute it through an internal git [[plugin-marketplace]] with auto-update, and finally run it nightly via cron. See also [[agentic-corporation]] for the organisational side.

## See also
- [[firefighter-agent]]
- [[ci-green-loop]]
- [[test-case-doc-automation]]
- [[agentic-corporation]]
- [[browser-mcp]]
- [[example-driven-prompting]]
- [[internal-tool-cli-skill]]
- [[plugin-marketplace]]
- [[skill-erosion]]
- [[2026-04-07-ai-product-team]]
- [[2026-07-23-ai-software-product]]
