---
title: Grill Me
category: tool
tags: [skill, ideation, brainstorming, playwright, agent-workflow]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

A minimal 3-line Claude Code skill by Matt Pocock that fires 10–20 pointed, adversarial questions at an idea or plan, forcing you to stress-test your thinking before committing to execution.

## What it does

[grill-me](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) is deliberately tiny — the skill definition itself is three lines. You invoke it on a rough idea, an architecture sketch, or a product proposal, and the agent generates a rapid-fire set of probing questions covering assumptions, risks, edge cases, and missing context. The volume (10–20 questions at once) is intentional: it surfaces blind spots you wouldn't think to ask yourself.

## The Playwright restriction

Victor's recommended configuration for business brainstorms: grant the agent **browser-only access via Playwright** to your staging environment, and nothing else — no code read, no file write, no shell. This constraint keeps the conversation at the functional/product level. If the agent has code access, it gravitates toward implementation questions ("should this be a microservice?") rather than business questions ("who actually pays for this?"). The tool surface shapes the conversation altitude.

This pattern complements [[ralph-loop]]: grill-me stress-tests the spec *before* you commit an overnight run to executing it, catching bad assumptions cheaply. It pairs naturally with [[llm-wiki]] — a grilling session's outputs make excellent wiki pages because they externalize tacit assumptions. Keep its tool surface tightly scoped to honor [[token-economy]] principles.

## See also

- [[ralph-loop]]
- [[token-economy]]
- [[llm-wiki]]
- [[tool-calling]]
- [[2026-06-10-spring-ai-itkonekt]]
