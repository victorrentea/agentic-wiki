---
title: Test-Case Docs → Automated Suites
category: pattern
tags: [testing, legacy, e2e, webdriverio, playwright, allure, traceability]
sources: ["[[2026-07-06-ai-medical-diagnostics]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Test-case doc automation converts a legacy manual test-case document (Word/Excel) into an automated browser test with a rich report, looping with the agent until every phrase of the document is traced to an assertion — then deletes the document.

## The loop

1. Drop the **simplest** not-yet-automated test-case `.doc` into the project.
2. The agent writes a [WebdriverIO](https://webdriver.io) (or Playwright) test plus an [Allure](https://allurereport.org) report.
3. Review the report in the browser and say what's missing; the agent adjusts.
4. **Spot a red in the report → investigate *why* by hand → tell the agent how to fix the test.**

"That little cycle is the whole job." Adopt it incrementally — one test each morning. It is a textbook [[feedback-loop]]: the report is the signal both you and the agent read.

## Bidirectional traceability

The key trick is a report column mapping **each phrase of the document → the assertion that covers it**, and highlighting in red every word of the document the test never used. The goal is zero red — "every comma in the test case is meant to be there." A custom reporter adds niceties: hover an expected result to see its code, ctrl-click to jump to the line. This is the same idea as [[semantic-coverage]]: coverage of the *requirement*, not of the code.

## Teaching the agent the house style

Legacy test documents have quirks no model knows: "D" means Disabled, repeated N/A rows, implicit preconditions such as pre-creating a record. Capture them in a dedicated **"read this Word document" [[agent-skill|skill]]** instead of repeating them in every prompt.

Keep the tests maintainable from day one: selectors live in [page objects](https://martinfowler.com/bliki/PageObject.html) or widgets, never inline, and each suite gets its own data partition so suites can run in parallel (see [[acceptance-test-bdd]]).

## Boy-scout rule

Stop hand-editing the document. Make every change in the real test, then **delete the doc** — otherwise two sources of truth start to [[drift]].

## New features

For features with no legacy doc, let the agent explore the screen and write the tests itself, fed with the ticket's acceptance criteria (via MCP or CLI), the transcript of the meeting where the feature was discussed, browser control ([[browser-mcp]]), and the requirements tool's specs. When migrating older suites, see [[selenium-to-playwright-migration]].

## See also
- [[acceptance-test-bdd]]
- [[browser-mcp]]
- [[feedback-loop]]
- [[agent-skill]]
- [[semantic-coverage]]
- [[selenium-to-playwright-migration]]
- [[ai-use-cases-beyond-coding]]
- [[legacy-migration-stages]]
- [[2026-07-06-ai-medical-diagnostics]]
