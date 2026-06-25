---
title: Review by Images
category: pattern
tags: [code-review, diagrams, plantuml, er-diagram, pre-push-hook, visual-review]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
new_in: "Kambi Day 2 (2026-06-25)"
---

<span style="color:red">🆕 New in Kambi Day 2 (2026-06-25)</span>

A review practice that prioritizes visual artifacts (diagrams, ER diagrams, rendered diffs) over raw text diffs, optimizing for the limits of human attention and pattern recognition.

## The principle

Review diagrams first — "what's red?" — before opening the diff. A picture surfaces structural changes (new tables, removed relations, dependency cycles) at a glance; a text diff buries them in noise.

The human brain processes spatial structure faster than linear text. Spending the first 2 minutes of a review on an image is more informative than spending the first 10 minutes on raw line-by-line changes.

## Automation pattern

A pre-push hook can:
1. Parse a SQL schema change or migration file.
2. Regenerate an ER diagram with new/changed entities **highlighted in red**.
3. Commit the rendered image beside the SQL in the PR.

The reviewer's first action is to scan the picture for red, not to read the SQL. This is a concrete application of [[agentic-review-pipeline]]'s "sorted review" step — the human sees the highest-signal view first.

## Extensions

The same principle applies to:
- **Class diagrams** — highlight new/changed classes and their relations in red.
- **Dependency graphs** — surface new circular dependencies or architecture violations (see [[archunit-drift-control]]).
- **Architecture diagrams** — show [[drift]] from the intended design visually before the diff.
- **PR narration video** — an AI-narrated walkthrough of the diff, combining visual and audio explanation. This is particularly useful for large diffs that exceed human attention span.

## Relation to [[field-reality-diagram]]

The field-reality diagram captures the current production state; review-by-images captures the *delta* — what is changing. Both are visual tools for different review moments.

## Pitfalls

- Generating diagrams but not highlighting the delta (new/changed entities) defeats the purpose — the human still has to find the change manually.
- Committing rendered images into source control may bloat the repo; use a dedicated artifacts path or CI artifact storage.
- Over-relying on pictures for logic-level bugs — visuals catch structural issues; [[code-review]] and [[multi-model-review]] catch behavioral ones.

## See also

- [[archunit-drift-control]]
- [[code-review]]
- [[field-reality-diagram]]
- [[drift]]
- [[agentic-review-pipeline]]
