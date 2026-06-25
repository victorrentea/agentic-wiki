---
title: Non-Goals
category: pattern
tags: [spec-driven, requirements, scope, agents-md]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-24
updated: 2026-06-24
---

A non-goals section explicitly states what a feature will *not* do, preventing agents from over-engineering solutions beyond the requested scope.

## Why agents need explicit non-goals

Agents are optimistic builders. Given an ambiguous specification they tend to implement the fullest possible interpretation — *"agents build rockets when you asked for a bicycle."* A `Non-goals` chapter in the [[spec-driven-development|spec]] (or [[agents-md|AGENTS.md]]) is the structural counterweight: it names the things that are deliberately out of scope so the agent doesn't implement them.

Concrete example: a rename/delete feature with offline sync out of scope. Without an explicit non-goal, the agent might implement a transaction-ID + timestamp offline queue to handle disconnected edits — correct in the general case, catastrophic for MVP scope. The non-goals chapter says *"no offline sync — if the server is unreachable, fail loudly."*

## Where it lives

- **In the spec's `design.md`** — under a `## Non-goals` heading alongside Risks & trade-offs.
- **In `AGENTS.md`** — for persistent, project-wide out-of-scope patterns ("never implement fuzzy search").
- **In [[openspec]] output** — OpenSpec's design template includes a non-goals section by default.

The non-goals chapter is also the place to call out [[scope-creep]] patterns the agent is likely to reach for — pagination on every screen, caching every response, parallelizing every list operation. Name them; the agent will avoid them.

## See also
- [[spec-driven-development]]
- [[openspec]]
- [[agents-md-discipline]]
- [[grill-me]]
- [[acceptance-test-bdd]]
- [[2026-06-23-ai-garmin]]
