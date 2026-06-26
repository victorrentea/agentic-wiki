---
title: Bug Heatmap
category: pattern
tags: [code-quality, metrics, vibe-coding, cleanup, prioritization, cyclomatic-complexity]
sources: ["[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-26
updated: 2026-06-26
new_in: 2026-06-26
---

<span style="color:red">🆕 New in AI@agentic.how (2026-06-26)</span>

A bug heatmap is a composite metric that ranks files by their *expected cleanup priority* by multiplying cyclomatic complexity × fan-in/out × git commit count — surfacing "where the shit is" so a [[productionalize|productionalization]] or [[vibe-coding]] cleanup effort attacks the highest-risk files first.

## The formula

```
heat(file) = cyclomatic_complexity × (fan_in + fan_out) × commit_count
```

- **Cyclomatic complexity** — a proxy for branching density and test difficulty.
- **Fan-in + fan-out** — how many other modules import this file (fan-in = blast radius) plus how many modules it depends on (fan-out = fragility surface).
- **Commit count** — files touched repeatedly are churn-prone; high churn + high complexity = bug magnet.

Files at the top of this ranking are the ones the agent most likely generated sloppily (fast delta, high interconnection) and where bugs hide with the highest consequence.

## When to use it

1. **Post-vibe-coding cleanup pass** — after a rapid AI generation sprint, use the heatmap to sequence the [[productionalize|productionalization]] pass rather than reviewing files alphabetically.
2. **Legacy codebase entry** — before handing a legacy codebase to an agent for [[legacy-migration-stages|migration]], rank the modules so the agent attacks the riskiest first.
3. **Sprint planning** — use the heatmap to frame "refactor the hotspot module" as a sprint item with objective data behind it.

## Tooling

Most CI/CD ecosystems can produce the raw inputs:
- `lizard` or `radon` (Python), `pmd` (Java), or SonarQube for cyclomatic complexity.
- Static analysis for fan-in/out (dependency graphs).
- `git log --format='%H' -- <file> | wc -l` for commit count per file.

The composite score can be computed in a short script and piped to a sorted table or a Grafana panel.

## Relation to [[archunit-drift-control]]

The bug heatmap is a *diagnostic* tool (where to look); [[archunit-drift-control]] is an *enforcement* tool (what to keep clean). Run the heatmap to find the hotspot; apply ArchUnit rules and a reflected class diagram to prevent it from re-accumulating drift after cleanup.

## See also
- [[productionalize]]
- [[vibe-coding]]
- [[cognitive-debt]]
- [[legacy-migration-stages]]
- [[archunit-drift-control]]
- [[static-analysis-gauntlet]]
- [[field-reality-diagram]]
- [[2026-06-26-ai-agentic-how]]
