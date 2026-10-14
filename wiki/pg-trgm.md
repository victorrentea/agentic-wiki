---
title: pg_trgm (Postgres Trigram Search)
category: tool
tags: [postgres, search, full-text, index, performance, sql]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-04-27-ai-healthcare-software]]"]
created: 2026-06-22
updated: 2026-10-13
---

`pg_trgm` is the Postgres extension that enables trigram-based GIN/GiST indexes, making substring search (`LIKE '%term%'` / `ILIKE '%term%'`) scale to large tables without a full sequential scan.

## Why plain indexes don't help for `LIKE '%term%'`

A standard B-tree index helps for prefix searches (`LIKE 'term%'`) but not for contains (`LIKE '%term%'`). Even a functional index like `lower(column)` only helps prefix matches. For substring search at scale, Postgres must scan the entire table — unacceptable at 100,000+ rows.

A trigram index decomposes every string into all its 3-character substrings (trigrams) and indexes them. A query for `'John'` becomes the set {`' Jo'`, `'Joh'`, `'ohn'`, `'hn '`}; Postgres intersects posting lists to find matching rows — far cheaper than a scan.

```sql
CREATE EXTENSION pg_trgm;
CREATE INDEX owners_name_trgm ON owners USING GIN (last_name gin_trgm_ops);
-- Now ILIKE '%term%' uses the index
```

## Limits — multi-column and multi-word queries

A trigram index on one column only covers that column. "Search across all visible columns" (owner name, city, phone) requires either:
- Multiple trigram indexes (one per searchable column) combined with `OR`/`EXISTS` — which still requires careful query planning.
- A **denormalized `tsvector` full-text search column** (the production-grade answer for complex multi-column, multi-word search).

Multi-word queries like `'John London'` (find owners named John in London) across joined tables (owner + pets) via correlated `EXISTS` subqueries can defeat trigram indexes — the planner may fall back to a scan when cardinality estimates are off.

## Red flags in AI-written search queries

<span style="color:red">Reviewers of an AI-written owner search caught a recurring set of index killers ([[2026-04-27-ai-healthcare-software]]):</span>
- <span style="color:red">`UPPER(column)` / `lower(column)` in the `WHERE` disables a plain index — it only works if there is a **functional index** on exactly that expression.</span>
- <span style="color:red">A long `OR` chain across predicates *expands* the result set instead of restricting it, and can derail the planner into a scan.</span>
- <span style="color:red">A leading wildcard (`'%term'`) disables B-tree and most other index types — only a trigram index survives it.</span>
- <span style="color:red">Prefer `EXISTS (SELECT … FROM pets …)` over `LEFT JOIN pets` + `DISTINCT`: no row multiplication, no dedup sort.</span>

## Diacritic-insensitive search

<span style="color:red">Three options for matching `Müller` when the user types `muller`: Postgres `unaccent()` wrapped in a functional (trigram) index, a generated normalized column with its own index, or an ICU collation with primary strength. **Pick one and record it in [[agents-md]]** — otherwise the AI improvises a different approach every time it touches search ([[2026-04-27-ai-healthcare-software]]).</span>

## ILIKE vs JPQL

When the alternative is Hibernate JPQL `LIKE lower(column)`, a functional `lower(column)` index plus `ILIKE` or JPQL are roughly equivalent — the index strategy, not the case-folding function, is what determines performance. An agent that pushes native `ILIKE` as "more efficient" without noting the index requirement is over-claiming.

## See also
- [[rag]]
- [[vector-search]]
- [[keyset-pagination]]
- [[grill-me]]
- [[spec-driven-development]]
- <span style="color:red">[[agents-md]]</span>
- <span style="color:red">[[code-review]]</span>
- [[2026-06-22-ai-sports-betting]]
