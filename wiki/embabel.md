---
title: Embabel
category: tool
tags: [embabel, jvm, agent-framework, workflow, java]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

[Embabel](https://github.com/embabel/embabel-agent) is a JVM agent framework created by Rod Johnson (founder of the Spring Framework) for enforcing structure and sequencing into multi-stage agentic workflows.

The framework is conceptually "like LangGraph for the JVM" — it lets you declare explicit workflow stages and transitions rather than letting an LLM decide its own execution path. This matters in production: an unconstrained agent driving a payment validation, a DB migration, or a customer-facing dispatch can skip steps, repeat actions, or take destructive shortcuts. Embabel provides the scaffolding to prevent that.

Victor describes it as a **"production firefighter"** — the tool you reach for when you need an agent to operate reliably inside a large or risky codebase and "still sleep at night." The standing challenge he articulated twice at [[2026-06-10-spring-ai-itkonekt]]: *how do you put an agent into a 5-million-line legacy monolith without it nuking prod?* Enforcing an explicit workflow via Embabel is one answer.

Embabel was teed up as the final topic of the session but the transcript ends before the deep dive, cut short by an internet outage. The concrete use case lined up was Victor's Barcelona engagement — applying agentic workflows to a large legacy codebase. It complements [[spring-ai]] (the chat-layer) rather than replacing it: Spring AI handles LLM calls and [[tool-calling]], Embabel handles the workflow graph that orchestrates when and in what order those calls happen.

It sits in the same design space as [[ralph-loop]] (batch workflows over clean context) but with stronger structural enforcement.

## See also
- [[spring-ai]]
- [[tool-calling]]
- [[ralph-loop]]
- [[guardrails]]
- [[elicitation]]
- [[dual-leg-rule]]
- [[2026-06-10-spring-ai-itkonekt]]
