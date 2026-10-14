---
title: OpenRewrite
category: tool
tags: [refactoring, migration, legacy, ast, codemod, determinism, token-economy, java]
sources: ["[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-06-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-25-ai-bank-migration]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

[OpenRewrite](https://docs.openrewrite.org) is an AST-aware refactoring engine whose *recipes* transform code as a syntax tree, giving IDE-refactoring-grade safety at codebase scale — and, in agentic work, the tool the agent should *write and run* instead of editing thousands of files itself.

## The golden rule: the agent writes the recipe, the recipe makes the change

Rewriting 100k lines by prompt — Spring Boot 2→3, Java 11→21, Hibernate 5→6, an Angular or Next.js major — is a **token bomb**: *"don't burn 50M tokens letting Claude rewrite by hand"* ([[2026-04-27-ai-healthcare-software]]). Direct agent edits on legacy are "the classic mistake": expensive, slow, risky, and each file read pushes the agent deeper into the [[dumb-zone]] — one team faced 10M lines of Java ([[2026-08-06-ai-bank]], [[2026-06-05-ai-consultancy]]). The alternative: *"modify 10,000 lines with PRECISION, not with an agent burning your quota"* ([[2026-04-16-ai-safety-critical]]). A recipe is **deterministic, precise, fast and cheap** — write it once, run it on the whole codebase in waves of commits. This is [[determinism-over-llm]] applied to refactoring.

Data points:
- A custom recipe that once took a week to write by hand was written by Copilot in **≈10 minutes** ([[2026-09-25-ai-bank-migration]]).
- Replacing every `assertEquals` with [AssertJ](https://assertj.github.io/doc/) across a whole project, by running the existing [AssertJ recipe](https://docs.openrewrite.org/recipes/java/testing/assertj) from `rewrite.yml`, cost about **$1** of credits — the agent only *ran* the recipe, spending zero tokens on the change itself ([[2026-09-10-testing-ai-bank]]).
- One recipe deleted 10k lines in one go ([[2026-07-23-ai-software-product]]).

## The migration ladder

1. **Start from a public recipe.** Spring Boot 2→3 ([guide](https://docs.openrewrite.org/running-recipes/popular-recipe-guides/migrate-to-spring-3)), javax→jakarta, EJB→Spring: AST-precise, zero tokens, typically ≈90% of the work ([[2026-05-04-ai-telecom]]).
2. **No recipe? Have the AI write one** — plus exhaustive tests mined from running it on real code. Moderne's CLI ships agent tooling for recipe authoring (`mod config agent-tools`, as demonstrated in [[2026-04-16-ai-safety-critical]]; watch [Moderne](https://github.com/moderneinc)). For one-off textual edits, an AI-written Python/AWK script that parses and transforms is the lighter variant.
3. **Test each recipe on a sample, expand only when green** ([[2026-05-06-ai-ecommerce]]).
4. **Stubborn leftovers** (≈1,000 compile errors after a migration): one [[sub-agents|sub-agent]] per 10–50 issues, or per module, so none loses focus.
5. **The last ≈1–10%** — the truly bespoke code — the AI finishes one by one.

See [[legacy-migration-stages]] for where this fits in a full modernisation.

## Concrete → abstract: from one example to a recipe

The killer combo ([[2026-04-27-ai-healthcare-software]], [[2026-07-23-ai-software-product]]):
1. Refactor **one** representative class by hand, *with* a senior model, for as long as it takes.
2. Ask: *"look at the current version and the whole conversation — write an OpenRewrite recipe that applies the same change everywhere, plus 300 corner-case tests."*
3. Run it one class at a time, eyeball the first ≈50 results, adjust, then unleash.

Give before/after examples (e.g. "inline variables used only once"); the agent writes a visitor on the Java AST ([docs](https://docs.openrewrite.org/authoring-recipes/writing-a-java-refactoring-recipe)) — think of `2 + 3 × 4` as a tree — with a unit test per recipe ([[2026-08-06-ai-bank]], [[2026-09-25-ai-bank-migration]]). **Iterate on counterexamples first**: a variable used twice must *not* be inlined. PetClinic has a worked example: [InlineSingleUseLocalVariable](https://github.com/victorrentea/petclinic/blob/main/refactoring-tools/src/main/java/victor/training/petclinic/rewrite/InlineSingleUseLocalVariable.java) and its [test](https://github.com/victorrentea/petclinic/blob/main/refactoring-tools/src/test/java/victor/training/petclinic/rewrite/InlineSingleUseLocalVariableTest.java). This is [[example-driven-prompting]] with a deterministic output.

## Running it safely

- Order: test coverage → small victim → verify → scale ([[2026-08-06-ai-bank]]).
- Run `rewrite:dryRun` with the [Maven plugin](https://docs.openrewrite.org/reference/rewrite-maven-plugin) and review the diff before applying ([[2026-09-25-ai-bank-migration]]).
- Delegate the run to a sub-agent with explicit "may not" rules ([[2026-08-06-ai-bank]]).
- For "move, don't rewrite" refactors done by the agent, insist on byte-for-byte moves — the manual cousin of a recipe ([[2026-07-06-ai-medical-diagnostics]]; see [[record-replay-refactoring]]).

## Alternatives in the same family

- **[Refaster](https://errorprone.info/docs/refaster)** — a lighter before/after template construct from Google's Error Prone; Picnic's error-prone-support ships a large ruleset. OpenRewrite can also run [Refaster templates](https://docs.openrewrite.org/authoring-recipes/refaster-recipes) ([[2026-07-23-ai-software-product]]).
- **Codemods for JS/TS** — [Codemod](https://codemod.com), where framework vendors publish their migrations; same idea: transform the syntax tree, never regex ([[2026-06-05-ai-consultancy]]).

The same move — babysit once, then script — is how ≈12 of 15 style rules leave AGENTS.md for tooling ([[agents-md-discipline]], [[tripwire]], [[static-analysis-gauntlet]]) and how a repeated agent task becomes an [[agent-skill]] with a script.

## See also
- [[determinism-over-llm]]
- [[legacy-migration-stages]]
- [[example-driven-prompting]]
- [[token-economy]]
- [[sub-agents]]
- [[static-analysis-gauntlet]]
- [[agent-skill]]
- [[dumb-zone]]
- [[record-replay-refactoring]]
