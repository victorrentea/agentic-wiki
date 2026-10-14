---
title: Gray Factory
category: pattern
tags: [code-review, dark-factory, sampling, review-budget, agentic-workflow, guardrails]
sources: ["[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-23-ai-software-product]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

A gray factory is the realistic near-term target between fully human-reviewed code and a lights-out "dark factory": humans deliberately review only a sample of the agent's output — half the lines, then less — after months of transferring their knowledge into the agent's markdowns and guardrails.

*"A factory with the lights at half, not a dark factory."* ([[2026-09-07-ai-bank]])

## From vibe coding to dark factories in one year

The canonical public statement is the Devoxx Belgium talk *Responsibly Dimming the Lights in the Software Factory* ([[2026-10-06-gray-factory-devoxx]]): in one year the industry went from [[vibe-coding]] to [lights-out](https://en.wikipedia.org/wiki/Lights_out_(manufacturing)) factories. StrongDM's AI team published its rules in February 2026 — *"Code must not be written by humans"* and *"Code must not be reviewed by humans"*, with validation by scenario tests and simulations replacing review ([factory.strongdm.ai](https://factory.strongdm.ai), [Simon Willison's write-up](https://simonwillison.net/2026/Feb/7/software-factory/)); the talk adds that Uncle Bob followed in April 2026. OpenAI's [harness-engineering](https://openai.com/index/harness-engineering) experiment is the other reference point.

The gray factory is the responsible version: the lights go down, but **consciously** — not "LGTM, I'm tired" — and humans stay in charge ([[2026-09-04-ai-software-company]]).

## The dimming rule

Earn the right to read less, PR by PR, per repository:

- **Hard review budgets first**: ≤500 lines and ≤45 minutes per review, in pair or mob ([[2026-09-07-ai-bank]]); stop reading at 500 lines regardless ([[2026-09-04-ai-software-company]]).
- **Then sample.** The most recent formulation: *after 10 consecutive good PRs, review only half; any bad one resets the counter* ([[2026-10-09-ai-jax-london]], also [[2026-09-30-ai-enterprise]], where the reset needs a mistake confirmed by ≥2 colleagues). Earlier sessions used 20 impeccable PRs as the threshold ([[2026-09-04-ai-software-company]], [[2026-09-16-ai-safety-critical]], [[2026-09-28-ai-gaming-tech]], [[2026-10-01-ai-bank]]).
- **Keep halving**: "review half the lines, then half again" ([[2026-10-06-gray-factory-devoxx]]) — 50% → 25% → 10% → 5% in one formulation ([[2026-09-07-ai-bank]]). 10% may be the floor; 0% probably never ([[2026-09-04-ai-software-company]]).
- **Timeline**: ≈9 months from 50% to 20% to 10%, proven on greenfield, not yet on brownfield ([[2026-07-01-ai-java-community]]). *"Do expertise extraction consistently for 6–9 months on a non-legacy project and you review only ≈50% of AI-generated code"* ([[2026-07-23-ai-software-product]]).
- **Rigour is a potentiometer**: an internal tool for 20 users is not a public B2C app ([[2026-09-30-ai-enterprise]]).

Not reading all the code is a **convention the team explicitly assumes**, not a private shortcut — and you stay fluent in the generated language (one 80k-line course tool had ≈10 lines read). The cardinal sin is the opposite: **handing a colleague code to review that you haven't read yourself** (Simon Willison, [code proven to work](https://simonwillison.net/2025/Dec/18/code-proven-to-work/)) ([[2026-09-30-ai-enterprise]]). *"The biggest danger: approving what you didn't understand"* ([[2026-10-09-ai-jax-london]]).

## Preconditions

You can only dim the lights on top of a full agentic workflow ([[2026-09-07-ai-bank]]):

1. grill → spec → implement → hooks → quality gate → green build → merge-from-main → adversarial review sub-agents (see [[grill-me]], [[spec-driven-development]], [[ci-green-loop]], [[multi-model-review]]);
2. **gates that stop the agent** — no starting work on files an unapproved PR already touches ([[policeman-agent]]), a [[doc-gardener]] loop against [[vibe-heresy]];
3. **three humans left in the flow**: the author, a second reviewer, and the CODEOWNERS elder ([[codeowners-elders]]);
4. **cheap deterministic gates before any reviewer**, human or agent — see [[static-analysis-gauntlet]] and [[agentic-review-pipeline]];
5. **pre-chewed output** for the sampled review: decisions the agent took alone with its confidence, a before/after video, requirement-level tests — see [[human-review-dashboard]] and [[semantic-coverage]]. Thousand-line PRs get rubber-stamped; split them into [[stacked-prs]].

Full spec markdowns go on **≤20% of issues**; the rest go straight to a direct prompt ([[2026-10-06-gray-factory-devoxx]], [[2026-09-30-ai-enterprise]]).

## The upgrade loop ("tower defense")

Every defect that slips past review means upgrading whatever agent or guardrail missed it — the grill, a reviewer agent, a skill, a Sonar rule ([[2026-10-06-gray-factory-devoxx]]).

1. **Root-cause the escape before blaming the AI.** The usual culprits: LGTM without reading (3 of 4 times, per one room; 4 of 5 in another), fuzzy requirements nobody owned, or a rule never written into markdown. Ask *"what did the AI not know?"* ([[2026-07-23-ai-software-product]]) — a careless upfront discussion, a requirements glitch, or an agent that needs "brain surgery" ([[2026-10-09-ai-jax-london]]).
2. **Edit that markdown** — push the lesson into the skill, AGENTS.md or reviewer prompt ([[self-improving-knowledge]]).
3. **Revert and re-run** to prove the fix catches it — "reinforcement learning with your wallet" ([[2026-09-04-ai-software-company]]).
4. **Back it with guardrails the agent cannot remove**: a [pre-commit hook](https://github.com/victorrentea/petclinic/blob/main/.githooks/pre-commit) and [CODEOWNERS](https://github.com/victorrentea/petclinic/blob/main/.github/CODEOWNERS) (see [[enforcement-ladder]]).

## Factory culture

- **Don't let one "AI champion" build a single factory for everyone** — each team grows its own flow ([[2026-10-06-gray-factory-devoxx]]).
- **Factory builders pair daily with a random tech lead — and log who**, so knowledge spreads and the factory isn't one person's.
- Rewrite the community skills you start from until the workflow is yours; skills unchanged for months mean nobody is learning ([[skills-governance]]).
- Watch [[cognitive-debt]]: the less code humans read, the more deliberately they must keep the decisions in their heads.

## See also

- [[agentic-corporation]]
- [[code-review]]
- [[agentic-review-pipeline]]
- [[multi-model-review]]
- [[codeowners-elders]]
- [[human-review-dashboard]]
- [[semantic-coverage]]
- [[stacked-prs]]
- [[static-analysis-gauntlet]]
- [[enforcement-ladder]]
- [[cognitive-debt]]
- [[vibe-coding]]
- [[2026-10-06-gray-factory-devoxx]]
