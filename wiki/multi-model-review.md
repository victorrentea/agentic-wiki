---
title: Multi-Model Review
category: pattern
tags: [code-review, sub-agents, attention-dilution, quorum, gpt]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

Multi-model review is a code-review pattern — the "quorum of mothers-in-law" — that runs one [[sub-agents|sub-agent]] per review concern across two model families, so each concern gets full attention and no single model's blind spots dominate.

## Attention dilution

A single prompt asking for clean code **and** repetition **and** performance **and** pen-testing **and** legal **and** ethics **and** UX *dilutes* the model — it attends to none of them well. The fix is to **split each concern into its own sub-agent**, and to spread them across model families (e.g. Opus plus GPT via the codex CLI) so their weaknesses don't overlap. Each reviewer is told which commits to look at and to **report only — don't act**, running in parallel.

## What humans still review

The agents handle breadth; humans handle judgement. Review the [[acceptance-test-bdd|acceptance tests]] and the code, never the AI's mock-coupled unit tests. And **pair-read the spec**: 10 minutes each, alternate, break when a brain dies ("you notice the other guy died → stop") — the human guard against [[skill-erosion]] and fatigue-LGTM. PR review is one of the two real bottlenecks left (the other is writing the spec).

## See also
- [[sub-agents]]
- [[acceptance-test-bdd]]
- [[skill-erosion]]
- [[spec-driven-development]]
- [[claude-code-router]]
- [[2026-06-11-ai-playtika]]
