---
title: Tenant Isolation Enforced in the MCP
category: security
tags: [mcp, multi-tenancy, row-level-security, authorization, tool-design]
sources: ["[[2026-04-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Tenant isolation in the MCP scopes every database query an agent makes to the single customer it is serving, by having the [[model-context-protocol|MCP]] server itself rewrite the SQL — security by tooling, not by prompting.

## The problem

A support agent with a database tool can, in principle, read any row. The tempting fix is a line in the [[system-prompt]]: *"only show customer 42's data."* That is *telling a child not to jump* — one [[prompt-injection]] or a confused turn and the agent reads another customer's orders. *"Asking the LLM 'don't show other people's data' is obviously not enough."* See [[guardrails]].

## The pattern

1. A **welcoming agent** identifies and authenticates the customer.
2. The session hands the database MCP **one `customer_id`**, out of band — never as a parameter the model chooses (see [[tool-context]]).
3. While processing any SQL the agent sends, the MCP server **injects an extra `AND customer_id = 42`** into the `WHERE` clause of every customer-scoped table (customer, order, payment).

The agent cannot bypass it, because the filter is nailed into the tool rather than requested in the prompt. Whatever the model writes, it only ever sees one tenant's rows.

## The database-native version

The same guarantee can live one layer lower, as **row-level security**: [Postgres RLS](https://www.postgresql.org/docs/current/ddl-rowsecurity.html), Oracle's equivalent, or [Supabase RLS](https://supabase.com/docs/guides/auth/row-level-security). Either way, the authorization lives below the model.

## Same family

The common idea is that **the tool is the control plane**:

- [[tool-context]] — identity passed out-of-band, never invented by the model ([[tool-calling]]).
- [[draft-only-email]] — the OAuth scope, not the prompt, decides the agent can only draft.
- [[firefighter-agent]] — limits scripted into the MCP (max 2 reboots, configuration only between fixed values).
- [[agent-auth]] — whose credentials the agent acts with.

It also removes one leg of the [[lethal-trifecta]]: the agent never has access to other tenants' private data in the first place.

## See also
- [[tool-context]]
- [[tool-calling]]
- [[guardrails]]
- [[agent-auth]]
- [[draft-only-email]]
- [[firefighter-agent]]
- [[observability-mcp]]
- [[enforcement-ladder]]
- [[lethal-trifecta]]
- [[2026-04-16-ai-safety-critical]]
- [[2026-09-28-ai-gaming-tech]]
