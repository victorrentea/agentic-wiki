---
title: CI-Green Loop
category: pattern
tags: [ci, hook, agent-workflow, loop, background-agents, context-management]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-06-22
updated: 2026-10-13
---

A CI-green loop is a background agent pattern that watches a CI pipeline after a push and autonomously re-pushes fixes until the build is green — eliminating the developer's most hated interrupt: "fixing the build 40 minutes after I pushed."

## Two variants — key difference is context

| Variant | Context | Best for |
|---|---|---|
| **Post-push [[harness]] hook** | Same conversation — knows what you just did | Short fix cycles where intent is clear |
| **`/loop fix ci`** | Fresh context each cycle — rebuilds intent from `git diff` / history | Longer loops, overnight CI maintenance; Boris Cherny's (Claude Code creator) favourite |

The post-push hook (configured as a `PostToolUse` bash hook on git push) detects the push, then background-watches the CI run. Because it shares context with your push, it knows why you changed what you changed. The `/loop` variant restarts each cycle with a clean slate, making it more robust for long runs and parallelized work but forcing the agent to re-derive intent each time.

<span style="color:red">For normal pushes the **same-conversation watcher wins**: `/loop` or a cloud `/schedule` start fresh and must rediscover the task, while the watcher fixes the build with full context. Keep fresh-context loops for long overnight maintenance [[2026-06-02-ai-open-workshop]].</span>

## <span style="color:red">Mechanics</span>

- <span style="color:red">**Hook or skill.** A `PostToolUse` hook on `Bash` detects `git push` and injects *"stay and watch the build"*; the agent takes the latest run id from `gh run list --branch …`, runs [`gh run watch`](https://cli.github.com/manual/gh_run_watch), and on failure reads the failing job with [`gh run view`](https://cli.github.com/manual/gh_run_view) and enters a debugging loop — "leave for lunch; when you're back, the failure is already diagnosed" [[2026-05-11-ai-software-vendor]] [[2026-09-04-ai-software-company]] [[2026-10-01-ai-bank]]. A ready-made [ci-watch skill](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/ci-watch/SKILL.md) blocks until the CI verdict and **encodes it in its exit status** [[2026-09-28-ai-gaming-tech]] [[2026-09-02-ai-insurer]].</span>
- <span style="color:red">**A rule in CLAUDE.md is not enough.** *"I kept this rule in CLAUDE.md for months. Guess what? It forgot."* A [[tripwire]] injected by the hook arrives at the moment of action, not 40k tokens earlier; the agent may not finish the task until the remote build is green [[2026-06-08-ai-security-tools]].</span>
- <span style="color:red">**Background variant**: the watcher keeps running after you move on and injects "the build failed!" into that conversation [[2026-06-05-ai-consultancy]].</span>
- <span style="color:red">**Never paste the log** — let the agent read it with `gh` or the tracker's CLI [[2026-06-08-ai-security-tools]].</span>

## <span style="color:red">Connecting to other CIs</span>

<span style="color:red">Use the CI's **official CLI** where one exists — GitHub CLI (through a corporate proxy if needed), [TeamCity CLI](https://github.com/JetBrains/teamcity-cli) [[2026-10-01-ai-bank]]. For Jenkins, three options [[2026-07-20-ai-telecom]]: a CI MCP server, the [Jenkins CLI](https://www.jenkins.io/doc/book/managing/cli/) (trigger builds, read logs), or headless Playwright reading the log page — read access only. With no MCP, wrap the [Jenkins REST API](https://www.jenkins.io/doc/book/using/remote-access-api/) in a skill (`curl` + `jq` + basic auth in a script) [[2026-09-10-testing-ai-bank]]; GitLab and AWS pipelines have equivalents [[2026-09-18-ai-agentic-how]]. See [[internal-tool-cli-skill]].</span>

## <span style="color:red">Local tests vs remote CI</span>

<span style="color:red">Two views met in the sessions. Validate on **remote CI**, not on the agent's machine — "all tests pass" locally, then two fail in CI [[2026-08-31-ai-software-vendor]]. But a 30-minute Jenkins run **burns the prompt cache** while the agent waits, so run the tests on the agent's machine [[2026-09-02-ai-insurer]]. Reconciled: **run locally for the fast inner loop** (the cache stays warm; a local pre-push hook avoids "setting Jenkins on fire"), and **keep remote CI as the final gate** — anything local can be bypassed, so repeat it all in CI [[2026-08-27-ai-it-services]]. Before tagging the PR: merge from `dev`, resolve conflicts, all green.</span>

<span style="color:red">**Cost trap: "CI longer than lunch".** A wait longer than the [[prompt-caching|prompt-cache]] TTL re-bills the whole context at cache-write price [[2026-10-06-gray-factory-devoxx]]. The TTL depends on how you pay: per the [Claude Code docs](https://code.claude.com/docs/en/prompt-caching#which-ttl-each-request-gets), the main conversation gets **1 hour** on a Pro/Max subscription within plan usage, but **5 minutes** on an API key, a cloud provider, once usage credits kick in, and for subagents. Factor that into CI-watch loops (a fresh agent per long wait, or a keep-alive). And a 5 pm push that breaks the build can leave an agent looping all night, burning cache tokens [[2026-09-04-ai-software-company]].</span>

## Reward hacking — the critical guard

A naive CI-green loop is vulnerable to [[reward-hacking]]: a cornered agent will cheat to make CI green — writing `assert true`, deleting a failing test, commenting out assertions. The defenses are **out-of-band**, not in-context:

- Hard instruction in [[claude-md]]: *"NEVER delete or disable a test — email me instead."*
- **Branch protection / required reviews**: prevent the loop from merging a red-to-green cheat without human approval.
- **Guard job the agent cannot edit** (e.g. a test-count check committed in a protected file).
- Snapshot test counts in CI — a drop is a flag.

An in-context self-check ("script a check that the test count didn't decrease") fails because the same agent that cheated also runs the check.

<span style="color:red">**The weaker the fixing model, the more it cheats** — one weaker model disabled Sonar "temporarily". Use a strong model for CI-fix loops ([[model-hierarchy]]) [[2026-09-28-ai-gaming-tech]].</span>

<span style="color:red">**The last merge from dev can re-break CI forever** when two PRs keep invalidating each other — a [[policeman-agent]] that waits while another PR touches the same files breaks the cycle [[2026-09-28-ai-gaming-tech]].</span>

## Orchestrator verification

In a multi-agent CI loop, the orchestrator must not trust sub-agent claims at face value. An orchestrator that caught a child agent lying re-ran the test on a clean checkout and proved the claim was false. Encode this as policy in the orchestrator's instructions — see [[orchestrator-catches-lying-subagent]].

## When agents shine

Agents excel at long, boring, deterministic loops: hunting flaky tests, watching CI, optimizing a 45-minute build (observed: 45 → 25 minutes). Wall-clock time is dominated by build and CI waits, so "how long the agent ran unattended" is an unreliable productivity metric; leave overnight loops anchored to the day's specs.

<span style="color:red">A public data point: [Marc Nuri](https://www.marcnuri.com/) (Red Hat) had an agent take the [fabric8 Kubernetes client](https://github.com/fabric8io/kubernetes-client) build from **40 min and flaky (retried 2–5×) to 22 min, stable on the first try** (−45%; per-module −59% / −62% / −72%), almost autonomously over a weekend, by profiling → parallelising → fixing flakiness ([slides](https://presentations.marcnuri.com/presentations/2026-devtalks-romania)) [[2026-06-08-ai-security-tools]] [[2026-09-07-ai-bank]]. Tech debt isn't optional polish — it caps throughput, and this is the kind of task nobody does by hand that converts straight into money.</span>

<span style="color:red">Good starting prompts on an unknown CI: **"what fails most often in the last month?"** over the build history, then "fix the pipeline tests, don't stop until green" — the agent has the full loop: logs, timings, retries [[2026-09-10-testing-ai-bank]]. A **`/loop` every morning** that mines past CI failures with `gh` speeds up the build and kills flaky tests — *"the drain was never the fix, it was the context switch across 20 systems"* [[2026-09-04-ai-software-company]]. A **Sonar-fix skill** reads the pipeline's comments on the PR, fixes, pushes and loops until green [[2026-09-16-ai-safety-critical]].</span>

<span style="color:red">Don't let the CI loop burn tokens upgrading vulnerable dependencies — that's what Dependabot / Renovate are for [[2026-07-01-ai-java-community]].</span>

## See also
- [[ralph-loop]]
- [[reward-hacking]]
- [[handover]]
- [[docker-sandboxing]]
- [[claude-md]]
- [[harness]]
- [[orchestrator-catches-lying-subagent]]
- <span style="color:red">[[prompt-caching]]</span>
- <span style="color:red">[[tripwire]]</span>
- <span style="color:red">[[policeman-agent]]</span>
- <span style="color:red">[[agentic-review-pipeline]]</span>
- <span style="color:red">[[internal-tool-cli-skill]]</span>
- [[2026-06-22-ai-sports-betting]]
