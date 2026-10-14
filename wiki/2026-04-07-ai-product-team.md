---
title: "Source: AI-Assisted Coding @ a product team (2026-04-07 – 2026-04-09)"
category: source
tags: [source, product-team, workshop, three-evenings, remote, claude-code, copilot]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Three remote evening sessions for a product team spread across Latin America, mostly on GitHub Copilot and just handed Claude Code ("they gave you a bazooka"). Day 1 laid the foundations — model vs harness, LLM pitfalls, the code-review pledge, CLAUDE.md as team memory, live browser automation and a crowdsourced prompt for a fuzzy owner search. Day 2 showed why the vibe-coded search was a denial-of-service waiting to happen, then ran spec-driven development twice (Superpowers interactively, OpenSpec non-interactively), the planner/coder/reviewer orchestration patterns, MCP as a product decision, and the CLAUDE.md / slash-command / skill / agent map with a tool-restricted scraper sub-agent. Day 3 covered layered code-quality guardrails, a theatrical prompt-injection and jailbreak session (Gandalf), permission fatigue and sandboxing, C4 diagrams with a caught hallucination, and a testing strategy built on mutation and functional integration tests.

## Concept inventory

*Foundations* — [[harness]] vs [[model-hierarchy]], [[knowledge-cutoff]] and [[context7]], [[hallucination]] ("how did you come to this conclusion?"), [[sycophancy]] counter-prompt, [[ai-spiraling]].

*Discipline & review* — [[vibe-coding]] (the 100-reviews bar, "I shall not push code without reviewing it"), [[code-review]] (three questions per AI-written PR), [[skill-erosion]] (`/teach-me` tutor mode), [[agentic-engineering]] (254-minute run, finish conditions, 30% realistic velocity), [[ai-use-cases-beyond-coding]].

*Knowledge engineering* — [[claude-md]] (prompt-don't-edit game, ultra-compressed rules, shortcut keywords), [[light-dark-code]] (greenfield spike exemplar), [[cross-repo-knowledge]] (company-wide public-interface repo, migrations in the same repo), [[agent-skill]], [[custom-agent]] (four ways to share prompts; HTML-scraper sub-agent), [[codeowners-elders]].

*Spec-driven & orchestration* — [[superpowers]] brainstorming, [[openspec]] (propose → apply → archive into a living spec), [[spec-driven-development]], [[sub-agents]] (planner → executor, writer → reviewer), [[example-driven-prompting]] (few-shot search spec).

*MCP & tools* — [[model-context-protocol]] (expose an MCP instead of embedding an LLM), [[browser-mcp]] (Chrome DevTools vs Playwright, explore-then-codify tests), [[observability-mcp]] (don't grep 100k log lines).

*Guardrails & security* — [[static-analysis-gauntlet]] (Snyk & co., attack your own guardrails), [[enforcement-ladder]], [[prompt-injection]] (attack catalog, email injection, the Mexico tax-authority incident), [[jailbreak-ladder]] (Lakera Gandalf), [[supply-chain-attack]] (slopsquatting, malicious MCPs, 36% of skills flawed), [[agent-permissions]] (approval fatigue), [[docker-sandboxing]], [[production-safety]] (iptables and K8s secret-ref incidents).

*Architecture & testing* — [[c4-model]] ("2nd AI accused 1st AI of hallucinations"), [[archunit-drift-control]], [[mutation-testing]], [[acceptance-test-bdd]] (functional integration tests as fences).

## See also
- [[2026-04-16-ai-safety-critical]]
- [[2026-04-27-ai-healthcare-software]]
- [[2026-05-04-ai-telecom]]
- [[2026-06-26-ai-agentic-how]]
