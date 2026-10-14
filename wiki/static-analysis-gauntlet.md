---
title: Static-Analysis Gauntlet
category: pattern
tags: [static-analysis, ci-gate, sonarqube, codeql, semgrep, guardrails]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-06-08-ai-security-tools]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-30-ai-safety-critical]]"]
created: 2026-06-11
updated: 2026-10-13
---

The static-analysis gauntlet is the practice of throwing every static-analysis tool at agent-written code as **build-failing CI gates**, so the code self-repairs against the machines before a human ever reviews it — "make the machine draw blood before a human looks."

## Make the machine draw blood first

It is degrading for a human to say "this method is too long" when a tool can detect it. So you run the full battery until the code self-repairs and reads "like an icon", and *then* a human does [[code-review]] — spending attention on judgement, not on counting parameters. <span style="color:red">Put bluntly: **you may not see the code until it's impeccable** — green tests, a happy Sonar, quiet security scanners ([[2026-07-09-ai-live-casino]]).</span> Concretely:

- **[SonarQube](https://www.sonarsource.com/products/sonarqube/)** — strongest on Java, C#, and TS/JS — run with **tuned custom rules**. Lowering its "max parameters" rule from 7 down to **5** makes it scream (major/minor/critical) earlier; tuning the gauntlet to your own taste is the point ("I strangled Sonar"). <span style="color:red">The free local Sonar is enough to start ([[2026-07-01-ai-java-community]]).</span>
- **[CodeQL](https://codeql.github.com)** — "query your code as data" — for pipeline checks Sonar doesn't cover.
- **[Semgrep](https://github.com/semgrep/semgrep)** — fast pattern-based scanning, easy custom rules.
- <span style="color:red">**Linters and style checkers** — PMD, Checkstyle, Ruff for Python ([[2026-04-07-ai-product-team]], [[2026-07-01-ai-java-community]]).</span>
- <span style="color:red">**Vulnerability / dependency scanners** — Snyk (Victor's pick), Checkmarx, BlackDuck, Veracode, Fortify, OWASP Dependency-Check, plus an SBOM over `pom.xml`. Most have free tiers — *"Hey Claude, configure that"* ([[2026-04-07-ai-product-team]]). See [[supply-chain-attack]].</span>

A 5,400-line single HTML file from un-guarded [[vibe-coding]] is exactly what the gauntlet must make impossible to merge silently: every weapon fires, the build fails, blood spills, it escalates.

<span style="color:red">**Garbage in, garbage out — AI code needs the gates too.** An early Copilot agent mode, pointed at 1990s-style code, generated SQL injection (`"SELECT … WHERE name like " + request.attribute["q"]`) "because that's how code was written in the era the code looked like." The model reproduces the patterns — vulnerable ones included — it smells in the repo, so the same build-failing gates must run on AI-generated PRs ([[2026-06-08-ai-security-tools]]).</span>

## Gates, not dashboards

The load-bearing detail: wire these as **build-failing CI gates**, not advisory dashboards. A Sonar quality gate, a Semgrep rule, or a CodeQL query that *fails the build* is what forces the agent's self-repair loop; a report the agent can ignore won't. This is the hard end of the same enforcement gradient as a [[tripwire]] (a soft "kind requirement" the agent may ignore) and a [[claude-md]] rule (pure discretion) — the gauntlet *makes* the workflow happen, like [[openspec]]'s backing CLI. It belongs in the same family as the [[guardrails]] stack: layered, automated defence placed around the model's output. <span style="color:red">*"A guardrail is a mechanism that blocks, not a plea in AGENTS.md"*: "always update the diagram when you change code" gets forgotten or hallucinated; a script doesn't ([[2026-07-30-ai-safety-critical]]; see [[enforcement-ladder]]).</span>

## Three layers, split by speed

<span style="color:red">Run the same checks on three layers, because each has a loophole ([[2026-07-30-ai-safety-critical]], [[2026-07-09-ai-live-casino]]):</span>

| <span style="color:red">Layer</span> | <span style="color:red">What runs</span> |
|---|---|
| <span style="color:red">`pre-commit`</span> | <span style="color:red">the fast ones: formatting, linter, unused imports, whitespace, **secrets in the diff** (see [[gitleaks]])</span> |
| <span style="color:red">`pre-push`</span> | <span style="color:red">tests, slow generators</span> |
| <span style="color:red">CI on push</span> | <span style="color:red">everything again + SonarQube + BlackDuck/Fortify/Snyk — the slow ones (20 min to 12 h)</span> |

<span style="color:red">Local hooks are bypassed with `--no-verify` or an unticked IDE checkbox, so **CI is the real safety net**. Still, the local layer earns its place as a feedback loop: in a live demo, a pre-commit hook ran the linter, the agent couldn't commit, read the error and fixed it — *"a strict feedback loop in the face of the chaotic agent"* ([[2026-04-07-ai-product-team]]; see [[feedback-loop]]).</span>

## Custom rules as the agent improves

The gauntlet is not limited to off-the-shelf tools. Custom Refaster rules (for Java), Semgrep patterns, or ArchUnit checks can encode team-specific standards — *"RxJava, die!"* as a build-failing rule. When an agent generates code that uses a banned pattern, the CI wall catches it and the agent fixes it — the human never needs to say it again. Snyk and Fortify extend coverage to dependency vulnerabilities and SAST security findings. The principle: **unleash every tool in CI**, because now there is an agent to do the fixing — tools that were advisory dashboards (because humans couldn't fix everything) become viable build gates. <span style="color:red">Harden rules past human tolerance — the agent does the fixing — accepting that it can occasionally "ripple back against you" ([[2026-07-01-ai-java-community]]).</span>

<span style="color:red">**Every recurring bug becomes a pipeline check, not an eyeball hunt** ([[2026-07-06-ai-medical-diagnostics]]):</span>

1. <span style="color:red">The agent greps *now* to inventory the occurrences; a sub-agent then builds the permanent rule (ArchUnit / PMD / Checkstyle) — "I want this as a test on my pipeline." See [[archunit-drift-control]].</span>
2. <span style="color:red">**Check whether the tool already ships the rule** before hand-rolling one: a JDBC resource-leak hunt turned out to be exactly [Sonar S2095 "Resources should be closed"](https://rules.sonarsource.com/java/RSPEC-2095/).</span>
3. <span style="color:red">**Script it, don't scan:** for a subtle semantic bug class, have the agent write a program that parses the code into an AST ([JavaParser](https://github.com/javaparser/javaparser)) and finds every offending construct, instead of letting the model eyeball 100k lines and hallucinate. **Unit-test the scanner on positive and negative examples**, then run it through a sub-agent. Same move as [[openrewrite]] recipes and [[determinism-over-llm]].</span>

## Attack your own guardrails

<span style="color:red">Keep a repo of real exploited vulnerabilities (Victor's `secure-coding` repo) and try to push intentionally bad code through your gates — if it passes, find a better tool ([[2026-04-07-ai-product-team]], [[2026-04-16-ai-safety-critical]]). The same idea for tests is [[mutation-testing]].</span>

## See also
- [[code-review]]
- [[multi-model-review]]
- [[guardrails]]
- [[tripwire]]
- [[vibe-coding]]
- [[supply-chain-attack]]
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[gitleaks]]</span>
- <span style="color:red">[[archunit-drift-control]]</span>
- <span style="color:red">[[openrewrite]]</span>
- <span style="color:red">[[determinism-over-llm]]</span>
- <span style="color:red">[[mutation-testing]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-04-07-ai-product-team]]</span>
- <span style="color:red">[[2026-06-08-ai-security-tools]]</span>
- <span style="color:red">[[2026-07-06-ai-medical-diagnostics]]</span>
- <span style="color:red">[[2026-07-30-ai-safety-critical]]</span>
