---
title: "Source: AI-Assisted Coding @ a safety-critical company (2026-07-30 → 2026-07-31)"
category: source
tags: [source, safety-critical, workshop, two-day, romanian, copilot-cli]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop (taught in Romanian) for a safety-critical communications company, mostly on GitHub Copilot CLI. Day 1 was about how you drive the agent: choosing models by phase (Opus with a 1M window for wide legacy sweeps), what actually ships with a bare "hi", the dumb zone and `/clear` discipline, YOLO/autopilot and approval fatigue (a `git push --force` hidden at line 55), AGENTS.md and skills, grilling a two-line ticket, and tooling the agent up with DB, Excel and browser access so it gathers its own context. Day 2 was about keeping slop away: review in a fresh conversation by another model family, the "paradise rule" (fix the markdown, not just the code), programmatic guardrails on three layers with CODEOWNERS closing the diagram loophole, breakout rooms wrapping internal tools that have no API as CLI + skill, and a security block on injection paths, slopsquatting, Shai-Hulud and insider exfiltration.

## Concept inventory

*Foundations & economics* — [[model-hierarchy]] (models by phase, weaker model ⇒ tighter spec), [[effort-setting]], [[harness]] (IDE-plugin faults, Copilot ≈3× markup), [[context-window]] (1M for legacy sweeps), [[dumb-zone]], [[handover]], [[token-economy]] (video ≈1M tokens/min, Romanian vs English), [[prompt-caching]] (400k beep hook), [[sub-agents]], [[knowledge-cutoff]], [[wispr-flow]].

*Autonomy & permissions* — [[agent-permissions]] (autopilot vs allow-all, rogue employee + agent), [[approval-fatigue]], [[docker-sandboxing]], [[dont-run-in-home-folder]] (Downloads).

*Knowledge engineering* — [[agents-md-discipline]] (red-Ferrari demo, explain WHY), [[agents-md]], [[agent-skill]], [[progressive-disclosure]], [[cross-repo-knowledge]], [[plugin-marketplace]], [[skills-governance]], [[vibe-heresy]].

*Requirements* — [[grill-me]] (pushback over questions, grill with real sources), [[keyset-pagination]], [[spec-driven-development]], [[plan-mode]].

*Tools & feedback loops* — [[chop-ai-waiter]] (DB access ladder, ghost-text hygiene), [[browser-mcp]] (Playwright CLI, video bug fixes), [[feedback-loop]] (a green build is not proof, runnable-locally apps, regeneration as judge), [[determinism-over-llm]], [[internal-tool-cli-skill]], [[authenticated-cli]], [[ai-use-cases-beyond-coding]].

*Review & guardrails* — [[multi-model-review]] (advisor model, start with built-ins), [[advisor-vs-executor]], [[code-review]] (paradise rule, line-15 brain death), [[static-analysis-gauntlet]] (three layers), [[archunit-drift-control]] (diagram loophole + CODEOWNERS), [[codeowners-elders]], [[field-reality-diagram]], [[c4-model]], [[code-city]], [[acceptance-test-bdd]], [[light-dark-code]] (design-system skill), [[vibe-fixer]].

*Security* — [[data-governance]] (no personal licenses, camera/QR exfiltration), [[prompt-injection]] (four paths, adversarial images), [[tool-poisoning]], [[rag]], [[supply-chain-attack]], [[slopsquatting]], [[skill-marketplace-security]], [[geo-steganography]], [[lethal-trifecta]].

## See also
- [[2026-04-16-ai-safety-critical]]
- [[2026-07-09-ai-live-casino]]
- [[2026-07-23-ai-software-product]]
