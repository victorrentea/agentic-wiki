---
title: Guardrails
category: security
tags: [security, defense-in-depth, advisor, classifier, spring-ai, jailbreak]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-08-06-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-06-10
updated: 2026-10-13
---

Guardrails are the layered defenses placed around an LLM chatbot to prevent harmful, off-topic, or adversarial output from reaching users.

<span style="color:red">The guardrail sits **outside the model**. The system prompt alone doesn't hold — the model already knows from training how to build a bomb — so a public chatbot needs a **separate guard at the input and another at the output**. (Cautionary tale: a bank's support chatbot cheerfully handing out a chicken-liver recipe.)</span>

## Defense layers (weakest to strongest)

### 1. Regex / keyword reject
Spring AI's `SafeGuardAdvisor` (see [Advisors](https://docs.spring.io/spring-ai/reference/api/advisors.html)) does a case-sensitive substring match before any model call. It is intentionally naive: a double space in *"ignore  instructions"* defeats it<span style="color:red">, and so do **ASCII-art prompts** ([ArtPrompt](https://arxiv.org/pdf/2402.11753))</span>. Value: cheaply kill obvious spam before burning tokens on the main model. Never rely on it alone.

### 2. System-prompt constraints
The [[system-prompt]] defines persona and "what not to do." Victor's honest label: *"pretty-please constraints"* — you are politely asking the model to behave. The system prompt never slides out of the [[context-window]] (only user/assistant turns age out), so the persona is always present. Still trivially bypassable via [[prompt-injection]]. <span style="color:red">State the bot's purpose there anyway, and assume it will leak ([OWASP LLM07: system prompt leakage](https://genai.owasp.org/llmrisk/llm072025-system-prompt-leakage/)) — never put secrets or rules you rely on for security in it.</span>

<span style="color:red">**A system prompt is not access control.** "Only show customer 42's data" in a prompt is *telling a child not to jump*. Authorize **at the tool**: hand off to an authorized agent whose MCP server enforces **row-level security** (e.g. [Postgres RLS](https://www.postgresql.org/docs/current/ddl-rowsecurity.html)) — see [[mcp-tenant-isolation]] and [[tool-calling]].</span>

### 3. Judge LLM
A separate cheap classifier screens **both** the incoming prompt and the outgoing response. This is the strongest practical layer. <span style="color:red">A classifier here means an LLM that answers only **pass/fail** (cf. Anthropic's [constitutional classifiers](https://www.anthropic.com/research/constitutional-classifiers)); an optional second judge checks the *response* stays on topic. **Use an off-the-shelf judge** — [Llama Guard](https://ai.meta.com/research/publications/llama-guard-llm-based-input-output-safeguard-for-human-ai-conversations/) or a prompt-injection classifier — rather than training your own.</span> See [[judge-llm]] for the full pattern, model-choice guidance, and why running different model families matters.

### <span style="color:red">4. Semantic-sense filter</span>
<span style="color:red">Against adversarial suffixes and token noise: **embed the prompt and reject text that has no semantic sense**. It is the text analogue of adversarial pixels that imperceptibly change a dog photo so an image classifier calls it a corkscrew.</span>

## Composition

In [[spring-ai]], guardrails are wired as [[advisor]] beans that wrap the `ChatModel` call chain — before-advice for input filtering, after-advice for output filtering. Multiple advisors stack; order matters (cheap regex first, expensive judge last).

The same layered-defence idea applies to an agent's *output code*, not just a chatbot's output text: the [[static-analysis-gauntlet]] (SonarQube / CodeQL / Semgrep as build-failing CI gates) and softer [[tripwire|tripwires]] are guardrails around what an agent writes, forcing self-repair before a human reviews.

**Each layer has a documented bypass.** The [[jailbreak-ladder]] maps the full escalation path (RLHF → abliteration → regex → judge LLM → context accumulation) and the specific technique that defeats each rung. <span style="color:red">Further rungs worth knowing: the **split prompt** (each half innocent, malicious when joined — [arXiv 2302.05733](https://arxiv.org/abs/2302.05733)); **adversarial suffixes** that make refusal improbable in any self-hosted LLM ([arXiv 2307.15043](https://arxiv.org/abs/2307.15043)); and an **emoji attack** that derails the judge itself ([arXiv 2411.01077](https://arxiv.org/pdf/2411.01077)).</span> None are guarantees; they're defense-in-depth. The only structural defense is removing a capability (see [[lethal-trifecta]]).

<span style="color:red">**Practice:** [Lakera Gandalf](https://gandalf.lakera.ai) is an 8-level prompt-injection sandbox — beating a few levels yourself is the fastest way to stop trusting layer 2.</span>

## See also
- [[prompt-injection]]
- [[judge-llm]]
- [[system-prompt]]
- [[advisor]]
- [[tool-calling]]
- [[mcp-tenant-isolation]]
- [[static-analysis-gauntlet]]
- [[tripwire]]
- [[jailbreak-ladder]]
- [[lethal-trifecta]]
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
