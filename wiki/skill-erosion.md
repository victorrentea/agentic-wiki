---
title: Skill Erosion
category: concept
tags: [human-factors, learning, review, team, quiz]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

Skill erosion is the gradual atrophy of an engineer's own competence when AI writes all the code — the human risk that mirrors the machine risk of [[drift]].

## Staying sharp

Concrete countermeasures discussed for keeping a team's skills from rotting:

- **Sprint quiz from commits.** At sprint end, have the AI generate a quiz *from the sprint's commits* — "why did we do X?". If more than one of four teammates answers wrong, spend 30 minutes understanding that decision.
- **Throwaway scaffolds.** Ask the AI to scaffold a tiny throwaway project for anything you've never played with (semaphores, etc.) to actually learn it.
- **Understand 100% of the words you review** — let no suspicious term slip by. Aggressively rename vague code; term confusion is also a [[hallucination]] cause.
- **Mob-review the markdowns** in 2–4 people to beat fatigue-LGTM; if one person doesn't get it, stop and discuss.
- Keep coding as a hobby to stay in shape.

This is the human half of the [[multi-model-review]] discipline (pair-reading the spec) and a direct consequence of [[vibe-coding]] at scale — the more you delegate, the more deliberately you must invest in understanding what was delegated.

<span style="color:red">**Front-load the analysis.** The reflex worth fighting is rushing to prompt before you've thought: a spec or plan **agglomerates ≈7 days of decisions into ≈1 hour**, and if you can't *imagine* where the agent will go you lose the thread after decision #4. The rule underneath it all, even when you're alone: **if you don't understand something, stop — never approve what you don't understand** (the core of honest [[code-review]]).</span>

## See also
- [[vibe-coding]]
- [[drift]]
- [[hallucination]]
- [[multi-model-review]]
- [[spec-driven-development]]
- [[2026-06-11-ai-playtika]]
