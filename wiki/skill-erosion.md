---
title: Skill Erosion
category: concept
tags: [human-factors, learning, review, team, quiz, mob-programming]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-23
---


Skill erosion is the gradual atrophy of an engineer's own competence when AI writes all the code — the human risk that mirrors the machine risk of [[drift]].

## Staying sharp

Concrete countermeasures for keeping a team's skills from rotting:

- **Sprint quiz from commits.** At sprint end, have the AI generate a quiz *from the sprint's commits* — "why did we do X?". If more than one of four teammates answers wrong, spend 30 minutes understanding that decision.
- **Throwaway scaffolds.** Ask the AI to scaffold a tiny throwaway project for anything you've never played with (semaphores, etc.) to actually learn it.
- **Understand 100% of the words you review** — let no suspicious term slip by. Aggressively rename vague code; term confusion is also a [[hallucination]] cause.
- **Mob-review the markdowns** in 2–4 people to beat fatigue-LGTM; if one person doesn't get it, stop and discuss.
- Keep coding as a hobby to stay in shape.
- **One quarter-sprint hands-only** — accept shipping 5–10% of usual velocity; the skill return is worth it.
- **Pair/mob programming** — only 2+ developers working together can match an Opus-level agent on the same task. It is the gold standard for maintaining craft. Reading code at scale is also an emerging bottleneck: the more agents write, the faster review skills must grow.

This is the human half of the [[multi-model-review]] discipline (pair-reading the spec) and a direct consequence of [[vibe-coding]] at scale — the more you delegate, the more deliberately you must invest in understanding what was delegated. <span style="color:red">The human cost compounds in isolation — the "up till 4 a.m. arguing with the agent" loneliness — so the antidote is social (mob/pair) as much as technical; and the durable countermeasure to delegation is learning to *feel* the problem class (a concurrency bug, a deadlock) well enough to judge a fix, the same thesis as the [[feedback-loop]].</span>

**Front-load the analysis.** The reflex worth fighting is rushing to prompt before you've thought: a spec or plan **agglomerates ≈7 days of decisions into ≈1 hour**, and if you can't *imagine* where the agent will go you lose the thread after decision #4. The rule underneath it all, even when you're alone: **if you don't understand something, stop — never approve what you don't understand** (the core of honest [[code-review]]).

## See also
- [[vibe-coding]]
- [[drift]]
- [[hallucination]]
- [[multi-model-review]]
- [[spec-driven-development]]
- [[code-review]]
- [[feedback-loop]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
