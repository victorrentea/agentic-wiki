---
title: Grill Me
category: tool
tags: [skill, ideation, brainstorming, playwright, agent-workflow, requirements]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]", "[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-10
updated: 2026-06-26
---

A minimal 3-line Claude Code skill by Matt Pocock that fires 10–20 pointed, adversarial questions at an idea or plan, forcing you to stress-test your thinking before committing to execution.

## What it does

[grill-me](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) is deliberately tiny — the skill definition itself is three lines. You invoke it on a rough idea, an architecture sketch, or a product proposal, and the agent interviews you **one question at a time**, spawning a [[sub-agents|sub-agent]] into the codebase ("sent a baby into the codebase") to answer what it can find itself rather than asking you. This keeps the grill focused on *genuinely unknown* decisions. The depth is the point: 10–20 hidden questions in a "4-line ticket" is normal.

## The Playwright restriction

The recommended configuration for business brainstorms: grant the agent **browser-only access via Playwright** to your staging environment, and nothing else — no code read, no file write, no shell. This constraint keeps the conversation at the functional/product level. If the agent has code access, it gravitates toward implementation questions ("should this be a microservice?") rather than business questions ("who actually pays for this?"). The tool surface shapes the conversation altitude.

## Four lines are a declaration of war

A four-line ticket ("add sorting & pagination to the owner's screen") is **a declaration of war** — the grill (and a room of engineers) routinely finds ≈10 questions hiding in it: server- or client-side? what is the business *actually* trying to achieve? how do you even sort phone numbers (don't — sort name & city only)? change the sort while on page 30 → jump to page 1? page-size selector and default? Filtering is almost inevitable but ≈10× more complex — don't even propose it. The point: **you don't *require* the product, you *propose* it** — and missing context (e.g. "100,000 owners within a year") is what makes the agent ask "dumb" questions, so volunteer scale, request/sec, latency, and load distribution.

**Don't over-specify — let the questions expose the vagueness.** A counter-intuitive twist: if you *hard-code* the answer (e.g. the exact target colour), the agent confidently does the wrong thing. Leave the option open and a strong agent starts *asking* — and its questions force the humans to actually look. (The canonical catch: refusing to pin the brand colour made the agent ask, which surfaced that two screens used two slightly-different oranges — the "Kumquat" moment.) Over-specify → confident wrong; leave open → the ambiguity surfaces. The grill also catches *scope* ambiguity others miss — e.g. "sortable by all properties and filterable by high-effort" actually means **filter by effort only, sort by all properties**. A run of ≈3 questions is disappointing; prior groups reached **17**. Hidden questions a good grill surfaces: default order? what *is* "high effort"? persist sort across filtering / tab-switch / restart? available offline? ascending only or toggle? how many rows now and in a year? server-side pagination (and then server-side sort)? When the human can't answer, the agent posts a clarifying comment on the issue for the business to answer async — the [[agentic-corporation|blackboard]] pattern.

## Research-only minions before the grill

<span style="color:red">For large spec-driven tasks (a week, five microservices, 55 classes), send **read-only research minions** before you start the grill: *"Do not suggest. Do not perform. Don't you dare. You just freaking read."* The same compulsion that made clean-code engineers reformat a class on sight hits agents too — they will start fixing things if you give them write access during the research phase. Send them read-only first, summarize what they found, then grill on that foundation.</span>

## Two grills — keep them apart

- **Technical grill** — start it *in the folder with the source code*: "if you can answer by reading the code, read the code" (that's why it warms up slowly).
- **Business grill** — give the bot **no code access**, only the ability to click through the staged app (Playwright) and read Jira. *"Business + bot + code = death."* It plays the app, reads tickets, grills the business, and produces an impeccable story. On mobile, the same role is played by [[adb-automation|`adb`]] — the grill can browse the running emulator to answer its own UI questions when the "PO" *"doesn't know what JSON is."*

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
- [[pg-trgm]]
- [[keyset-pagination]]
- [[adb-automation]]
- [[agentic-corporation]]
- [[grilling-in-reverse]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
- [[2026-06-26-ai-agentic-how]]
