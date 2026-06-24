---
title: Data Governance (ZDR)
category: security
tags: [security, privacy, zdr, compliance, data-residency, policy, gdpr]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-24
---


Data governance for agents is the policy layer that decides *which* model endpoints may legally see your source code and prompts — most concretely whether a vendor has signed a **Zero-Data-Retention (ZDR)** agreement.

## Zero-Data-Retention

A ZDR agreement means a vendor does not store your prompts and code and does not train on them. It is a contractual, not a technical, control — and it is endpoint-specific. A team can be cleared to use one provider's models and *forbidden* from another's, even for the identical task, purely on the basis of which ZDR contracts exist.

## The practical consequences

- **Cleared endpoint** (e.g. a vendor you hold ZDR with) — code and prompts may flow there.
- **Uncleared endpoints** (any provider without ZDR) — using them with company code is a **policy violation**, not a grey area.
- The "screenshot the code into a public chatbot when I'm out of quota" workaround is **explicitly off-limits** — it exfiltrates source to an uncleared endpoint.
- **Disable IDE AI plugins that route to uncleared providers** org-wide — an in-editor assistant can silently ship your `AGENTS.md` and code over the wire to a non-ZDR endpoint.

## Relationship to on-prem and sovereignty

ZDR governs *hosted* endpoints. The stronger guarantee is keeping inference inside your perimeter entirely: open-weights models like [DeepSeek](https://huggingface.co/deepseek-ai) run on [[ollama]] or via [[claude-code-router]] against an on-prem box — no data leaves at all. Note that routing to a *hosted* open-model API still leaves your perimeter; only the on-prem deployment buys true sovereignty. Frontier proprietary models can never be downloaded — they always run in a vendor datacenter, so ZDR is the only available control for them.

This is a different concern from runtime [[prompt-injection]] / exfiltration: data governance is about *where the data legitimately goes by policy*, while the [[lethal-trifecta]] and [[docker-sandboxing]] are about *what a compromised agent can leak*.

## See also
- [[ollama]]
- [[claude-code-router]]
- [[prompt-injection]]
- [[lethal-trifecta]]
- [[docker-sandboxing]]
- [[secret-zero]]
- [[2026-06-23-ai-garmin]]
