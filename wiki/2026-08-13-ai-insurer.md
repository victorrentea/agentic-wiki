---
title: "Source: AI-Assisted Coding @ an insurer (2026-08-13 – 2026-08-14)"
category: source
tags: [source, insurer, workshop, two-day, claude-code]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop with an engineering group that already pushes ≈95% AI-generated code after an internal bootcamp. Day 1 went from "what actually happens when you press Enter" — the system-prompt inventory, the 5-or-60-minute prompt cache and what silently invalidates it, the dumb zone and the cost of a sub-agent — to the tools that keep the agent on a leash: reactive rules vs deterministic guardrails, rules that expire with each model, path-scoped rules, sub-agents, browser drivers and an interview that finds facts by itself and asks you only for decisions. Day 2 started from tests (critical vs complex, acceptance tests the agent cannot read, component tests, file-based cases), climbed to diagrams generated from runs and guardrails over guardrails, a sorted review dashboard and tracer-bullet slices, then closed on the leash: CLI over MCP, restricted credentials, destructive-operation discipline, approval fatigue, persisted prompt injection and four sandboxing levels.

## Concept inventory

*Context & cost* — [[system-prompt]] (auto-memory, target under 25k), [[prompt-caching]] (TTL set by `cache_control.ttl`; mid-session edits invalidate it), [[dumb-zone]] (60–85%; ≈85 rules), [[sub-agents]] (≈25k franchise), [[effort-setting]], [[rtk]] / [[toon]], [[ralph-loop]] (night-shift recipe), [[ollama]] (open-weights equivalence table).

*Knowledge engineering* — [[agents-md-discipline]] / [[claude-md]] (rules expire; guardrail = 0 tokens), [[agent-skill]] / [[progressive-disclosure]] (three levels; path-scoped rules), [[code-graph]] (`/ide` and LSP-grade navigation), [[browser-mcp]] (what the accessibility tree can't see; the logged-in second profile).

*Requirements* — [[grill-me]] (layered numbered interview; facts vs decisions), [[spec-driven-development]] (when yes, when no), [[tracer-bullets]], [[skill-erosion]] (cognitive burnout, the vibe horizon), [[feedback-loop]] (feed it everything that isn't code).

*Tests & guardrails* — [[acceptance-test-bdd]] (critical vs complex, separate repo against overfitting, component tests, test language by reviewer), [[file-based-testing]], [[field-reality-diagram]], [[archunit-drift-control]] (the four escapes), [[reward-hacking]] (guards over guards), [[mutation-testing]].

*Review* — [[code-review]] (look "here and there"; someone else's agent PR), [[human-review-dashboard]], [[assumptions-ledger]], [[sycophancy]] (sycophantic panic), [[agentic-corporation]] (the dark-factory example).

*Security* — [[production-safety]] (destructive operations, take it out of auto mode for 10 minutes), [[approval-fatigue]], [[prompt-injection]] (library-shipped CLAUDE.md, WebFetch vs curl), [[memory-poisoning]], [[os-sandbox]] / [[docker-sandboxing]] (four leash levels, fake keys), [[cli-vs-mcp-tradeoff]].

## See also
- [[2026-09-30-ai-enterprise]]
- [[2026-08-10-ai-consultancy]]
- [[2026-08-27-ai-it-services]]
- [[2026-06-26-ai-agentic-how]]
