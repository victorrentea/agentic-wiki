---
title: "Source: AI-Assisted Coding @ a live-casino game producer (2026-07-09 → 2026-07-10)"
category: source
tags: [source, live-casino, workshop, two-day, romanian, gaming]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop (taught in Romanian) for a mixed Java + PHP mid-senior group at a live-casino game producer. Day 1 laid the foundations under the running theme "agentic coding = addiction engineering": token efficiency and the quota lifestyle, model vs harness vs tools, sycophancy and hallucination, CLAUDE.md and skill descriptions (with a correction: the real limit is ≤1024 characters, not a word count), skill erosion, QA's new role, and the hardest manoeuvre of the day — showing the agent the problem instead of dictating the fix. Day 2 switched from "how do I write code with AI" to "how do I keep it on a leash": MCP features and when not to use them, functional and quality guardrails (and why a regex deny is a trap, not a railing), living diagrams generated from code and traces, multi-agent review that reads under 50% of the code, then a long security block — jailbreaks, prompt injection, supply-chain worms and slopsquatting, isolation and blast radius.

## Concept inventory

*Foundations & economics* — [[harness]] (brain / hands / tools), [[model-hierarchy]], [[token-economy]] (status line, `/insights`, quota lifestyle), [[headroom]], [[rtk]], [[prompt-caching]], [[plugin-marketplace]], [[hallucination]], [[knowledge-cutoff]], [[dumb-zone]].

*Knowledge engineering* — [[claude-md]] (post-mortems, regulated files, five retro targets), [[agents-md]], [[agent-skill]] (≤1024-char description, under-activation), [[agent-lifecycle-hooks]] (UserPromptSubmit `/clear` nudge, SessionStart worktree offer), [[handover]] (`/loop` bookkeeping), [[sycophancy]] (show the problem, not the solution).

*Specs, tests & feedback* — [[grill-me]], [[keyset-pagination]], [[acceptance-test-bdd]] (≈60% critical coverage, Playwright test agents, hidden tests), [[three-amigos]], [[feedback-loop]] (bug as failing test, pixel-diff loop), [[browser-mcp]].

*Review & living docs* — [[multi-model-review]] (throw the dice twice, review guider), [[code-review]], [[static-analysis-gauntlet]] (fast local / slow cloud split), [[field-reality-diagram]] (`@WithSpan`), [[c4-model]], [[archunit-drift-control]], [[code-graph]] (LSP over grep), [[codeowners-elders]].

*MCP* — [[model-context-protocol]], [[elicitation]], [[sampling]], [[cli-vs-mcp-tradeoff]] (65-tool Grafana MCP, DB MCP vs CLI).

*Security* — [[prompt-injection]], [[jailbreak-ladder]] (poetry, adversarial suffix, Gandalf), [[lethal-trifecta]], [[rag]] (RAG poisoning), [[supply-chain-attack]] (Shai-Hulud, slopsquatting, `npx`/`uvx`), [[skill-marketplace-security]], [[agent-permissions]] ("deny is binary"), [[os-sandbox]], [[docker-sandboxing]], [[production-safety]], [[dont-run-in-home-folder]].

*Human factors* — [[ai-addiction]] (addiction engineering), [[skill-erosion]] (self-training ladder), [[agentic-engineering]] (≈1.3× realistic gain, reactive defenses), [[vibe-fixer]].

## See also
- [[2026-07-01-ai-java-community]]
- [[2026-07-03-ai-bank]]
- [[2026-06-26-ai-agentic-how]]
