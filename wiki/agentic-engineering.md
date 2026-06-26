---
title: Agentic Engineering
category: concept
tags: [agentic-engineering, discipline, autonomy, spec-driven, overview]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---

Agentic engineering is the disciplined practice of building software *through* autonomous agents — investing human effort in specs, knowledge, guardrails, and review rather than in typing code — the deliberate opposite of [[vibe-coding]].

## The defining principle

**Autonomy scales with the engineer:** "the stronger the agentic engineer, the longer the flows you can run." A weak operator can supervise a single short prompt; a strong one front-loads enough thinking that an agent can run for hours unattended (the longest single autonomous run cited was **6 hours** — the agent fixed a CI pipeline, waited 40 minutes for the build, saw it fail, fixed, and pushed). Weekend-scale runs are not one loop but the [[ralph-loop]].

## What the discipline consists of

The day's whole arc is the toolkit:

- **Front-load thinking** — [[grill-me|grill]] the requirement, write a [[spec-driven-development|spec]], leave a [[handover]].
- **Manage context** — respect the [[token-economy]] and stay out of the [[dumb-zone]] (via [[sub-agents]], [[handover]], [[plan-mode]]).
- **Engineer the knowledge** — curate [[claude-md]], grow [[tripwire|tripwires]], share via [[cross-repo-knowledge]].
- **Constrain the output** — [[acceptance-test-bdd|acceptance tests]] from one side, design decisions from the other, then the [[static-analysis-gauntlet]] (machines draw blood first) and [[multi-model-review]] / [[code-review]] (sorted, judgement-only) where the human spends a fading attention budget.
- **Guard the human** — beware [[ai-addiction]] and [[skill-erosion]].

The engineer becomes a "manager of agents", not a viper — see [[vibe-coding]] for the contrast.

**Two slogans capture the spine of the discipline.** First, *stop being the servant of the AI*: don't copy-paste between a chat and your editor (the [[chop-ai-waiter|CHOP anti-pattern]]) — integrate the agent and give it a [[feedback-loop]] so it checks its own work. Second, *the new skill is seeing, testing, and reproducing the problem, not fixing it* — *"move your focus from typing to feeling the problems"* (races, deadlocks, load). The agent fixes almost anything once you point it at an observable loop; your job is to make the problem visible and to judge the result. Matching the right [[model-hierarchy]] tier to the task is part of that judgment.

**Every AI failure is an opportunity to enrich the plan or the context.** If the agent gets it wrong, the first question is: what did the agent not know (context), or what was the spec unclear about (plan)? Make "what did the agent learn this sprint?" the first item in every sprint retro — congratulate whoever added good insight to [[claude-md]]. Treat the agent like a junior who just joined: pair, babysit, show the way.

As agents take on more, the human role shifts toward: specifying intent clearly, reviewing the result judiciously, and maintaining the judgment and craft that machines cannot supply. The production horizon includes [[firefighter-agent|firefighter agents]] recovering incidents autonomously before the human connects, and "all day in Claude Code" workflows where scoped MCP access to GitHub, Jira, CI, deploy tools, and Slack replaces the manual tool-switching. The discomfort with this is emotional, not technical.

## See also
- [[vibe-coding]]
- [[spec-driven-development]]
- [[ralph-loop]]
- [[token-economy]]
- [[multi-model-review]]
- [[skill-erosion]]
- [[firefighter-agent]]
- [[ci-green-loop]]
- [[feedback-loop]]
- [[chop-ai-waiter]]
- [[model-hierarchy]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
