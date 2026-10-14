---
title: Bug Heatmap
category: pattern
tags: [code-quality, metrics, vibe-coding, cleanup, prioritization, cyclomatic-complexity, hotspots, code-review]
sources: ["[[2026-06-26-ai-agentic-how]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-31-ai-software-vendor]]"]
created: 2026-06-26
updated: 2026-10-13
---

A bug heatmap is a composite metric that ranks files by their *expected cleanup priority* by multiplying cyclomatic complexity × fan-in/out × git commit count — surfacing "where the shit is" so a [[productionalize|productionalization]] or [[vibe-coding]] cleanup effort attacks the highest-risk files first.

## The formula

```
heat(file) = cyclomatic_complexity × (fan_in + fan_out) × commit_count
```

- **Cyclomatic complexity** — a proxy for branching density and test difficulty. <span style="color:red">It is 1 + the number of decision points (four `if`s → 5), while four *sequential* `if`s can produce up to 16 execution paths — so it under-counts what a tester faces.</span>
- **Fan-in + fan-out** — how many other modules import this file (fan-in = blast radius) plus how many modules it depends on (fan-out = fragility surface). <span style="color:red">Both directions hurt, for opposite reasons.</span>
- **Commit count** — files touched repeatedly are churn-prone; high churn + high complexity = bug magnet.

Files at the top of this ranking are the ones the agent most likely generated sloppily (fast delta, high interconnection) and where bugs hide with the highest consequence.

### <span style="color:red">Extended signal set</span>

<span style="color:red">For surveilling where AI-generated code went ugly, a richer set of signals ([[2026-08-10-ai-consultancy]]) — pick the ones your tooling gives you cheaply:</span>
- <span style="color:red">**Size**: file length; **number of public methods** (1 is relaxed, 20 is "uh-oh").</span>
- <span style="color:red">**Complexity**: cyclomatic *and* [cognitive complexity](https://www.sonarsource.com/resources/cognitive-complexity/) (which penalises nesting the way a reader feels it).</span>
- <span style="color:red">**Coupling**: incoming vs outgoing, plus *instability* (outgoing / total).</span>
- <span style="color:red">**History**: distinct committers (≈10 people on one area = maximum danger), churn, and **bug-fix commit count** — parse the ticket ID out of commit messages; incident history and old PR comments if you have them.</span>
- <span style="color:red">**Coverage — from requirement-level tests**, not unit tests ([[semantic-coverage]], [[acceptance-test-bdd]]).</span>
- <span style="color:red">**Change cadence across services**: three microservices that always change together should probably deploy together — a heatmap at architecture level.</span>

## When to use it

1. **Post-vibe-coding cleanup pass** — after a rapid AI generation sprint, use the heatmap to sequence the [[productionalize|productionalization]] pass rather than reviewing files alphabetically.
2. **Legacy codebase entry** — before handing a legacy codebase to an agent for [[legacy-migration-stages|migration]], rank the modules so the agent attacks the riskiest first.
3. **Sprint planning** — use the heatmap to frame "refactor the hotspot module" as a sprint item with objective data behind it.
4. <span style="color:red">**Deciding where humans review** — many commits + high complexity + few tests + many dependencies → put [[codeowners-elders|CODEOWNERS]], mandatory human review and extra [[guardrails]] there. *"Not every file deserves the same paranoia"* ([[2026-06-08-ai-security-tools]]). It is the first stop of the "AI-guided tour" in [[assumptions-ledger|guided human review]].</span>
5. <span style="color:red">**Pre-merge PR risk classifier** — flag PRs that touch [hotspots](https://codescene.io/docs/guides/technical/hotspots.html) ("look carefully here"). You choose the signals; one team scores PRs from its CVE history. Two policies then compete: prudent up-front review, or **YOLO + low [MTTR](https://dora.dev/guides/dora-metrics-four-keys/)** — rollback tuned to seconds across the whole pipeline ([[2026-08-31-ai-software-vendor]], [[code-review]]).</span>

## Tooling

Most CI/CD ecosystems can produce the raw inputs:
- `lizard` or `radon` (Python), `pmd` (Java), or SonarQube for cyclomatic complexity.
- Static analysis for fan-in/out (dependency graphs).
- `git log --format='%H' -- <file> | wc -l` for commit count per file.

The composite score can be computed in a short script and piped to a sorted table or a Grafana panel.

## <span style="color:red">Seeing it: Code City</span>

<span style="color:red">The visual companion is a **3D [[code-city]]** ([Wettel's CodeCity](https://wettel.github.io/codecity.html)): each file is a block — footprint = size, height = complexity, colour = churn or test coverage — so the same hotspots jump out at a glance. Victor vibe-coded one in ≈3h ([[2026-07-01-ai-java-community]]). It's good for first-contact orientation in an unknown codebase, nothing more; the ranking table is what drives decisions ([[2026-08-10-ai-consultancy]], [[2026-08-06-ai-bank]]).</span>

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
- <span style="color:red">[[code-city]]</span>
- <span style="color:red">[[code-review]]</span>
- <span style="color:red">[[assumptions-ledger]]</span>
- <span style="color:red">[[codeowners-elders]]</span>
- [[2026-06-26-ai-agentic-how]]
