---
title: Grill Me
category: tool
tags: [skill, ideation, brainstorming, playwright, agent-workflow]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]"]
created: 2026-06-10
updated: 2026-06-11
---

A minimal 3-line Claude Code skill by Matt Pocock that fires 10–20 pointed, adversarial questions at an idea or plan, forcing you to stress-test your thinking before committing to execution.

## What it does

[grill-me](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) is deliberately tiny — the skill definition itself is three lines. You invoke it on a rough idea, an architecture sketch, or a product proposal, and the agent generates a rapid-fire set of probing questions covering assumptions, risks, edge cases, and missing context. The volume (10–20 questions at once) is intentional: it surfaces blind spots you wouldn't think to ask yourself.

## The Playwright restriction

The recommended configuration for business brainstorms: grant the agent **browser-only access via Playwright** to your staging environment, and nothing else — no code read, no file write, no shell. This constraint keeps the conversation at the functional/product level. If the agent has code access, it gravitates toward implementation questions ("should this be a microservice?") rather than business questions ("who actually pays for this?"). The tool surface shapes the conversation altitude.

## Four lines are a declaration of war

A four-line ticket ("add sorting & pagination to the owner's screen") is **a declaration of war** — the grill (and a room of engineers) routinely finds ≈10 questions hiding in it: server- or client-side? what is the business *actually* trying to achieve? how do you even sort phone numbers (don't — sort name & city only)? change the sort while on page 30 → jump to page 1? page-size selector and default? Filtering is almost inevitable but ≈10× more complex — don't even propose it. The point: **you don't *require* the product, you *propose* it** — and missing context (e.g. "100,000 owners within a year") is what makes the agent ask "dumb" questions, so volunteer scale, request/sec, latency, and load distribution.

## Two grills — keep them apart

- **Technical grill** — start it *in the folder with the source code*: "if you can answer by reading the code, read the code" (that's why it warms up slowly).
- **Business grill** — give the bot **no code access**, only the ability to click through the staged app (Playwright) and read Jira. *"Business + bot + code = death."* It plays the app, reads tickets, grills the business, and produces an impeccable story.

End every grill with the same instruction: **"summarize the Q&A into a [[handover]]"** — which is exactly the input to [[spec-driven-development]] (paste it into [[openspec]]). And keep tone calm: insulting the agent triggers [[sycophancy]] and it loses impartiality (see "don't insult the AI").

This pattern complements [[ralph-loop]]: grill-me stress-tests the spec *before* you commit an overnight run to executing it, catching bad assumptions cheaply. It pairs naturally with [[llm-wiki]] — a grilling session's outputs make excellent wiki pages because they externalize tacit assumptions. Keep its tool surface tightly scoped to honor [[token-economy]] principles.

## See also

- [[ralph-loop]]
- [[token-economy]]
- [[llm-wiki]]
- [[tool-calling]]
- [[handover]]
- [[spec-driven-development]]
- [[openspec]]
- [[sycophancy]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
