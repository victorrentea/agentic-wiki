---
title: CLAUDE.md (Project Memory)
category: concept
tags: [claude-md, memory, drift, context-window, onboarding]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


`CLAUDE.md` is the project memory file loaded into every agent session from the current working directory — and because it costs [[context-window]] on turn 1, its hygiene matters more than its completeness.

## The hygiene ritual

The opening line of every sprint retrospective on your `CLAUDE.md`:

> "What in here is **absolutely obvious from your training data** that I can safely remove without risking you breaking the rule?"

Out go the platitudes the model already knows: "use constructor injection not `@Autowired`", "run `mvn test` / `npm test`", "validate `@RequestBody`", "keep line length". Then ask: *any internal conflicts? can we make it more concise?* — because a contradiction inside the file is a direct cause of [[hallucination]] (the model silently resolves the tension instead of flagging it).

## Curate, don't generate

The #1 mistake is dumping your knowledge "like writing your will" or pasting your conventions wiki verbatim. Instead: *"read that wiki, keep only what's special for us, write it concisely in your own words."* A bloated, generic, **AI-generated** `CLAUDE.md` reportedly makes tasks perform *worse* and cost ≈20% more — the exact number is anecdotal but the mechanism is sound: a generic memory file is pure [[dumb-zone]] fuel.

## Growth and drift

A good `CLAUDE.md` **grows like an onboarding doc** — each new rule fixes the reason the AI last went off the rails, the way you'd onboard a brilliant-but-junior colleague. Better still, replace hard rules with a [[tripwire]]. And mind [[drift]]: bump Spring Boot or Angular and the file goes stale, so refresh it periodically (≈monthly) with `/init`. Loading deeper context is governed by [[progressive-disclosure]]; sharing it across repos is the [[cross-repo-knowledge]] pattern.

## See also
- [[progressive-disclosure]]
- [[tripwire]]
- [[drift]]
- [[hallucination]]
- [[cross-repo-knowledge]]
- [[dumb-zone]]
- [[2026-06-11-ai-playtika]]
