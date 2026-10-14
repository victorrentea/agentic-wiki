---
title: Non-Goals
category: pattern
tags: [spec-driven, requirements, scope, agents-md]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-09-18-ai-agentic-how]]"]
created: 2026-06-24
updated: 2026-10-13
---

A non-goals section explicitly states what a feature will *not* do, preventing agents from over-engineering solutions beyond the requested scope.

## Why agents need explicit non-goals

Agents are optimistic builders. Given an ambiguous specification they tend to implement the fullest possible interpretation — *"agents build rockets when you asked for a bicycle."* A `Non-goals` chapter in the [[spec-driven-development|spec]] (or [[agents-md|AGENTS.md]]) is the structural counterweight: it names the things that are deliberately out of scope so the agent doesn't implement them.

Concrete example: a rename/delete feature with offline sync out of scope. Without an explicit non-goal, the agent might implement a transaction-ID + timestamp offline queue to handle disconnected edits — correct in the general case, catastrophic for MVP scope. The non-goals chapter says *"no offline sync — if the server is unreachable, fail loudly."*

<span style="color:red">More live examples of the agent that "always flows toward doing more":</span>

- <span style="color:red">For a CRUD screen with ≈300 owners, the AI proposed a **"Raft-style generation counter"** to handle request races — cut [[2026-04-16-ai-safety-critical]].</span>
- <span style="color:red">Asked for a small feature, it returned 1,000 lines, admitted it was overdone, and came back with 60. The critical non-goal there: no search-as-you-type without a button [[2026-07-20-ai-telecom]].</span>

## <span style="color:red">The two most common over-delivery patterns</span>

<span style="color:red">Name both explicitly as non-goals [[2026-05-06-ai-ecommerce]]:</span>

1. <span style="color:red">**Scope borrowed from framework idioms** — unrequested pagination, caching, parallelism, because "that's how it's usually done".</span>
2. <span style="color:red">**Refusal to delete** — keeping the old API "for compatibility" next to the new one (see [[ai-never-deletes]]).</span>

## <span style="color:red">Writing a good non-goal list</span>

- <span style="color:red">**Be concrete.** For a schema change: *"no navigation redesign, no backfill of old records, the new FK may be null"* — teams have lost a week for lack of such a list [[2026-05-13-ai-it-services]].</span>
- <span style="color:red">**Existing UX is an unofficial contract with users.** In regulated or banking contexts users want the screen to look as it has for decades (export to Excel → edit → re-import), so "refactor the UX" is a non-goal by default [[2026-05-13-ai-it-services]].</span>
- <span style="color:red">**The "rejected / not included" section has double value**: it blocks feature creep *and* upsells the next ticket [[2026-08-10-ai-consultancy]] [[2026-08-06-ai-bank]].</span>

## Where it lives

- **In the spec's `design.md`** — under a `## Non-goals` heading alongside Risks & trade-offs.
- **In `AGENTS.md`** — for persistent, project-wide out-of-scope patterns ("never implement fuzzy search").
- **In [[openspec]] output** — OpenSpec's design template includes a non-goals section by default. <span style="color:red">If your downloaded SDD framework's templates are unchanged after six months, you brought no opinion to them — non-goals are one of the first places to add it [[2026-09-18-ai-agentic-how]].</span>

The non-goals chapter is also the place to call out [[scope-creep]] patterns the agent is likely to reach for — pagination on every screen, caching every response, parallelizing every list operation. Name them; the agent will avoid them.

## <span style="color:red">"Out of scope" as a career skill</span>

<span style="color:red">*"In 5 years the life skill won't be writing code — it will be saying 'out of scope'."* The same session framed it more broadly: *"70% of your job isn't code — it never was"*; developers traded people time for focus, and as focus work moves to agents, scoping and negotiating with people becomes the job. Practical take-aways: ≈30 min/week of critical-thinking exercises, and discuss personal boundaries with management before "AI psychosis" (a term from a [Karpathy](https://x.com/karpathy) tweet) shows up in the team. See [[skill-erosion]] and [[ai-addiction]] [[2026-04-16-ai-safety-critical]].</span>

## See also
- [[spec-driven-development]]
- [[openspec]]
- [[agents-md-discipline]]
- [[grill-me]]
- [[acceptance-test-bdd]]
- <span style="color:red">[[ai-never-deletes]]</span>
- <span style="color:red">[[ai-spiraling]]</span>
- <span style="color:red">[[skill-erosion]]</span>
- <span style="color:red">[[ai-addiction]]</span>
- [[2026-06-23-ai-device-maker]]
