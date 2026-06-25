---
title: Selenium to Playwright Migration
category: pattern
tags: [testing, selenium, playwright, migration, mutation-testing, feature-files, typescript]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-25
updated: 2026-06-25
new_in: "Kambi Day 2 (2026-06-25)"
---

<span style="color:red">🆕 New in Kambi Day 2 (2026-06-25)</span>

A migration strategy for replacing Selenium-Java step definitions with Playwright-TypeScript equivalents while leaving Gherkin `.feature` files untouched as the immutable source of truth.

## The strategy

The key insight: **only rewrite the glue layer** (step definitions), never the [[acceptance-test-bdd|`.feature` files]]. The Gherkin scenarios are the acceptance contract; the implementation layer is replaceable.

### Migration steps

1. **Identify rotted/dead Selenium tests** — tests that no longer run or have been commented out are the lowest-risk starting point.
2. **Port step definitions** from Selenium-Java to Playwright-TypeScript, matching each Gherkin step.
3. **Verify the port with mutation testing**: deliberately break something in the browser (a selector, a field name, a conditional), confirm the old Selenium test fails on that break, then confirm the new Playwright test also fails on the same break. This is the gold standard for migration verification.
4. **Scale** to the remaining tests once the pattern is validated.

## Ecosystem rationale

Playwright is the current ecosystem leader for browser automation: more compact in tokens, broader investment, and better async/await ergonomics than Selenium's Java binding. See [[browser-mcp]] for agent-driven browser interaction.

## Prerequisites

The agent can write Playwright-TypeScript, but **you must understand TypeScript** (async/await, let/const, Promise chains) before using it in production — otherwise you cannot debug the async/sync bugs the agent inevitably introduces. [[legacy-migration-stages]] apply here: understand the target stack before delegating to the agent.

## Reality check

Broken Selenium tests cannot "prove green first" (they're already failing), which removes the standard baseline. Run a small [[optimize-then-measure-subset|subset experiment]] to gauge locator-repair cost before committing to the full migration path. If the locators are heavily rotted, the porting cost may exceed a fresh rewrite from the `.feature` files.

## Pitfalls

- Modifying `.feature` files during migration breaks the contract and invalidates the comparison.
- Trusting the agent's "this is equivalent" self-report without mutation-test verification.
- Skipping TypeScript fundamentals and discovering async bugs only in CI.

## See also

- [[acceptance-test-bdd]]
- [[legacy-migration-stages]]
- [[browser-mcp]]
- [[feedback-loop]]
- [[optimize-then-measure-subset]]
