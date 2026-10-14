---
title: Code City
category: tool
tags: [visualization, code-review, hotspots, complexity, git-history, architecture]
sources: ["[[2026-07-01-ai-java-community]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Code City renders a codebase as a 3D city — packages are districts, classes are buildings whose footprint, height and colour encode size, complexity and change history — so you can spot hotspots and review structural change at a glance.

## Lineage

The idea is old: Richard Wettel's [CodeCity](https://wettel.github.io/codecity.html) (2007, [VISSOFT paper](https://www.inf.usi.ch/lanza/PUBS/P/Wett2007b.pdf)). What is new is cost and use: Victor vibe-coded his own version in ≈3 h, without an LLM in the generator, and open-sourced it at [victorrentea/code-city](https://github.com/victorrentea/code-city) ([live demo](https://victorrentea.github.io/human-review/demo/assets/codecity/codecity.html)) — and the interesting picture is no longer the city but its **delta across a PR**.

## The encoding

| Visual | Metric |
|---|---|
| District | package |
| Building footprint | lines of code in the file/class |
| Height | complexity — cyclomatic (number of branches) in the current version; some renderings used cognitive complexity |
| Colour ("heat") | commits, bug-fix commits (inferred from the tracker key in commit messages), or number of distinct committers — *"more humans → more mess"* |
| Arrows | coupling in/out, filterable to dependencies that **leave the package**; purple = bidirectional |

Multi-repo: symlink 20 microservices into one folder and they render as neighbourhoods of one city.

## Uses

- **PR surveillance.** Diff the city between `main` and the branch: *delta arrows* show which class grew, got more complex, or gained coupling — *"how much did our apartment grow"*. It is one of the "images first" in [[review-by-images]] and a quick way to size a 10k-line business PR before the [[vibe-fixer]] goes in.
- **Warn the agent where the mines are.** Bug density, size and churn per file mark hotspots; an agent entering a hot zone is told *"sit down and study, there are race conditions here"* ([[2026-06-05-ai-consultancy]]). This is the visual form of the [[bug-heatmap]] formula.
- **Post-vibe-coding triage.** Before devibing a vibe-coded mess, the city points at the "hate-filled" classes to attack first ([[vibe-coding]]).
- **Grilling input and onboarding.** Git history shows how many different brains touched a class — a cue for which [[grill-me|grilling]] questions to ask and whom.
- **Change coupling.** Classes or packages that always change together ([change coupling](https://codescene.io/docs/guides/technical/change-coupling.html), mined from git) should live close — possibly be merged.

## Sizing modules for agents

A heuristic from the room ([[2026-09-30-ai-enterprise]]): a module should fit in a 1M-token context — roughly **≈50k lines of Java**. Between modules, add `@WithSpan` and watch the real calls ([[observation-points]]).

## Honest limits

In practice, for review, **cross-service sequence diagrams beat it** ([[2026-09-28-ai-gaming-tech]]) — Code City shows *where* things grew, not *what flows*. It belongs to the family of visualizations generated straight from code — reflected class diagrams ([[archunit-drift-control]]), [[field-reality-diagram]], [[deterministic-diagrams]] — which cannot drift because they are regenerated on every build.

## See also
- [[bug-heatmap]]
- [[review-by-images]]
- [[archunit-drift-control]]
- [[field-reality-diagram]]
- [[cognitive-debt]]
- [[observation-points]]
- [[code-review]]
- [[vibe-fixer]]
- [[human-review-dashboard]]
