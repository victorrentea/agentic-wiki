---
title: Spec Afterlife
category: pattern
tags: [spec-driven, documentation, adr, drift, jira, acceptance-tests]
sources: ["[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Spec afterlife is what happens to the markdowns of [[spec-driven-development]] once the change ships: each file drains into a durable home — the ticket, the test suite, an ADR — and the markdown itself is deleted within ≈3 months, before it drifts and misleads the next agent.

*"Markdowns are scaffolding — rehouse, then delete."* ([[2026-09-18-ai-agentic-how]])

## Why specs must die

**Old specs drift and derail agents.** An agent built something from a month-old obsolete spec ([[2026-05-06-ai-ecommerce]]); another trusted an old spec of a Python → Swift migration and burned an hour ([[2026-09-02-ai-insurer]]). The agent cannot tell a stale decision from a live one — this is [[vibe-heresy]] ("history heresy" in one session, [[2026-08-10-ai-consultancy]]). Yet while they're fresh, committed specs explain *why* the code looks as it does and survive the bus factor, which chat history doesn't ([[2026-05-06-ai-ecommerce]]); with archived specs in git, a reviewer reads the markdown intent first, then the 20-file diff ([[2026-04-27-ai-healthcare-software]]).

## How the advice evolved

- **Spring 2026: accumulate.** OpenSpec's `archive` merges each finished change into a git-committed `specs/<capability>/spec.md`, a reverse-engineered, always-current Given/When/Then description of live behaviour — *"in a year the source of truth won't be Jira, it'll be this specs folder"*, because Jira always drifts ("Is your Jira up to date? Never.") ([[2026-04-16-ai-safety-critical]], [[2026-05-11-ai-software-vendor]], [[2026-05-13-ai-it-services]]). The page then also said "burn `design.md` when done".
- **Summer: split by kind.** Keep and sync technical specs ≈3 months, then burn them; **functional specs and especially acceptance tests never expire** ([[2026-07-01-ai-java-community]]). Jira, not `proposal.md`, is the source of truth for the functional part, with the tester's `.feature` embedded in the ticket ([[2026-07-23-ai-software-product]]).
- **Autumn: drain, then delete.** The consensus across the September–October sessions: the markdowns are dead in ≈3 months, "like building scaffolding"; before deleting, move every piece of content to where it lives on ([[2026-09-28-ai-gaming-tech]], [[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]).

## Where each file goes

| File | Afterlife |
|---|---|
| `proposal.md` | **back into the Jira ticket** — what was functionally implemented, with feedback on the criteria, where non-technical people have access and control ([[2026-08-27-ai-it-services]], [[2026-09-14-ai-industrial]]) |
| `spec.md` (acceptance criteria) | **automated acceptance tests** in the pipeline — 100% of its sentences covered ([[2026-09-28-ai-gaming-tech]]; see [[semantic-coverage]]) |
| `design.md` | **one-line ADR rows** — decision, why, what was rejected — in `architecture.md`, *linked* from CLAUDE.md rather than inlined ([[2026-09-04-ai-software-company]]) |
| `tasks.md` | trash |

The ADR is **compressed for the agent** — Martin Fowler's [context anchoring](https://martinfowler.com/articles/reduce-friction-ai/context-anchoring.html) ([[2026-08-31-ai-software-vendor]], [[2026-09-28-ai-gaming-tech]]). Row shapes: [Nygard's ADR](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions), or [MADR](https://adr.github.io/madr/), which keeps *Considered Options*. See [[decision-context]].

Open questions found during development also go **back into the ticket as agent-posted comments**, not buried in a markdown ([[2026-07-23-ai-software-product]]); non-goals push leftover work ("we're not fixing the N+1 now") back into the backlog ([[2026-08-27-ai-it-services]]).

## Tests outlive code

**Code is disposable; tests are the asset.** Some major open-source frameworks now **hide their acceptance tests**, so a fork can't be verified — years of corner cases stay with the original ([[2026-07-01-ai-java-community]]). That is why acceptance criteria are the one part of a spec that must survive, as executable tests ([[acceptance-test-bdd]]).

## Keeping live docs honest

For markdown that *does* stay (AGENTS.md, ADRs, architecture notes), a [[doc-gardener]] agent periodically rescans it against the code and flags drift ([[2026-10-06-gray-factory-devoxx]]). Generate a **`runbook.md` with the feature** — config to add, endpoints to call manually at deploy — as a deliberately operational leftover ([[2026-09-16-ai-safety-critical]]).

## See also

- [[spec-driven-development]]
- [[spec-review]]
- [[vibe-heresy]]
- [[doc-gardener]]
- [[decision-context]]
- [[drift]]
- [[acceptance-test-bdd]]
- [[openspec]]
- [[2026-09-28-ai-gaming-tech]]
