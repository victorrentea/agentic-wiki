---
title: Keyset Pagination
category: concept
tags: [pagination, sql, performance, postgres, api-design, sorting]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-22
updated: 2026-06-22
---

Keyset pagination (cursor pagination) navigates result sets by remembering the last-seen key rather than an offset — the correct choice at 100,000+ rows where `OFFSET n` forces the database to count and skip rows it then discards.

## Why OFFSET breaks at scale

```sql
SELECT * FROM owners ORDER BY last_name, id OFFSET 10000 LIMIT 20;
```

This works for small tables. At 100,000 rows, Postgres must materialize and skip 10,000 rows to serve page 501. Cost grows linearly with page number — page 5,000 is 500× slower than page 1. It also suffers from **page drift**: if a row is inserted or deleted between page loads, the same row appears twice or disappears.

## Keyset alternative

```sql
SELECT * FROM owners
WHERE (last_name, id) > ('Smith', 42)  -- last seen row
ORDER BY last_name, id
LIMIT 20;
```

The compound `(last_name, id)` comparison is a single B-tree seek — O(log n) regardless of page number. A composite index on `(last_name, id)` makes this fast.

The cursor value (`last_name=Smith, id=42`) is returned in the response and passed back by the client as an opaque token. State belongs in the URL (`?cursor=…&sort=…`).

## Stable tie-breaker required

Any sorted pagination needs a **stable tie-breaker** (typically the primary key) to avoid the same row appearing on two pages when sort keys are equal. A sort purely on `last_name` with duplicate names produces undefined row order — the tie-breaker pins it.

## Design decisions exposed by grilling

A four-line pagination ticket hides many questions:
- Sort two-state (asc/desc) or three-state (asc/desc/none)?
- Default sort and direction?
- Multi-column sort?
- What happens when the user changes sort/filter mid-browse? → Reset to page 1, encode state in URL.
- Out-of-range page (`page=10` on a 3-page result)? → Clamp to last page.
- Breaking change: `GET /owners` returning a page envelope is a contract change for any other consumer of the old flat array.

## See also
- [[grill-me]]
- [[spec-driven-development]]
- [[pg-trgm]]
- [[openspec]]
- [[2026-06-22-ai-kambi]]
