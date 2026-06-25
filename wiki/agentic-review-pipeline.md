---
title: Agentic Review Pipeline
category: pattern
tags: [code-review, sub-agents, pipeline, proof-of-life, sonar, multi-model-review]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
new_in: "Kambi Day 2 (2026-06-25)"
---

<span style="color:red">🆕 New in Kambi Day 2 (2026-06-25)</span>

A structured sequence of automated and human gates that every AI-generated diff must pass before a human reviewer sees it.

## Overview

The pipeline runs before the human is ever summoned:

1. **Coder agent** writes the code.
2. **Fresh [[multi-model-review|"mother-in-law" reviewers]]** run in parallel on *different* models — never resumed sessions, always fresh context (reuse → Sonnet, simplification → GPT, efficiency → Haiku).
3. **[[static-analysis-gauntlet|Sonar]]** runs at maximum aggression.
4. **Merge attempt first** — pull dev into the branch, run all tests post-merge, before calling the human.
5. **Only then call the human ("the king")** — they review a pre-vetted diff, not raw output.
6. **Sorted review** — the human reads first where agents *disagreed* ("where there was blood"). Human attention degrades sharply after ≈30 min on a 1,500-line diff.
7. **[[policeman-agent|Proof-of-life gate]]** — nothing merges without a human sign-off token.
8. **Verdict persisted** — the review verdict is committed as a `.md` file in the PR, not left in volatile chat.
9. **Upgrade, don't hand-fix** — when the human finds a flaw, upgrade the [[sub-agents|agent's prompt]] and re-run ("upgrade the turret"), rather than patching the output by hand.

## Why it matters

Without the pipeline, AI-generated code flows directly to human review with no pre-screening. Humans become first-line QA for obvious issues, burning attention budget on noise instead of judgment calls.

The sorted review step leverages disagreement as a signal: when two independent models flag the same spot, the human's attention is most valuable there. See [[mob-spec-review]] for the analogous human-review pattern.

## Pitfalls

- Resuming reviewer agents across sessions defeats independence; always start fresh.
- Persisting verdict in chat (not a committed file) means it disappears when the chat is closed. Use [[agents-md-discipline]] to enforce the commit step.
- Skipping the post-merge test run hides integration failures until after human review.

## See also

- [[multi-model-review]]
- [[code-review]]
- [[static-analysis-gauntlet]]
- [[sub-agents]]
- [[mob-spec-review]]
- [[policeman-agent]]
