---
title: Goals over Instructions
category: pattern
tags: [prompting, skills, delegation, model-capability]
sources: ["[[2026-08-27-ai-it-services]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-13-ai-insurer]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

A prompt- and skill-authoring style that states the objective and its constraints — what I want, by when, what may be deferred — instead of a step-by-step procedure, letting a model that is better at the task choose the *how*.

## Evidence

A 1,500-line step-by-step fan-out [[agent-skill|skill]] kept disappointing. Rewritten in one go as objectives — *"aggregated summary ASAP; wiki, links, pictures in background afterwards"* — it worked better [[2026-08-27-ai-it-services]]. The lesson is partly about ego: stop micro-directing someone who is better than you at the job.

## The same idea, phrased four ways

- **Skills describe the environment and its problems, not the steps.** A procedure written for last month's model is a temporal trap: the next model would have found a better path, and the steps now hold it back [[2026-08-03-ai-telecom-ops-qa]].
- **Write context and problem, not solution** [[2026-08-06-ai-bank]].
- **Prompt with what hurts, not what to do** — describe the pain and let the agent propose the fix [[2026-08-06-ai-bank]] [[2026-08-13-ai-insurer]].
- **Specs state outcomes and boundaries** — see [[spec-driven-development]] and [[non-goals]]: say what is out of scope rather than scripting the implementation.

## What goals need around them

A goal without constraints invites the agent to do *more* ([[ai-spiraling]], [[non-goals]]). A good goal-shaped prompt carries:

- the **objective** and its priority order (what first, what in the background);
- the **constraints** — deadline, budget, what may be deferred or skipped;
- the **acceptance signal** — how you'll know it's done (a test, a [[feedback-loop]]).

## Limit

Goals replace *judgement-heavy* procedure, not deterministic procedure. Steps that must run the same way every time still belong in a script inside the skill, not in prose — see [[determinism-over-llm]].

## See also

- [[agent-skill]]
- [[agentic-engineering]]
- [[sub-agents]]
- [[spec-driven-development]]
- [[non-goals]]
- [[determinism-over-llm]]
- [[example-driven-prompting]]
- [[feedback-loop]]
