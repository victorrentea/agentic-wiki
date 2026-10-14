---
title: Kiro
category: tool
tags: [kiro, aws, ide, spec-driven-development, steering, hooks, ears]
sources: ["[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-05-11-ai-software-vendor]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

[Kiro](https://kiro.dev) is AWS's agentic IDE and CLI built around [[spec-driven-development]], with "steering" documents as project memory and an event-driven hook system.

## Specs in EARS

A Kiro spec runs **requirements → design → tasks**. Requirements are generated in **EARS** notation ([Easy Approach to Requirements Syntax](https://en.wikipedia.org/wiki/Easy_Approach_to_Requirements_Syntax), Rolls-Royce origin): *"WHEN [trigger], the [system] SHALL [response]"*. Every `SHALL` line is an independently verifiable claim — in effect a self-grading rubric for the AI — and maps cleanly onto Gherkin Given/When/Then ([[acceptance-test-bdd]]).

Kiro is one of several interchangeable SDD frameworks, alongside [[openspec]], [GitHub Spec-Kit](https://github.com/github/spec-kit), BMad, get-shit-done and [[superpowers]] ([[2026-05-13-ai-it-services]], [[2026-05-27-ai-open-workshop]]).

## Steering inclusion modes

Steering files are Kiro's equivalent of [[claude-md]] / [[agents-md]]. Pick the cheapest inclusion mode that works ([[progressive-disclosure]]):

| Mode | Loaded when | Use for |
|---|---|---|
| *always* | every conversation | very little — it taxes every turn |
| *auto* | the description matches the task (skill-like) | topical guidance |
| *manual* | you reference it with `#file` | rarely needed docs |
| **file match** | a file matching a glob is read or written | rules tied to a layer |

File match was called *"the single most important Kiro feature"* by a team on a large public-sector Kiro project. Example: a `db-queries.md` rule — *"prefer `EXISTS` over `JOIN` for parent–child filters"* (avoids cardinality explosion and broken page counts) — that fires only on `*repository*` files. The same rule surfaced independently in two other sessions ("never `DISTINCT` + `LIMIT/OFFSET` over a `JOIN` — page counts lie") ([[2026-05-06-ai-ecommerce]], [[2026-05-11-ai-software-vendor]]). Action item for teams: convert always-loaded steering to auto or file-match. Claude Code's equivalent is a `paths:` glob on a skill or a `.claude/rules/*.md` file ([docs](https://code.claude.com/docs/en/memory#path-specific-rules)).

## Powers, hooks and agents

- **"Powers" are skills.** The IDE's "powers" UI is Kiro's naming; underneath it reads the same Anthropic [[agent-skill]] format (`.kiro/skills/`), portable across Claude Code, Codex, Cursor and Kiro CLI.
- **Hook events:** `fileCreated` / `fileSaved` / `fileDeleted`, `agentStop`, `promptSubmit`, `preToolUse` / `postToolUse`, `preTask` / `postTask`, and `manualTrigger` (an IDE button). Recursive hooks are warned about but not blocked. Compare [[agent-lifecycle-hooks]].
- **CLI vs IDE:** at the time of the session, orchestration patterns and [[sub-agents]] worked only in Kiro CLI. Trusting `*` in YOLO mode is "the button you don't know what it does" ([[agent-permissions]]).

## What to commit

`.kiro/steering/`, `skills/`, `hooks/`, `agents/` and `settings.json` (MCP servers, with `${ENV_VAR}` for secrets) — all yes. `specs/` too, archived after 3–6 months.

## Live behaviour worth knowing

From a two-day workshop in Kiro IDE and Kiro CLI ([[2026-05-06-ai-ecommerce]]):

- **Good:** Kiro caught a potential N+1 unprompted ("shall execute the search using a single query") and noticed feature creep mid-vibe: "this is no longer vibing — do you want to spec?"
- **Bad:** on the free tier the model was Sonnet, and one long spec conversation drifted into the [[dumb-zone]] — "do this" / "of course" with no change in the code ([[ai-spiraling]]). Fix: `/clear` after each spec phase.

## The December 2025 outage report

In February 2026 the *Financial Times*, citing four unnamed people, reported that in mid-December 2025 engineers let Kiro make changes and it chose to **"delete and recreate the environment"**, interrupting AWS Cost Explorer in one mainland-China region for **13 hours**; the agent had inherited the operator's broad permissions, so no second approval was required ([summary](https://the-decoder.com/aws-ai-coding-tool-decided-to-delete-and-recreate-a-customer-facing-system-causing-13-hour-outage-report-says/)). Amazon disputes the framing — "user error—specifically misconfigured access controls—not AI" — says no compute, storage or database service was affected, and added **mandatory peer review for production access** ([Amazon's correction](https://www.aboutamazon.com/news/aws/aws-service-outage-ai-bot-kiro)). No database was dropped, and the event is unrelated to the October 2025 us-east-1 DynamoDB outage. Either way the lesson is the same: an agent must not inherit a human's production rights ([[production-safety]], [[agent-auth]]).

## See also
- [[spec-driven-development]]
- [[openspec]]
- [[progressive-disclosure]]
- [[agent-lifecycle-hooks]]
- [[agent-skill]]
- [[harness]]
- [[superpowers]]
- [[production-safety]]
- [[2026-05-06-ai-ecommerce]]
