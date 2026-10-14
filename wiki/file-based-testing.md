---
title: File-Based Test Cases
category: pattern
tags: [testing, test-data, json5, data-transformation, maintenance]
sources: ["[[2026-08-13-ai-insurer]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

For data-transformation logic, each test case is a pair of data files (`<case>.in.json` + `<case>.out.yaml`) run by one generic test, so the test suite is **data the agent can mass-maintain** rather than code.

## How it works

- One parameterized test scans a folder, feeds every `*.in.json` through the transformation, and compares the result with the matching `*.out.yaml`.
- A hundred pairs is normal. Adding a case means adding two files — no new test method.
- Example from Victor's unit-testing repo: [`missing-createdBy.in.json`](https://github.com/victorrentea/unit-testing/blob/master/src/test/resources/test-cases/export/missing-createdBy.in.json).

## Why it suits agents

- **Bulk edits are now cheap.** Renaming a field means the agent rewrites 500 files — prohibitive by hand, a few minutes for an agent. The old objection to data-driven fixtures (maintenance cost) largely disappears.
- **Explain the critical case inside the file.** Use [JSON5](https://json5.org/) so each fixture can carry a comment saying *why* the case exists — context the agent (and the next human) needs to keep it meaningful.
- **A review surface close to the requirements.** Business analysts and QA can read input/output pairs directly, without reading Java — the same goal as [[acceptance-test-bdd]] scenarios, with less ceremony.
- The expected outputs are a clear [[feedback-loop]] for an agent refactoring the transformation: any diff is either a bug or a requirement change to confirm.

## See also
- [[acceptance-test-bdd]]
- [[openapi-test-fixtures]]
- [[data-quality-validator]]
- [[feedback-loop]]
- [[record-replay-refactoring]]
- [[2026-08-13-ai-insurer]]
