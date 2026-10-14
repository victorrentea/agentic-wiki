---
title: Mutation Testing
category: pattern
tags: [testing, quality, pit, stryker, coverage, ai-generated-tests]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-10-testing-ai-bank]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Mutation testing deliberately breaks production code — flips an `if`, turns `<=` into `<`, deletes a method call — and re-runs the tests: a **killed** mutant proves some test verifies that behaviour, a **surviving** mutant shows exactly which behaviour no test checks.

## Coverage measures execution, not validation

Coverage counts which lines *ran*; it is cheap and gameable — 87% coverage with zero asserts is easy ([[2026-09-10-testing-ai-bank]]). AI-generated "test slop" stays green at 100% coverage even with the code under test deleted; one anecdote had Opus write *"insert ABC, sort, assert ABC"* ([[2026-08-31-ai-software-vendor]]). Tests generated from the implementation pass "by happiness" and assert what already happens — the fake-coverage trap of [[cognitive-debt]] and a quiet form of [[reward-hacking]]. Mutation score counts *verified behaviour*; it is the only programmatic proof that a test validates rather than merely executes.

Coverage is still useful: the agent can read the Cobertura/JaCoCo XML report directly and "write tests for the gaps", good enough for ≈95% of work. Invest in mutation where coverage feels misleading ([[2026-04-07-ai-product-team]]).

## Why agents bring it back

PIT was famously "unusable by hand" — slow, and its report is a wall of survivors to triage. An agent can run it overnight and work through the report. Uncle Bob's argument ([Software Fundamentals in the Age of AI](https://www.youtube.com/live/zcLPGC-tvgk)): mutation testing returns because *you* no longer write the code — **hand the mutation report to the agent as its target instead of line coverage** ([[2026-09-10-testing-ai-bank]]). Have the tool produce the report, not the agent, or the agent grades its own homework ([[2026-08-13-ai-insurer]]).

## Tools

- [PIT / pitest](https://pitest.org) for the JVM — mutates bytecode; configure target packages in `pom.xml`.
- [Stryker](https://stryker-mutator.io) for JS/TS frontends.

## Cheap manual variant

No tooling needed: change one line of production code yourself (rename a field, add an off-by-one, **surgically delete the sorting**) and run the tests. If they still pass, the AI's tests were lying. *"Doubting Thomas"* — run `mvn test` with your own hands and watch it fail where you mutated ([[2026-05-13-ai-it-services]], [[2026-08-27-ai-it-services]]). A test you have never seen fail is not trusted; see [[red-green-tdd]] and [[acceptance-test-bdd]].

Agent version: *"exercise 7 mutations on this class and prove they are all killed"* — token-intensive but feasible ([[2026-04-07-ai-product-team]]). Victor used it to prove a generated Playwright suite: mutate the production controller → test red; revert → green. The same move verifies a [[selenium-to-playwright-migration]] (break a selector, confirm the test fails).

## Where to aim it

- **Before a big refactor** — 100% coverage is not a shield; Golden Master + characterisation tests + mutation are ([[2026-06-08-ai-security-tools]], [[legacy-migration-stages]], [[record-replay-refactoring]]).
- **Critical domain classes** during a legacy migration — grow boundary-level functional tests to ≈85% coverage, then run PIT on the core ([[2026-08-10-ai-consultancy]]).
- **Data migrations** — mutation-test the migration so any change to its logic blows a test up ([[2026-07-23-ai-software-product]]).
- **After AI-generated fuzz tests** — the report shows which assertions lack teeth ([[2026-04-16-ai-safety-critical]]).
- **Guardrail verification** — mutation runs on hard areas are part of the periodic red-teaming that keeps an [[enforcement-ladder]] honest ([[2026-08-13-ai-insurer]]).

## Cost and triage

The build climbs to hours, so run it **targeted on packages** or overnight/monthly — never on every build ([[2026-09-10-testing-ai-bank]], [[2026-04-07-ai-product-team]]). Exhaustive testing is impossible (pesticide paradox, defect clustering ≈80/20), so point mutation runs at the risky 20%. 100% mutation score is only worth it where an error costs a space mission (the classic anecdote is the Space Shuttle flight software). A hands-on group reported many false positives and survivors in code where it was unclear whether covering it was worth it — budget time for triage ([[2026-06-08-ai-security-tools]]).

Even with a good mutation score, tests the AI writes from the spec still need to be **read by a brain**, including the glue code under them ([[2026-08-31-ai-software-vendor]]).

## See also
- [[acceptance-test-bdd]]
- [[red-green-tdd]]
- [[reward-hacking]]
- [[cognitive-debt]]
- [[legacy-migration-stages]]
- [[selenium-to-playwright-migration]]
- [[semantic-coverage]]
- [[static-analysis-gauntlet]]
- [[feedback-loop]]
