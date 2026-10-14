---
title: "Source: Responsibly Dimming the Lights in the Software Factory @ Devoxx Belgium (2026-10-06)"
category: source
tags: [source, devoxx, conference, talk, gray-factory]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Victor Rentea's 3-hour Devoxx Belgium deep dive on agentic engineering: one real feature of the petclinic demo app taken live with Claude Code from a vague ticket to a reviewed pull request — what it costs, where it fails, and how to keep humans in charge while the "lights go down" from vibe coding toward a dark factory. The talk is the public statement of the gray-factory idea: dim review gradually, treat every escaped defect as a reason to upgrade the agent or guardrail that missed it, hide holdout tests from the coder, review only requirement-level tests a human actually read, and let each team grow its own factory rather than adopt one built by an AI champion.

## Concept inventory

*The factory* — [[gray-factory]] (dim gradually, upgrade loop, factory culture), [[agentic-corporation]], [[vibe-coding]], [[vibe-fixer]] (business PRs).

*Before coding* — [[grill-me]] (bring questions, answer C, PO questions first), [[model-hierarchy]], [[spec-driven-development]] (golden tokens on disk, doc gardener), [[three-amigos]], [[agentic-engineering]] (agent as teacher, every source a colleague uses).

*While coding* — [[os-sandbox]] / [[docker-sandboxing]] (`/sandbox` by default), [[ci-green-loop]] / [[prompt-caching]] (CI waits re-bill the cache), [[ralph-loop]], [[legacy-migration-stages]] / [[light-dark-code]] (finish half-done migrations, show don't tell), [[openrewrite]].

*Review* — [[agentic-review-pipeline]] / [[static-analysis-gauntlet]] (deterministic gates, Stop-hook veto), [[multi-model-review]] / [[code-review]] (sunk-cost author, stacked PRs), [[assumptions-ledger]] / [[review-by-images]] (bug video, confidence), [[acceptance-test-bdd]] (sociable tests, "Viewed" coverage, holdout tests), [[keyset-pagination]].

## See also
- [[2026-10-07-agentic-reconversion-devoxx]]
- [[2026-09-30-ai-enterprise]]
- [[gray-factory]]
- [[agentic-review-pipeline]]
