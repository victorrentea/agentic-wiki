---
title: Sprint Design Quiz
category: pattern
tags: [sprint, code-review, skill-erosion, orchestration, accountability]
sources: ["[[2026-06-23-ai-garmin]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-24
updated: 2026-06-25
---

A sprint design quiz is an automated end-of-sprint exercise where an agent scans all merged PRs and asks each developer one or two questions about the architectural decisions in their PRs — a lightweight guard against losing comprehension of AI-written code.

## Why it matters

When agents write most of the code, there is a real risk that developers approve PRs they don't fully understand. The sprint design quiz surfaces this: if a developer cannot answer a simple question about the design decision in their own PR, the team is shipping code without comprehension — a precursor to [[cognitive-debt]].

## How it works

1. At sprint end, an agent reads all merged PRs.
2. For each PR it generates 1–2 questions about the key architectural or design decisions — not implementation trivia, but *"why was this approach chosen?"* questions.
3. The team answers synchronously (retrospective) or asynchronously (a Slack thread or Jira comment).
4. Non-answers or wrong answers flag PRs for a deeper retrospective discussion.

The questions are a forcing function: *"Are we still in charge, or squeezed?"*

## Relation to skill erosion

The sprint design quiz is a lightweight countermeasure to [[skill-erosion]] — the gradual loss of technical depth as developers defer more decisions to agents. Running it regularly ensures the team retains authorship over architectural choices even when agents do the typing.

## Knowledge spreading and bus-factor

<span style="color:red">The Kambi framing: at sprint end, have AI quiz the whole team *"why did we decide X?"* from merged PRs and design docs — not just the PR author. This spreads architectural knowledge across the team and fights bus factor (the risk that only one person understands a design decision). Non-answers flag PRs for deeper retrospective discussion; the quiz is a forcing function: *"Are we still in charge, or squeezed?"*</span>

## See also
- [[skill-erosion]]
- [[cognitive-debt]]
- [[code-review]]
- [[multi-model-review]]
- [[agentic-engineering]]
- [[2026-06-23-ai-garmin]]
- [[2026-06-22-ai-kambi]]
