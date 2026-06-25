---
title: Optimize-Then-Measure Subset
category: pattern
tags: [performance, testing, measurement, subset, parallelism]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
new_in: "Kambi Day 2 (2026-06-25)"
---

<span style="color:red">🆕 New in Kambi Day 2 (2026-06-25)</span>

A disciplined sequence for performance optimization that baselines first, validates on a small subset, and only scales after confirming a real speedup — preventing the classic trap of "optimized and made it slower."

## The sequence

1. **Baseline first** — run the full suite or pipeline and record wall-clock time *before* touching anything, even if it takes 18 minutes. No baseline, no proof of improvement.
2. **Prototype on a subset** — apply the optimization to ≈5% of the work (e.g., 2 tests out of 40). This is fast to iterate on and easy to revert.
3. **Confirm a real speedup** — run the subset *twice* for a reliable number; a single run is noise.
4. **Compute the actual factor** — calculate the speedup ratio and worker count. Only proceed if it clears a meaningful threshold (e.g., >1.5× gain).
5. **Scale to full** — only after the subset confirms a win, apply to the full project and re-baseline.

## Why subset first

Full-scale rollouts surface shared-resource contention (DB connection pools, bound ports, filesystem locks) that is invisible at small scale. A subset run is cheap enough to repeat; a full-scale rollout gone wrong may leave the [[ci-green-loop]] red for hours.

This pattern pairs naturally with [[sub-agents]] running parallel test shards — but parallelism wins must be quantified before committing to the architecture. See [[orchestrator-catches-lying-subagent]] for the verification discipline that prevents a sub-agent from falsely reporting a speedup.

## Common pitfalls

- Skipping the baseline because the suite "obviously takes too long" — you still need a number to compare against.
- Measuring only once and treating it as definitive (run twice minimum).
- Scaling before the subset confirms: contention at full scale can make parallelism *slower* than serial.
- Using the agent's self-reported timing instead of an independent [[feedback-loop]] measurement.

## See also

- [[feedback-loop]]
- [[ci-green-loop]]
- [[sub-agents]]
- [[orchestrator-catches-lying-subagent]]
