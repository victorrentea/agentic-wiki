---
title: Mob Spec Review
category: pattern
tags: [spec-driven, code-review, pair-programming, fatigue, decision-making]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-03-ai-bank]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-10-ai-consultancy]]"]
created: 2026-06-25
updated: 2026-10-13
---

A practice of reviewing architecture or spec documents with two or more humans present simultaneously, rather than asynchronously, to prevent fatigue-driven false approvals and anchor shared decisions.

## Overview

No architecture markdown or [[spec-driven-development|spec]] is reviewed in isolation — always 2+ humans together (mob). A 30-minute joint session condenses decisions that would otherwise scatter across a week of async back-and-forth.

The pattern defuses the deadliest phrase in AI review: *"looks good to me"* (fatigue-LGTM) followed later by *"wait, who decided this?"*. When reviewers work sequentially and asynchronously, it is easy for each person to assume someone else validated the hard parts.

<span style="color:red">**Why one reviewer is not enough — measured in a room.** One wrong line in a 220-line spec hides a real bug, and a room of 27 people missed it *even after being told where to look* (the example was a name rendered as "Potter, Harry") ([[2026-08-10-ai-consultancy]]).</span> <span style="color:red">The **Hawthorne effect** ([wiki](https://en.wikipedia.org/wiki/Hawthorne_effect)) — people perform better when observed — is part of why mobbing works: it is the natural antidote to LGTM on specs ([[2026-06-05-ai-consultancy]], [[2026-07-03-ai-bank]]).</span> <span style="color:red">The stakes compound: like a sniper, 1 mm off at the barrel and you hit the wrong target hundreds of metres later ([[2026-07-20-ai-telecom]]).</span>

## <span style="color:red">Calibration: what a real review costs</span>

- <span style="color:red">Properly reviewing one spec takes **≈3 hours with ≈5 developers at a projector** — *"none of you can explain every line of this file — and I'm paginating a grid."* The developers who invest in this become the team's rockstars ([[2026-07-03-ai-bank]]).</span>
- <span style="color:red">Governance rule: **every change bigger than a day or two gets a markdown review** ([[2026-07-03-ai-bank]]). One team cited from abroad forbids reviewing a markdown alone — 5 people on the same doc ([[2026-07-20-ai-telecom]]).</span>
- <span style="color:red">In 10 minutes of live review one session found: a wrong architectural assumption ("OpenAPI is the source of truth for DTOs" when the project derives OpenAPI from Java), a requirement present in the design but missing from the proposal, an "overload" that would have left dead code, and an over-engineered race-condition counter ([[2026-04-16-ai-safety-critical]]).</span>

## Mechanics

- Reviewers alternate reading in ≈10-minute chunks. When one person's brain fatigues, the other takes over — stop when both are done.
- <span style="color:red">**Assign paragraphs to different voices:** each person narrates their section aloud and the group confirms; one firm reads the markdown aloud taking turns and stops at any word someone doesn't understand ([[2026-08-10-ai-consultancy]], [[2026-06-05-ai-consultancy]]).</span>
- <span style="color:red">**Mindset: OCD++** — *"let the mother-in-law out of you, the supreme critic"*. Give each reviewer a quota: *"find three things you'd push back on"* ([[2026-04-16-ai-safety-critical]], [[2026-04-27-ai-healthcare-software]]).</span>
- <span style="color:red">**Never review specs at the end of the day** — you have a circadian rhythm, the AI doesn't. *"If your brain dies halfway in a markdown, stop. Take a shower, go to bed."* See [[system-2-fatigue]] ([[2026-04-27-ai-healthcare-software]], [[2026-07-22-ai-agentic-how]]).</span>
- <span style="color:red">**Invite the specialist the spec touches** — e.g. pull the DBA in when a sort/paging spec is reviewed; the collation question needed one ([[2026-07-22-ai-agentic-how]]).</span>
- If a concept won't click after re-reading, have the agent explain it visually: a diagram, a small runnable experiment, or an animation. <span style="color:red">Concretely: **stop and have the agent scaffold a ≈200-line runnable demo** of the technique the spec leans on (e.g. Hibernate `@BatchSize`) — a 10–30 min learning session — or have the agent quiz you ([[2026-08-10-ai-consultancy]]).</span>
- Pair with [[multi-model-review]]: run automated agents first (see [[agentic-review-pipeline]]), then mob the residual issues together.
- The [[three-amigos]] ritual (BA + QA + Dev) is the closest classical analogue.

## <span style="color:red">Learning time, not inefficiency</span>

<span style="color:red">Four people on one markdown looks expensive; one company calls it "our learning time" — people articulate, explain and learn from each other with Claude in the background, which is the most direct counter to [[skill-erosion]] ([[2026-06-05-ai-consultancy]], [[2026-06-08-ai-security-tools]]).</span> <span style="color:red">Reviewing also teaches: twice in one session the AI introduced a technique the reviewer didn't know — "1–0 for AI" ([[2026-04-16-ai-safety-critical]]).</span>

## When to invoke

- Any spec, ADR, or architecture markdown that will shape more than one sprint's worth of work.
- Decisions that involve [[skill-erosion]] risk — cases where rubber-stamping by an AI-fatigued human could quietly lower the quality bar.
- Any document longer than can be held in one person's working memory.

## Pitfalls

- Scheduling mob reviews too late (after the agent has already written code against the spec) removes leverage.
- Mob reviews of raw AI output instead of distilled specs waste the session on implementation noise.

## See also

- [[spec-driven-development]]
- [[code-review]]
- [[multi-model-review]]
- [[skill-erosion]]
- [[three-amigos]]
- [[agentic-review-pipeline]]
- <span style="color:red">[[system-2-fatigue]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[sprint-design-quiz]]</span>
- <span style="color:red">[[2026-07-03-ai-bank]]</span>
- <span style="color:red">[[2026-08-10-ai-consultancy]]</span>
