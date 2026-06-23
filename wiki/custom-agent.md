---
title: Custom Agent
category: concept
tags: [sub-agents, specialist, model-pinning, restricted-tools, context-window, review]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-23
new_in: 2026-06-23
---

<span style="color:red">🆕 New in Garmin 2026-06-23</span>

A custom agent is a single-purpose specialist (e.g. a security reviewer, a clean-code maniac) defined with its **own context window**, a **pinned model**, and a **restricted tool set** — distinct from the project's ground-rules memory file.

## Custom agent vs `AGENTS.md`

- **`AGENTS.md`** = ground rules injected into *any* agent that touches that folder (see [[agents-md-discipline]]). It shapes every agent equally.
- **A custom agent** = a named role you invoke deliberately. It carries:
  - its **own context window** — it doesn't pollute the orchestrator's context, and isn't polluted by it;
  - a **pinned model** from the [[model-hierarchy]] (`haiku` for trivial, `opus` for long-horizon);
  - **restricted tools** — a security reviewer needs read + grep, not deploy.

## Why it's useful

Custom agents are the named, reusable form of the [[sub-agents]] pattern: instead of spawning an ad-hoc worker, you keep a stable cast of specialists. The restricted tool surface keeps each one at the right altitude (a reviewer that can't write code can't "fix" instead of flag), and the pinned model encodes the cost/quality trade-off so it isn't improvised each run. A review pipeline assembles several of them — one per concern — into a [[multi-model-review]] quorum.

Their boundaries are a [[token-economy]] win (no cross-contamination) and a safety win (least-privilege tools, the same instinct as [[agent-permissions]]).

## See also
- [[sub-agents]]
- [[agents-md-discipline]]
- [[model-hierarchy]]
- [[multi-model-review]]
- [[agent-skill]]
- [[agent-permissions]]
- [[2026-06-23-ai-garmin]]
