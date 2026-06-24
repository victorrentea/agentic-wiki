---
title: CLAUDE.md (Project Memory)
category: concept
tags: [claude-md, memory, drift, context-window, onboarding]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---


`CLAUDE.md` is the project memory file loaded into every agent session from the current working directory — and because it costs [[context-window]] on turn 1, its hygiene matters more than its completeness.

## The hygiene ritual

The opening line of every sprint retrospective on your `CLAUDE.md`:

> "What in here is **absolutely obvious from your training data** that I can safely remove without risking you breaking the rule?"

Out go the platitudes the model already knows: "use constructor injection not `@Autowired`", "run `mvn test` / `npm test`", "validate `@RequestBody`", "keep line length". Then ask: *any internal conflicts? can we make it more concise?* — because a contradiction inside the file is a direct cause of [[hallucination]] (the model silently resolves the tension instead of flagging it).

## Curate, don't generate

The #1 mistake is dumping your knowledge "like writing your will" or pasting your conventions wiki verbatim. Instead: *"read that wiki, keep only what's special for us, write it concisely in your own words."* A bloated, generic, **AI-generated** `CLAUDE.md` reportedly makes tasks perform *worse* and cost ≈20% more — the exact number is anecdotal but the mechanism is sound: a generic memory file is pure [[dumb-zone]] fuel.

## Three moves to shrink a bloated file

When a `/init`-generated `CLAUDE.md` balloons to hundreds of lines, apply these in order:

1. **Strip the obvious** — don't tell a Java agent how to run `mvn test`; it knows.
2. **Split into per-folder files** — `frontend/CLAUDE.md` loads only when the agent touches that folder; the root file stays lean.
3. **Extract into a [[agent-skill|skill]]** — durable, reusable knowledge lives in a skill whose body lazy-loads on demand.

Keep the root file under **≈100 lines**. Everything in the root `CLAUDE.md` loads in *every* conversation — it is a constant tax, not a free resource. The real value of a hand-curated file is project NFRs the agent cannot know from training data: expected throughput, row counts, resilience SLAs, which APIs are rate-limited.

The cross-harness equivalent is [[agents-md|AGENTS.md]] (symlink `AGENTS.md → CLAUDE.md` so Codex can read it too); the full curation discipline — the anti-dumping bar, the seed-of-heresy Socratic enrichment loop, sub-folder injection, retrospect-every-sprint — lives in [[agents-md-discipline]], and this file is the *project* tier of the four [[memory-layers]] (user / project / team / company).

## Growth and drift

A good `CLAUDE.md` **grows like an onboarding doc** — each new rule fixes the reason the AI last went off the rails, the way you'd onboard a brilliant-but-junior colleague. Better still, replace hard rules with a [[tripwire]]. And mind [[drift]]: bump Spring Boot or Angular and the file goes stale, so refresh it periodically (≈monthly) with `/init`. Loading deeper context is governed by [[progressive-disclosure]]; sharing it across repos is the [[cross-repo-knowledge]] pattern.

## See also
- [[progressive-disclosure]]
- [[agent-skill]]
- [[agents-md]]
- [[agents-md-discipline]]
- [[memory-layers]]
- [[tripwire]]
- [[drift]]
- [[hallucination]]
- [[cross-repo-knowledge]]
- [[dumb-zone]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
