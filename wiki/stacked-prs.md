---
title: Stacked PRs
category: pattern
tags: [code-review, pull-requests, git, review-budget, graphite]
sources: ["[[2026-08-31-ai-software-vendor]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Splitting a large agent-generated change into a chain of small PRs, each branched from the previous one, so that every review stays inside a human's attention budget.

## Why: review is the bottleneck now

Writing code stopped being the bottleneck; [[code-review]] is. Agents happily produce 3,000–10,000-line diffs overnight, and *"thousand-line PRs get rubber-stamped"* [[2026-10-06-gray-factory-devoxx]]. The heuristics quoted across sessions agree on the shape, not the exact number:

| PR size | What happens |
|---|---|
| <100 lines | reviewers find ≈10 issues [[2026-08-31-ai-software-vendor]] |
| ≈200 lines | fits a **30–40 minute attention budget** [[2026-08-10-ai-consultancy]] |
| >300 lines | "all good" [[2026-08-31-ai-software-vendor]] |
| <500 lines | "seven small beat three big" [[2026-10-01-ai-bank]] |
| ≈1,000 lines | still passes, each slice reviewable in ≈45 min [[2026-09-30-ai-enterprise]] |
| ≥1,500–3,000 lines | war, or dead [[2026-08-31-ai-software-vendor]] [[2026-09-30-ai-enterprise]] |

A practical trigger: above ≈20 files (a paginated grid touched 37), or a 75-file PR → **5 PRs of ≈20 files**, each pulled from the previous one [[2026-08-31-ai-software-vendor]].

## How

- **Branch from branch** instead of opening one giant PR; slices of ≈1,000 lines or less [[2026-08-27-ai-it-services]]. Stacked diffs come from Phabricator-era workflows and were brought to GitHub by tools such as [Graphite](https://graphite.com/guides/stacked-diffs) [[2026-10-06-gray-factory-devoxx]].
- **Native on GitHub** since the [30 July 2026 public preview](https://github.blog/changelog/2026-07-30-stacked-pull-requests-are-now-in-public-preview/): the stack shows in the PR UI, with a `gh stack` CLI extension ([docs](https://docs.github.com/en/pull-requests/get-started/about-stacked-prs), "subject to change"). Outside it: Graphite, git-town, or plain chained base branches [[2026-09-04-ai-software-company]].
- **The agent cascades fixes.** A change requested on PR #2 is propagated into every PR stacked above it — the rebasing chore humans hated is now free [[2026-08-31-ai-software-vendor]] [[2026-08-27-ai-it-services]]. Each slice is approved one by one, with fixes merged forward [[2026-08-10-ai-consultancy]].
- **A posteriori** — split an overnight 3,000-line diff into stacked PRs under 1,000 lines [[2026-09-04-ai-software-company]] — or, better, **preemptively**: chop the giant ticket at the origin, before it becomes a spec [[2026-08-31-ai-software-vendor]].
- **Night shift.** Six specs ready at 17:00, a script that runs the agents one after another, and a hook that sleeps when quota drops under 5% → a chain of stacked PRs in the morning. See [[ralph-loop]] [[2026-08-13-ai-insurer]].

## Costs and dissent

- **You lose intermediate commits and conversation history** when the stack is rebuilt [[2026-08-27-ai-it-services]].
- **Same reading, more churn.** One team noted that stacking doesn't reduce the total amount to read; the real fix is slicing the requirement upstream into [[tracer-bullets|vertical slices]] [[2026-09-04-ai-software-company]].
- **"Stacked PRs fake progress"**: one fix at the bottom ripples through the whole stack [[2026-09-28-ai-gaming-tech]]. The counter-argument is that the agent now does that cascade for free — but every re-review of an upper PR still costs human attention.

The agent itself pulls the other way: left alone it prefers **big-bang by layers** (repository → service → controller, four screens at once), because opening each file only once is cheapest in tokens [[2026-09-02-ai-insurer]]. Asking for stacked, vertically sliced PRs is a deliberate trade of tokens for reviewability.

## See also

- [[code-review]]
- [[agentic-review-pipeline]]
- [[tracer-bullets]]
- [[ralph-loop]]
- [[worktree]]
- [[spec-driven-development]]
- [[approval-fatigue]]
- [[gray-factory]]
