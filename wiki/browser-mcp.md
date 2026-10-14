---
title: Browser MCP
category: tool
tags: [mcp, playwright, browser-automation, accessibility-tree, testing, chrome-devtools]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-25-ai-bank-migration]]"]
created: 2026-06-12
updated: 2026-10-13
---

[Playwright MCP](https://github.com/microsoft/playwright-mcp) lets an agent drive a real browser headlessly via [[model-context-protocol]] — and, crucially, it reads the page through the **accessibility tree** rather than through screenshots, which is structurally cheaper and cross-browser.

## <span style="color:red">Three ways to drive a browser</span>

| <span style="color:red">Tool</span> | <span style="color:red">What it drives</span> | <span style="color:red">Pick it for</span> |
|---|---|---|
| <span style="color:red">**Playwright** — [MCP](https://github.com/microsoft/playwright-mcp) or [CLI](https://playwright.dev/agent-cli/introduction)</span> | <span style="color:red">A fresh, isolated browser (Chromium/Firefox/WebKit), headless in Docker/CI</span> | <span style="color:red">Cross-browser, vision-free automation and test generation; the CLI needs no MCP at all (click, scroll, input, screenshot from the terminal)</span> |
| <span style="color:red">**[Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)**</span> | <span style="color:red">Your *live* Chrome: screenshot, click, fill, DOM, console errors, network</span> | <span style="color:red">Debugging, breakpoints, performance tuning, Lighthouse — Chrome-only</span> |
| <span style="color:red">**[Claude in Chrome](https://code.claude.com/docs/en/chrome)** extension</span> | <span style="color:red">Your everyday browser, with your logged-in sessions</span> | <span style="color:red">Internal tools behind SSO, staging environments</span> |

<span style="color:red">Teams usually declare them in a committed `.mcp.json`, and **pin the version** (`chrome-devtools-mcp@x.y.z`, not `@latest` — see [[supply-chain-attack]]). Any web app you can log into programmatically, even with basic-auth headers, becomes automatable: click-flows, test generation, CSS tuning. The flip side: the fight against AI scrapers is lost — an agent gets in through a real browser anyway.</span>

## Accessibility tree vs screenshots

Every browser exposes an **accessibility tree** (built for screen-readers): a structured text description of the page — buttons, inputs, labels, grid rows — without any pixel data. An agent reading the a11y tree gets the page's semantics as tokens, not as a vision-model prompt. <span style="color:red">It reads the web "like a blind man" — Playwright's [ARIA snapshot](https://playwright.dev/docs/aria-snapshots) literally pretends to be blind and asks Chrome what it sees.</span> This makes browser automation:
- **Cheaper** — no vision inference cost.
- **More reliable** — text labels are stable; pixel coordinates shift on resize.
- **Cross-browser and cloud-runnable** — the same test script runs against Chrome, Firefox, and Safari, headless in CI.

Screenshots and vision are the fallback for canvas, games, or visual regression (pixel-diff), not the default. The screenshot-driven loop is still a powerful [[feedback-loop]] when layout is the goal — *"stay in a loop taking screenshots until it's pixel-aligned"* converged a render to <0.5% diff unattended. On mobile, [[adb-automation|`adb`]] is the direct equivalent: the agent screenshots/taps/drags a running emulator.

<span style="color:red">**What isn't in the tree is invisible.** Canvas, maps, WebGL 3D views and embedded PDFs have no accessibility nodes; there the agent needs explicit screenshots. Conversely, **WebFetch can't see SPAs**: Angular/React pages often ship near-empty initial HTML and render only after JavaScript runs — use Playwright for dynamic UIs, WebFetch for static docs.</span>

<span style="color:red">**Security angle.** No pixels means no channel for image-borne [[prompt-injection]]. In one class demo, a "Hi There!" image with near-white "IGNORE PREVIOUS INSTRUCTIONS…" text came straight out of "extract the text from this screenshot"; documented attacks hide faint text that survives a screenshot ([Brave](https://brave.com/blog/unseeable-prompt-injections/)) or reveal a prompt only when the image is downscaled ([Trail of Bits](https://blog.trailofbits.com/2025/08/21/weaponizing-image-scaling-against-production-ai-systems/)). Canvas-rendered sites force OCR on pixels and reopen that door — see [[geo-steganography]].</span>

<span style="color:red">**Locators and diffs.** Prefer locators by accessibility role (`getByRole`, `getByLabel`) over XPath — but they must be unique on the page (two `password` inputs make an ambiguous locator). When the UI changes, have the agent **diff the ARIA snapshots** of the two app versions running side by side instead of eyeballing, and proactively generate the missing locator for every new element.</span>

<span style="color:red">**Don't describe pixels, show them.** For a misaligned button: screenshot → paste → draw an arrow → "move this here" ([[review-by-images]]). For diagrams, skip vision entirely: give the agent the draw.io XML — or even the PNG export, since draw.io embeds the XML in the image metadata.</span>

## The "name the tool" discipline

When telling an agent to drive the browser, name the exact MCP tool: *"iterate on the screenshot via Playwright MCP"*. This prevents the agent derailing to a less-appropriate tool (a raw `curl`, a different screenshot mechanism). <span style="color:red">With three browser tools installed, "sometimes you have to shout which tool to use."</span> See [[agent-permissions]] for the broader principle of constraining tool access by name.

## <span style="color:red">Workflows that pay off</span>

- <span style="color:red">**Explore, then codify.** Let the agent click through like a human first and **generate the script last**: *"explore the app in Chrome, create/search/edit/delete an owner, then write a Playwright suite covering that flow."* Victor's demo project has ≈100 such tests, none written by hand.</span>
- <span style="color:red">**Explore before specifying.** *"Open the app, go to Owners, search for something — figure out what search mechanism exists just by navigating the UI"*: the browser as a manual-QA explorer that learns current behaviour before you write the [[spec-driven-development|spec]] for a change.</span>
- <span style="color:red">**Bug reproduction loop.** Click through until the bug appears → *"now write a Playwright test reproducing what you did"* → *"now fix it until the test passes"* ([[red-green-tdd]]). Left on autopilot during a break: "reproduce bug #40 via Playwright CLI, write a failing e2e `.feature`, fix it". Advanced teams already deliver bug fixes **with a before/after video**.</span>
- <span style="color:red">**Exploratory testing (verbatim, steal it):** *"You can find the application logged in in the browser. Do exploratory testing — click randomly, explore corner cases, report bugs as Jira tickets. Don't give up until you find 10 bugs or 10 dubious behaviours."* One team runs it weekly and gets ≈10 issues per run.</span>
- <span style="color:red">**Design-polish loop** with Chrome DevTools MCP: *"stay in the loop until the design matches the intent in the original picture I gave you"* — screenshot, compare with the reference, edit CSS, repeat. Anthropic's `frontend-design` skill packages the same loop: it asks for context (app, branding, framework), generates code, and screenshots its own result until acceptable. Also useful to edit data through one screen and prove it shows up in another.</span>
- <span style="color:red">**Cross-browser check after every push** to a public site: screenshots in Chrome, Firefox, Safari and mobile, compared automatically. Vision only where visual alignment truly matters.</span>
- <span style="color:red">**Self-regenerating user manual.** A headless agent clicks through the app, screenshots, and writes what it can do — no code access needed — wrapped as a `/regenerate-user-manual` command. It needs a fixed **template**, or every regeneration looks different; rerun periodically and it doubles as a behavioural regression check.</span>
- <span style="color:red">**Capture-to-test.** Record mouse and voice while a user operates the app, send key frames plus transcript to an LLM, get a Playwright test or `.feature` back ([Replay.io](https://www.replay.io/) exposes browser record-and-replay to agents over MCP). The cheap version: record your microphone while clicking through a manual test and hand transcript + test plan to the agent ([[acceptance-test-bdd]]).</span>
- <span style="color:red">Expect an **"allergy to payment gateways"**: the browser agent refuses to click Pay.</span>

## <span style="color:red">Playwright test agents</span>

<span style="color:red">[Playwright Test Agents](https://playwright.dev/docs/test-agents) are just **three free markdown agent files**: **planner** (explores the app through the browser and writes a test plan in markdown), **generator** (turns the plan — or a Jira ticket — into tests), **healer** (semantically remaps locators when the screen changes). Have your agent fetch them and push them into the team [[plugin-marketplace]]; on a Selenium codebase, ask the agent to port them ([[selenium-to-playwright-migration]]). The **trace viewer** (film + network + console per step) is why teams don't go back to Selenium — keep the heavy trace only for tests that fail three times in a row. Note: `playwright test` runs headless by default; Playwright MCP opens a visible browser.</span>

## Lighthouse integration

[Lighthouse](https://developer.chrome.com/docs/lighthouse/overview) lives in Chrome DevTools and gives a perf/a11y audit as structured JSON — another structured oracle the agent can reason over without vision. Wire it into the same MCP session for a combined layout-correction + accessibility pass.

## Chrome extension vs Playwright for login-gated apps

The Chrome browser extension reuses your already-logged-in session; a fresh Playwright launch opens a clean profile with no cookies and hits the login wall. Workarounds (a login script with credentials, persisted `storageState`, attaching to the existing browser) all exist, but the extension is the clean path for internal tools and staging environments. For new pipelines you control (no SSO/OAuth), `storageState` — save an authenticated session once and reuse the file — is the cleanest production answer. The login problem decides it: extension for session-gated internal apps, Playwright for clean automated pipelines.

<span style="color:red">**Your identity comes along.** Binding Playwright to your main browser profile (e.g. Edge on an intranet) means the agent **inherits every logged-in session — including your personal mail**: "the best thing that ever happened to you, and a reason to get fired." That is two legs of the [[lethal-trifecta]] in one click. Safer: a dedicated, persistent test profile (`--user-data-dir`) where you log in once to just the tools the agent needs; for SSO, fully close the browser first or the new profile won't pick up the login. A sandboxed agent reaches nothing internal by default — **punch a hole for the internal wiki** deliberately rather than opening the whole intranet ([[os-sandbox]]).</span>

## <span style="color:red">QA habits</span>

<span style="color:red">**One hour a day without the mouse:** dictate clicks to the agent, or give it the day's manual test plan to automate. **QA in a minute:** read the test plan aloud as if to a new junior, hand the agent the ≈10-minute recording, and it clicks through with Playwright.</span>

## See also

- [[model-context-protocol]]
- [[mcp-sandbox-inheritance]]
- [[dynamic-tool-discovery]]
- [[acceptance-test-bdd]]
- [[agent-permissions]]
- [[adb-automation]]
- [[feedback-loop]]
- [[selenium-to-playwright-migration]]
- <span style="color:red">[[lethal-trifecta]]</span>
- <span style="color:red">[[geo-steganography]]</span>
- <span style="color:red">[[prompt-injection]]</span>
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[review-by-images]]</span>
- <span style="color:red">[[red-green-tdd]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-22-ai-sports-betting]]
- <span style="color:red">[[2026-04-27-ai-healthcare-software]]</span>
- <span style="color:red">[[2026-08-06-ai-bank]]</span>
- <span style="color:red">[[2026-09-10-testing-ai-bank]]</span>
