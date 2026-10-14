---
title: The Agent Loop
category: concept
tags: [agents, harness, tool-calling, fundamentals, architecture]
sources: ["[[2026-05-11-ai-software-vendor]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Every agent — Claude Code, a Spring AI app, LangChain4j or a hand-rolled harness — is the same small loop: call the model with the messages and the tool list, execute any tool call it returns, append the result, and repeat until the model stops.

## The loop

The minimal form, as taught in the Claude Certified Architect material ([[2026-05-11-ai-software-vendor]]):

```python
messages = [system, user_prompt]
while True:
    response = llm.chat(messages, tools=available_tools)
    if response.type == "stop":
        break
    if response.type == "tool_call":
        result = execute_tool(response.tool, **response.args)
        messages.append(response)
        messages.append({"role": "tool", "content": result})
```

Reference: [Anthropic tool-use docs](https://platform.claude.com/docs/en/docs/agents-and-tools/tool-use/overview). This is fundamental knowledge "for any developer actually working with AI, not just taping orchestration over frameworks".

## What falls out of the loop

- **The model is stateless.** "Memory" is just the growing `messages` list, re-sent on every iteration — which is why the [[context-window]] fills up and why [[prompt-caching]] matters so much.
- **Tool outputs accumulate and dominate cost.** Every result is re-read on every later turn; a verbose `mvn` log pays rent until the session ends ([[token-economy]]).
- **The only control points are the tools and the harness around them.** The model decides *what* to call; your code decides *what actually happens* — inside `execute_tool` ([[tool-calling]]) and in the hooks and permission checks wrapped around it ([[agent-lifecycle-hooks]], [[agent-permissions]]).

## Where control must live

Identity and authorization are enforced **inside `execute_tool`** — e.g. forwarding the user's JWT to an MCP server — never in the [[system-prompt]], which the model may forget or be talked out of ([[tool-context]], [[jwt-identity]]).

## Build it or reuse it?

Don't rebuild the loop unless you must. Reuse a hardened harness — `claude -p` / `--resume` ([[headless-claude]]) — and inherit its permissions, hooks and context management. Hand-roll it, or use a graph framework, only when you need a **bounded, deterministic action space**: a LangGraph-style state machine or [[embabel]]'s goal-oriented planning. [[sub-agents]] are the same loop nested: a tool call that starts a fresh loop with its own context.

## See also
- [[harness]]
- [[tool-calling]]
- [[context-window]]
- [[headless-claude]]
- [[embabel]]
- [[spring-ai]]
- [[sub-agents]]
- [[2026-05-11-ai-software-vendor]]
