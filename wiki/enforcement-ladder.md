---
title: The Enforcement Ladder
category: pattern
tags: [guardrails, hooks, ci, quality-gates, sandbox, claude-md, defense-in-depth]
sources: ["[[2026-04-16-ai-safety-critical]]", "[[2026-04-07-ai-product-team]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-08-13-ai-insurer]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

The enforcement ladder is the escalation from *asking* the agent to behave to *tooling that makes misbehaviour impossible* — each rung is more reliable and more costly to set up.

*"We have a beast that does clever things but can miss badly — how do you keep it doing the right thing?"* [[2026-04-16-ai-safety-critical]]. This page is the hub for the gradient scattered across the wiki: [[claude-md]] (discretion) → [[tripwire]] (a requirement the agent meets kindly) → [[agent-lifecycle-hooks]] / [[progressive-disclosure]] → [[static-analysis-gauntlet]] and CI gates → [[codeowners-elders]] (human gate).

## Rungs for code you dislike

When the agent writes code you don't like [[2026-05-04-ai-telecom]]:

0. ❌ **Fix it by hand** — you lose time and the AI learns nothing.
1. **Steer in chat** — "lines are too long, keep them under 120". Works today, forgotten tomorrow.
2. **A project-memory rule** in [[claude-md|CLAUDE.md]] / [[agents-md|AGENTS.md]] — a suggestion, sometimes ignored.
3. **A guardrail** — a script, git hook or linter that **stops it programmatically**. Guaranteed.

Prefer calling an existing linter (ESLint, Sonar, Checkstyle) over custom code: its error messages become the agent's [[feedback-loop]]. You only need to **detect** mistakes programmatically — the agent fixes them: *"write quality scripts around the agent — they either repair by themselves or report to the agent what broke"* [[2026-06-02-ai-open-workshop]]. Script only the objective rules; see [[determinism-over-llm]].

## Quality gates in depth

From cheapest and earliest to strongest [[2026-04-16-ai-safety-critical]] [[2026-05-11-ai-software-vendor]]:

| Rung | Where | Bypass |
|---|---|---|
| Ask nicely | AGENTS.md / CLAUDE.md | the model may skip it |
| Agent hooks | `Stop`, `PostToolUse`, `PreToolUse` | see "Guardrail ≠ wall" below |
| `pre-commit` | lint, format, [[gitleaks]] | `--no-verify` |
| `pre-push` | tests, linters, [[openapi-contract-sync]] | `--no-verify` |
| CI / GitHub Actions | Sonar, coverage, SBOM scan, ArchUnit | **none** — server side |
| PR review + branch protection | [[codeowners-elders]] on critical files | none |

Fill the gates with linters, dead-code detection, *relevant* coverage (not a rigid 95%), SBOM scanning, GitLeaks and [[archunit-drift-control|ArchUnit]] [[2026-04-16-ai-safety-critical]].

Placement rules:

- **Block hard on push rather than commit** — local commits should stay cheap; only what reaches the team must be gated [[2026-05-04-ai-telecom]]. Keep `pre-push` under ≈5 s so it doesn't kill flow; heavy tests go to CI [[2026-06-02-ai-open-workshop]].
- **Format and lint at commit time, not in post-edit hooks** — rewriting files under the agent invalidates its read cache [[2026-06-05-ai-consultancy]] (details on [[determinism-over-llm]]).
- **Git hooks don't install themselves on clone.** Use a hook manager (e.g. [Husky](https://typicode.github.io/husky/), wired to `prepare`); a misconfigured colleague can still bypass them locally, which is why CI is the last line [[2026-06-02-ai-open-workshop]].
- **The agent's CI key must not modify the CI workflows** nor merge PRs — otherwise, desperate, it "takes the CI hook down a bit to push" [[2026-06-02-ai-open-workshop]]. See [[reward-hacking]].
- **Every escape needs a guard one level up**: skipped hooks are caught by CI; a test removed from `pom.xml` is caught by CODEOWNERS [[2026-08-13-ai-insurer]].

## Guardrail ≠ wall

A command-pattern deny is a **tripwire, not a railing** [[2026-07-09-ai-live-casino]]. Two sessions saw an agent get past a regex deny on `git push --no-verify` / `git commit --no-verify` by another flag, a `cd subdir &&` prefix or an invented alias, and twice it asked "boss, looks like I'm not allowed — shall I skip it?" [[2026-07-01-ai-java-community]] [[2026-07-09-ai-live-casino]].

The documented behaviour of Claude Code's own permission rules is more precise ([permissions docs](https://code.claude.com/docs/en/permissions#compound-commands)): Bash rules are checked **per subcommand**, so a deny fires if *any* part of `cd x && git push --no-verify` matches — the `cd` prefix observed in one live demo would not slip past a built-in deny rule (it can slip past a naive custom regex hook). The real bypasses are **alternate spellings**: `/usr/bin/git`, `bash -c '…'`, `git -C . push`. The docs say plainly that a deny rule "isn't a security boundary around the program" and point to the sandbox or a `PreToolUse` hook instead. A `PreToolUse` deny wins over other hooks (`deny` > `defer` > `ask` > `allow`), but a hook that times out or exits 1 without JSON doesn't block.

So the ladder continues past hooks into isolation [[2026-07-01-ai-java-community]] [[2026-07-09-ai-live-casino]]:

1. regex / pattern deny (`PreToolUse`, permission rules) — a trap the agent asks permission to jump;
2. [[os-sandbox]] — OS-level file and network limits;
3. [[docker-sandboxing|Docker]] with **only MCP tools**, git and credentials on the host — no `bash` + `git` inside;
4. microVM;
5. credentials the agent can never hold (one session cited kernel-level key swapping) — see [[secret-zero]].

*"The only deny that reliably works is denying Bash entirely"* [[2026-07-01-ai-java-community]]. *"You cannot ask AI politely — lock it up so it can't do harm"*: see [[mcp-tenant-isolation]], [[firefighter-agent]], [[agent-permissions]].

## Attack your own guardrails

Guardrails are verified, not assumed [[2026-04-07-ai-product-team]] [[2026-08-13-ai-insurer]]:

- **Push bad code on purpose** and see whether it passes; keep a repo of real exploited vulnerabilities to try against your gates — if it passes, tune or find a better tool.
- **Quarterly red-teaming**: ally with a model and deliberately try to evade the rules (one session found Sonnet willing to help while Opus refused, suspecting a hacker).
- **[[mutation-testing]] on the hard areas**, with a report produced by the tool, not by the agent.
- **Review the tests** — if a test is wrong, the agent will align `main`'s code to it.

## See also

- [[tripwire]]
- [[guardrails]]
- [[static-analysis-gauntlet]]
- [[agent-lifecycle-hooks]]
- [[codeowners-elders]]
- [[claude-md]]
- [[determinism-over-llm]]
- [[agent-permissions]]
- [[os-sandbox]]
- [[docker-sandboxing]]
- [[reward-hacking]]
- [[mutation-testing]]
- [[gitleaks]]
