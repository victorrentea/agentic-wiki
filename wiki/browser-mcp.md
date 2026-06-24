---
title: Browser MCP
category: tool
tags: [mcp, playwright, browser-automation, accessibility-tree, testing]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-12
updated: 2026-06-24
---

[Playwright MCP](https://github.com/microsoft/playwright-mcp) lets an agent drive a real browser headlessly via [[model-context-protocol]] — and, crucially, it reads the page through the **accessibility tree** rather than through screenshots, which is structurally cheaper and cross-browser.

## Accessibility tree vs screenshots

Every browser exposes an **accessibility tree** (built for screen-readers): a structured text description of the page — buttons, inputs, labels, grid rows — without any pixel data. An agent reading the a11y tree gets the page's semantics as tokens, not as a vision-model prompt. This makes browser automation:
- **Cheaper** — no vision inference cost.
- **More reliable** — text labels are stable; pixel coordinates shift on resize.
- **Cross-browser and cloud-runnable** — the same test script runs against Chrome, Firefox, and Safari, headless in CI.

Screenshots and vision are the fallback for canvas, games, or visual regression (pixel-diff), not the default. The screenshot-driven loop is still a powerful [[feedback-loop]] when layout is the goal — *"stay in a loop taking screenshots until it's pixel-aligned"* converged a render to <0.5% diff unattended. On mobile, [[adb-automation|`adb`]] is the direct equivalent: the agent screenshots/taps/drags a running emulator.

## The "name the tool" discipline

When telling an agent to drive the browser, name the exact MCP tool: *"iterate on the screenshot via Playwright MCP"*. This prevents the agent derailing to a less-appropriate tool (a raw `curl`, a different screenshot mechanism). See [[agent-permissions]] for the broader principle of constraining tool access by name.

## Lighthouse integration

[Lighthouse](https://developer.chrome.com/docs/lighthouse/overview) lives in Chrome DevTools and gives a perf/a11y audit as structured JSON — another structured oracle the agent can reason over without vision. Wire it into the same MCP session for a combined layout-correction + accessibility pass.

## See also

- [[model-context-protocol]]
- [[mcp-sandbox-inheritance]]
- [[dynamic-tool-discovery]]
- [[acceptance-test-bdd]]
- [[agent-permissions]]
- [[adb-automation]]
- [[feedback-loop]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
