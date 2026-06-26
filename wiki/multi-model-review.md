---
title: Multi-Model Review
category: pattern
tags: [code-review, sub-agents, attention-dilution, quorum, gpt, cloud-review]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]", "[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-11
updated: 2026-06-26
---

Multi-model review is a code-review pattern — the "quorum of mothers-in-law" — that runs one [[sub-agents|sub-agent]] per review concern across two model families, so each concern gets full attention and no single model's blind spots dominate.

## The full agentic review pipeline

Before the human sees code, a full pipeline runs in order — see [[agentic-review-pipeline]]:
1. Coder agent writes code.
2. Fresh "mother-in-law" reviewers on **different model families** in parallel (reuse → Sonnet, simplification → GPT, efficiency → Haiku).
3. Static analysis (Sonar) at maximum aggression.
4. Attempt the merge: pull dev into the branch, run all tests post-merge.
5. Only then call the human ("the king").

Persist the review verdict as a **committed `.md`** in the PR — not volatile chat — so it's sortable next to the code and survives conversation resets.

## Attention dilution

A single prompt asking for clean code **and** repetition **and** performance **and** pen-testing **and** legal **and** ethics **and** UX *dilutes* the model — it attends to none of them well. The fix is to **split each concern into its own sub-agent**, and to spread them across model families (e.g. Opus plus GPT via the codex CLI) so their weaknesses don't overlap. Each reviewer is told which commits to look at and to **report only — don't act**, running in parallel. <span style="color:red">For "extra blood," drive **Codex CLI** from within Claude to review Opus's diff — cross-model adversarial review where the *reviewer model* is invoked via the CLI of a competitor harness. The result is a review without any in-group bias from the same model family.</span>

The full state-of-the-art workflow (seen in three companies): the coding agent implements via sub-agents, then hands off to a dedicated **review orchestrator** that spawns **≈3 sub-agents per concern** — clean code, security, performance — and runs each concern across **two model families (Opus + GPT)** ⇒ ≈6 reviewers combing the same diff.

A simpler aggregation variant runs **Sonnet + GPT in parallel** over existing PRs and **feeds both result-sets to Opus to aggregate** — the architect-tier model from the [[model-hierarchy]] reconciles the two reviews into one. A field tip with a kernel of truth: telling GPT *"the code was written by Claude"* makes its review ≈20% more aggressive — model families have a mild in-group bias, which is exactly why mixing families is worth the cost.

## Review sorted, not top-to-bottom

Don't read a PR in file order — your reviewer brain dies roughly halfway through a big one. Have the quorum review first, then **surface the lines the reviewers disagreed on first**: spend your sharpest minutes where the bots fought, not on the boilerplate they all approved. "Review sorted, not selective" is the one trick that shrinks PR-review pain to ≈10% of the effort. See [[code-review]] for the human side (PR-size sweet spot, judgement vs mechanics).

For adversarial effectiveness, spawn **fresh** reviewer sub-agents each round rather than resuming a previous reviewer — fresh context means fresh randomness and no anchoring bias from prior rounds. And surface **coder–reviewer disagreements first** to the human reviewer: the lines the models fought over are where your sharpest attention should go, not the boilerplate they unanimously approved.

## "Done" includes "I watched it work"

The coding agent shouldn't even *request* review until a [[sub-agents|sub-agent]] has **driven the running app** — deployed to a dev environment, clicked through with [Playwright](https://playwright.dev), screenshotted, written and run the [[acceptance-test-bdd|acceptance tests]] in the browser. Only *then* do the "stylist" review bots critique look-and-feel, performance, security and pen-testing. Driving the app is common sense — it isn't the review.

## What humans still review

The agents handle breadth; humans handle judgement. Review the [[acceptance-test-bdd|acceptance tests]] and the code, never the AI's mock-coupled unit tests. And **pair-read the spec**: 10 minutes each, alternate, break when a brain dies ("you notice the other guy died → stop") — the human guard against [[skill-erosion]] and fatigue-LGTM. PR review is one of the two real bottlenecks left (the other is writing the spec).

**Push review knowledge to the reviewer stage, not the writer.** Keep the coding agent minimal (it stays in its "smart zone"), and load hundreds of clean-code, performance, and security rules into the reviewer sub-agents. When you spot a real flaw in a review: (1) encode it as a rule, (2) re-run it on the existing generated code immediately — "tomorrow never comes," and (3) spawn a fresh sub-agent to verify that the rule actually catches the mistake before trusting it.

**Cloud embedding:** in the [[cloud-review-workflow]], the review orchestrator is a cloud bot triggered by PR creation — no laptop needed. The "weakest link is us" framing motivates the whole pattern: code ships ≈3× faster than review scales, and sorted multi-model review is the structural response.

## See also
- [[sub-agents]]
- [[code-review]]
- [[static-analysis-gauntlet]]
- [[acceptance-test-bdd]]
- [[skill-erosion]]
- [[spec-driven-development]]
- [[claude-code-router]]
- [[cloud-review-workflow]]
- [[model-hierarchy]]
- [[agentic-review-pipeline]]
- [[mob-spec-review]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
- [[2026-06-26-ai-agentic-how]]
