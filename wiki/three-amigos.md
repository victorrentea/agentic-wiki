---
title: Three Amigos
category: pattern
tags: [agile, requirements, bdd, acceptance-tests, tester, planning]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
---

Three Amigos is an agile planning ceremony where a business representative, a developer, and a tester each interrogate a ticket from their own perspective until unambiguous [[acceptance-test-bdd|acceptance tests]] emerge.

## The three perspectives

- **Business** — owns the desired outcome; clarifies value and priority.
- **Developer** — asks about technical boundaries and edge cases; spots hidden complexity.
- **Tester (the "ninja")** — machine-guns absurd corner cases the others wouldn't think to ask. Framing: "we're afraid of the tester; they find bugs where we don't expect them." Examples: a 1 GB profile photo, dragging the carrot to the rabbit while performing another move simultaneously.

The session ends when all three can write consistent [[acceptance-test-bdd|Gherkin acceptance tests]]. Those tests are the concrete deliverable, not a document or a wireframe.

## AI augmentation

In the [[cloud-review-workflow]], the tester role can be played or supplemented by an AI: it reads the ticket and the staged application (via [[browser-mcp|Playwright]]), and generates machine-gun corner-case questions and scenarios. The bot-as-tester still needs a human to validate the scenarios make business sense — the ninja role is hard to fully automate.

## Without Three Amigos

Skipping this ceremony means the developer builds against the first interpretation of the ticket, the tester finds the obvious gaps, and the business gets surprised by both. Three Amigos front-loads that negotiation — the same ≈1-hour investment that [[spec-driven-development]] compresses into a design document.

## See also

- [[acceptance-test-bdd]]
- [[cloud-review-workflow]]
- [[spec-driven-development]]
- [[grill-me]]
- [[vibe-fixer]]
- [[2026-06-11-ai-playtika]]
