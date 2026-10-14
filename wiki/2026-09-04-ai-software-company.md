---
title: "Source: AI-Assisted Coding @ a software company (2026-09-04, 2026-09-09)"
category: source
tags: [source, software-company, workshop, two-day, onsite]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop (in English) with ≈25–27 mostly backend engineers at a software company's India site. Day 1 built the agent itself — when vibing is acceptable, CLAUDE.md/AGENTS.md hygiene (no symlinks: `@AGENTS.md`), skills vs path rules vs plugins, the system prompt as the bill, prompt-cache and fork economics checked against the docs, hooks and CI-watch, CLI over MCP, and a grilled pagination plan where Opus was wrong until it saw the data. Day 2 built everything around it — spec frameworks and their four audiences, design markdown as the place slop hides, adversarial review that gets the coder's inputs, script-generated review artifacts and stacked PRs, review economics, pain points worth handing to agents (bloated Playwright suites, manual testing, flaky CI, leaks), non-human identities for agents, microVM sandboxes with bogus keys, and the injection zoo (poisoned MCP descriptions, logs, RAG, protestware, slopsquatting, markdown-image exfiltration). This is the most docs-verified summary of the course, and it settles several numbers (skill-description limits, cache TTLs, fork vs fresh sub-agent cost).

## Concept inventory

*Context & cost* — [[context-window]] (Sept 2026 price table, "the system prompt is the bill"), [[token-economy]], [[prompt-caching]] (prefix-by-prefix cache, `/compact` costs most on a cold cache, 60× subscription), [[sub-agents]] (fork vs fresh child, Explore inherits the main model), [[effort-setting]] (never `max`), [[model-hierarchy]], [[handover]], [[dumb-zone]], [[knowledge-cutoff]] (force research with quoted links).

*Knowledge engineering* — [[claude-md]] (200-line docs ceiling, `/doctor`, marker names, reader profile), [[agents-md]] (no symlink), [[cross-repo-knowledge]] / [[progressive-disclosure]] (`@import` is eager), [[memory-layers]] (official four scopes), [[agent-skill]] (1,536-char limit, `/skill-doctor`, skill bloat, don't micromanage), [[skill-marketplace-security]] (NVIDIA SkillSpector), [[plugin-marketplace]], [[agent-lifecycle-hooks]] (`Skill` matcher, `UserPromptSubmit`, `asyncRewake`), [[tripwire]], [[ci-green-loop]].

*Integration* — [[cli-vs-mcp-tradeoff]] (tool-messup detour memorized, never repeat a web flow), [[elicitation]], [[sampling]], [[browser-mcp]] (ARIA snapshots), [[chatops-agent]], [[observability-mcp]], [[field-reality-diagram]] (OTel traces committed to git), [[llm-wiki]].

*Requirements & specs* — [[grill-me]] (verbatim prompt, frontier-per-round revision, pacing), [[plan-mode]], [[spec-driven-development]] (BMAD, Spec Kit, legacy "minimal, focal change"), [[openspec]], [[superpowers]], [[decision-context]] (one-line ADR rows), [[keyset-pagination]].

*Review & testing* — [[code-review]] (reviewer gets the coder's inputs, 45-minute cap, never Haiku), [[agentic-review-pipeline]] (one growing conversation is cheapest), [[human-review-dashboard]], [[stacked-prs]] (native on GitHub), [[gray-factory]], [[semantic-coverage]], [[deterministic-diagrams]], [[archunit-drift-control]], [[codeowners-elders]], [[static-analysis-gauntlet]], [[acceptance-test-bdd]] (DB dump per test-tree floor, Playwright test agents), [[agentic-engineering]] (AFK time is a vanity metric, review economics, pain points).

*Security* — [[agent-auth]] (non-human identity, on-behalf-of), [[production-safety]] (cloud zombies, ExploitGym escape), [[docker-sandboxing]], [[secret-zero]], [[supply-chain-attack]] (Shai-Hulud, protestware), [[slopsquatting]], [[prompt-injection]] / [[tool-poisoning]], [[jailbreak-ladder]], [[lethal-trifecta]], [[openclaw]], [[approval-fatigue]], [[data-governance]] (copyright both ways).

*Human factors* — [[vibe-coding]] (Willison's line, business PRs as UX prototypes), [[vibe-fixer]], [[skill-erosion]] (10x learner before 10x shipper), [[cognitive-debt]].

## See also
- [[2026-08-31-ai-software-vendor]]
- [[2026-09-02-ai-insurer]]
- [[2026-06-26-ai-agentic-how]]
