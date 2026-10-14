---
title: Determinism over LLM
category: pattern
tags: [scripts, hooks, codemods, token-economy, skills, reliability]
sources: ["[[2026-05-06-ai-ecommerce]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-03-ai-bank]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-09-07-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Whenever a check or transformation can be expressed deterministically — a script, regex, AST rewrite, formatter or linter — use that tool and reserve the LLM for judgement: it is cheaper, faster, repeatable, and impossible to talk out of.

*"Use intelligence only where you need intelligence."* The rule already appears on [[agent-skill]] (the JaCoCo script) and [[token-economy]]; this page is the hub.

## "The agent writes the program, it doesn't do the work"

The goal of a successful conversation is to leave behind **artifacts that run tomorrow without the agent and without quota**: the skill, the scripts, and a `template.md` of the final result's shape [[2026-07-30-ai-safety-critical]]. Two variants of the move:

- **Burn the discovery into an executable.** An `az` exploration that took ≈20 tool calls became a ≈200-line script answering in ≈3 s — fewer tokens, faster, no hallucination [[2026-07-03-ai-bank]] (see [[authenticated-cli]]).
- **Manual-then-script.** Do one instance with the AI, then have it script the rest — the staged approach of [[legacy-migration-stages]] [[2026-05-06-ai-ecommerce]]. *"Use AI to generate deterministic scripts, not to edit code at scale"* [[2026-04-16-ai-safety-critical]].

Then **compare the generated output with what you would have written by hand — but make the agent do the diff**, ask it what it doesn't understand, and fold the conclusions into the skill: knowledge transfer from your expertise into it [[2026-07-30-ai-safety-critical]]. Inside a skill, the split is *"markdown for creativity, shell script for anything deterministic"*.

## Script it, don't scan it

- **Scanning code**: rather than letting the model eyeball 100k lines, have it write an AST scanner, unit-tested on positive and negative examples — and first check whether Sonar already ships the rule (e.g. S2095 for unclosed resources) [[2026-07-06-ai-medical-diagnostics]].
- **Migrations**: don't mass-rewrite code with AI ("expensive, risky, slow"). For mechanical syntax changes use **codemods** — transformations on the syntax tree, not regex: [Codemod](https://codemod.com) for JS/TS, [[openrewrite]] for Java. Have your agent write the *recipe* for your migration [[2026-06-05-ai-consultancy]].
- **Data conversion in hooks**: a post-tool hook that asked the agent itself to convert a tracker's JSON into [[toon]] burned tokens and was slow and imprecise; the fix was a deterministic converter library [[2026-09-28-ai-gaming-tech]].
- **Strict linters at build time** instead of review comments: TypeScript no-`any`, mypy strict, [Pydantic](https://docs.pydantic.dev/latest/) against "dict-driven development", [Semgrep](https://semgrep.dev/) and [[archunit-drift-control|ArchUnit]] rules [[2026-05-11-ai-software-vendor]]. See [[static-analysis-gauntlet]].

## Objective rules in code, subjective rules in prose

**Script the objective rules, leave the subjective ones in prose.** A [pre-push hook](https://github.com/victorrentea/petclinic/blob/main/.githooks/pre-push) that rejects lines over 120 characters costs milliseconds (a check over the pushed diff). "Methods under 30 lines" must *not* be scripted — too vague, and coding models already lean towards clean code [[2026-09-07-ai-bank]].

The same split decides **auto-fix vs critique** [[2026-05-13-ai-it-services]]: syntactically precise transforms (formatting, import sorting, missing braces) are fixed silently; semantic decisions (naming, design) are *critiqued* back to the agent so it learns and the rule lands in [[claude-md|CLAUDE.md]].

## When to run the deterministic fix

Early advice put formatters (Spotless, Prettier, `dotnet format`) and a silent FQCN → import rewrite in a **post-edit** hook [[2026-05-11-ai-software-vendor]]. A later session retracted that as *"a crass stupidity"*: rewriting a file under the agent's hands invalidates its read cache (the harness checks at write time that the file is as it last read it), causing re-reads, token burn and "someone else is in this folder!" confusion. The current recommendation: **format and lint at commit time**; in post-edit hooks only *warn* — a [[tripwire]] message like "FQCN found, replace with simple names" — never rewrite [[2026-06-05-ai-consultancy]]. See [[agent-lifecycle-hooks]].

## How durable is each lever

Ordered by durability and enforcement strength: **hooks > skills > CLAUDE.md > prompt** [[2026-05-11-ai-software-vendor]]. A deterministic check in a hook or CI gate runs every time; a sentence in a prompt is a suggestion. The full ladder, including why a regex deny is still not a wall, is on [[enforcement-ladder]].

## See also

- [[agent-skill]]
- [[agent-lifecycle-hooks]]
- [[enforcement-ladder]]
- [[tripwire]]
- [[static-analysis-gauntlet]]
- [[openrewrite]]
- [[token-economy]]
- [[rtk]]
- [[internal-tool-cli-skill]]
- [[authenticated-cli]]
- [[legacy-migration-stages]]
