---
title: "Source: AI-Assisted Coding @ an IT services company (2026-08-27 – 2026-08-28)"
category: source
tags: [source, it-services, workshop, two-day, claude-code]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop at a software services company about how the job changes when you no longer type the code. Day 1 opened with the room's DevEx pains and an 18-month timeline of the rupture, then covered models and providers, CLAUDE.md and an internal plugin marketplace, expertise captured in markdown and skills, the system prompt and token economics, work modes, the dumb zone and prompt-cache behaviour, sub-agents, the Playwright bug-fix prompt, grilling a vague grid ticket against real data, sycophancy, `npm install -g` as code execution, spec-driven development, and a thinking-bandwidth budget of ≈4 hours a day. Day 2 went into multi-agent review (one round only), red-green trust in AI-written tests, acceptance tests the agent can't see, MCP formats and MCP-vs-CLI auth trade-offs, build and ops loops, generated documentation and LSP-grade code navigation, "tower defense" and four-eyes review, the doc-gardener loop, skill erosion and anti-brain-rot habits, then security: adversarial images, jailbreaks, prompt injection and the insider-text threat, the lethal trifecta, the anatomy of a real production-database deletion, the `permissions.deny` myth and four tiers of sandboxing. A verified corrections section settles the prompt-cache TTL history and the dumb-zone threshold.

## Concept inventory

*Context & cost* — [[prompt-caching]] (5-minute API default since launch; 1 h on subscription), [[dumb-zone]] (a percentage, not a number), [[context-window]], [[token-economy]] (output styles instead of "caveman"; real task costs), [[effort-setting]] (Opus-medium beats Sonnet), [[model-hierarchy]], [[sub-agents]] (fork sub-agent), [[claude-code-router]].

*Knowledge engineering* — [[agents-md]] (symlink breaks on Windows), [[memory-layers]] (project doesn't override home), [[agents-md-discipline]], [[agent-skill]] (four skills only; PreToolUse extension), [[goals-over-instructions]], [[plugin-marketplace]], [[doc-gardener]], [[two-layer-memory]] (digital twin).

*Requirements & spec* — [[grill-me]] (business first, code last), [[three-amigos]] (prepare with an agent, cut at 35–40 min), [[spec-driven-development]] (bazooka for weak harnesses; red flags for an oversized spec), [[keyset-pagination]], [[sycophancy]] (rewind with Esc Esc), [[figma-mcp]] (mock-ups now in code).

*Tests & guardrails* — [[acceptance-test-bdd]] (red-green, zero mocks, ATDD), [[mutation-testing]], [[archunit-drift-control]] (generated docs chain), [[ci-green-loop]], [[code-graph]] (MCP over LSP), [[feedback-loop]] (measurable self-verification; JFR on the build).

*Review & workflow* — [[multi-model-review]] (one round; four `/simplify` agendas), [[code-review]] (four eyes beside you; review-rate decay; tower defense verified clean), [[stacked-prs]], [[agentic-engineering]] (micro-waterfall; agent guardian), [[headless-claude]] (Jira2PR is built in), [[vibe-coding]], [[skill-erosion]] (epistemic dopamine).

*Security* — [[prompt-injection]] (insider text in RAG, base64 is no defence), [[geo-steganography]], [[jailbreak-ladder]] (OWASP references), [[lethal-trifecta]], [[slopsquatting]], [[supply-chain-attack]] (`min-release-age`, Maven plugins), [[production-safety]] (PocketOS anatomy), [[agent-permissions]] (the deny myth; auto-mode custom rules), [[docker-sandboxing]] / [[os-sandbox]] / [[secret-zero]], [[dont-run-in-home-folder]], [[data-governance]].

## See also
- [[2026-08-13-ai-insurer]]
- [[2026-08-10-ai-consultancy]]
- [[2026-08-31-ai-software-vendor]]
- [[2026-06-26-ai-agentic-how]]
