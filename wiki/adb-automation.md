---
title: adb-Driven UI Automation
category: tool
tags: [adb, android, mobile, emulator, feedback-loop, ui-automation, ci]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-23
new_in: 2026-06-23
---

<span style="color:red">🆕 New in Garmin 2026-06-23</span>

`adb` (Android Debug Bridge) lets an agent screenshot, tap, and drag on a running emulator or device — the mobile equivalent of [[browser-mcp|Playwright]], closing the feedback loop on a real app UI.

## The hands-free vision

The end-to-end loop it enables: **read the ticket → reproduce the bug on the emulator → fix the code → re-run until a green test confirms it** — without a human driving the UI. The agent observes the running app the same way a QA engineer would (screenshots, gestures), so it can *see* the problem it's fixing instead of guessing from a stack trace. This is a [[feedback-loop]] for mobile, and it's also how a [[grill-me|grilling]] session can answer its own UI questions — *"if the PO says 'I don't know what JSON is,' the agent browses the running emulator via `adb`."*

## The precondition: a deterministic emulator in CI

The fix-the-bug-hands-free vision needs a **maintained, deterministic emulator image wired into CI** — otherwise the agent spirals on environment setup long before it reaches the bug. The emulator is to mobile what a headless Docker stack is to backend [[acceptance-test-bdd|acceptance tests]]: the reproducible substrate the loop runs against.

## Tool-integration paradise (and its risks)

`adb` sits alongside [[figma-mcp]] (designs in), Jira/issue-tracker MCPs, and CI MCPs as the integrations that turn the agent into a self-driving developer. The standing caution applies to all of them: **mind which checkboxes you tick when you mint the agent's API key** — don't hand it the power to delete a project or take down prod (see [[agent-permissions]]).

## See also
- [[browser-mcp]]
- [[feedback-loop]]
- [[figma-mcp]]
- [[acceptance-test-bdd]]
- [[grill-me]]
- [[agent-permissions]]
- [[2026-06-23-ai-garmin]]
