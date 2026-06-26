---
title: Static-Analysis Gauntlet
category: pattern
tags: [static-analysis, ci-gate, sonarqube, codeql, semgrep, guardrails]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---

The static-analysis gauntlet is the practice of throwing every static-analysis tool at agent-written code as **build-failing CI gates**, so the code self-repairs against the machines before a human ever reviews it — "make the machine draw blood before a human looks."

## Make the machine draw blood first

It is degrading for a human to say "this method is too long" when a tool can detect it. So you run the full battery until the code self-repairs and reads "like an icon", and *then* a human does [[code-review]] — spending attention on judgement, not on counting parameters. Concretely:

- **[SonarQube](https://www.sonarsource.com/products/sonarqube/)** — strongest on Java, C#, and TS/JS — run with **tuned custom rules**. Lowering its "max parameters" rule from 7 down to **5** makes it scream (major/minor/critical) earlier; tuning the gauntlet to your own taste is the point ("I strangled Sonar").
- **[CodeQL](https://codeql.github.com)** — "query your code as data" — for pipeline checks Sonar doesn't cover.
- **[Semgrep](https://github.com/semgrep/semgrep)** — fast pattern-based scanning, easy custom rules.

A 5,400-line single HTML file from un-guarded [[vibe-coding]] is exactly what the gauntlet must make impossible to merge silently: every weapon fires, the build fails, blood spills, it escalates.

## Gates, not dashboards

The load-bearing detail: wire these as **build-failing CI gates**, not advisory dashboards. A Sonar quality gate, a Semgrep rule, or a CodeQL query that *fails the build* is what forces the agent's self-repair loop; a report the agent can ignore won't. This is the hard end of the same enforcement gradient as a [[tripwire]] (a soft "kind requirement" the agent may ignore) and a [[claude-md]] rule (pure discretion) — the gauntlet *makes* the workflow happen, like [[openspec]]'s backing CLI. It belongs in the same family as the [[guardrails]] stack: layered, automated defence placed around the model's output.

## Custom rules as the agent improves

The gauntlet is not limited to off-the-shelf tools. Custom Refaster rules (for Java), Semgrep patterns, or ArchUnit checks can encode team-specific standards — *"RxJava, die!"* as a build-failing rule. When an agent generates code that uses a banned pattern, the CI wall catches it and the agent fixes it — the human never needs to say it again. Snyk and Fortify extend coverage to dependency vulnerabilities and SAST security findings. The principle: **unleash every tool in CI**, because now there is an agent to do the fixing — tools that were advisory dashboards (because humans couldn't fix everything) become viable build gates.

## See also
- [[code-review]]
- [[multi-model-review]]
- [[guardrails]]
- [[tripwire]]
- [[vibe-coding]]
- [[supply-chain-attack]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
