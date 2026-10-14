---
title: Keyset Pagination
category: concept
tags: [pagination, sql, performance, postgres, api-design, sorting]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-06-22
updated: 2026-10-13
---

Keyset pagination (cursor pagination) navigates result sets by remembering the last-seen key rather than an offset — the correct choice at 100,000+ rows where `OFFSET n` forces the database to count and skip rows it then discards.

<span style="color:red">Paginating a grid is the most-used live exercise across the sessions: a "four-line ticket" that, once grilled, exposes performance, security, collation, API-contract and UX decisions. This page collects them.</span>

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

The cursor value (`last_name=Smith, id=42`) is returned in the response and passed back by the client as an opaque token. State belongs in the URL (`?cursor=…&sort=…`). <span style="color:red">Cursor ids can be [ULID](https://github.com/ulid/spec)s ([[2026-07-09-ai-live-casino]]).</span>

## Stable tie-breaker required

Any sorted pagination needs a **stable tie-breaker** (typically the primary key) to avoid the same row appearing on two pages when sort keys are equal. A sort purely on `last_name` with duplicate names produces undefined row order — the tie-breaker pins it. <span style="color:red">Postgres documents that `LIMIT` with a non-unique order can [return the same row on two pages](https://www.postgresql.org/docs/current/queries-limit.html): with two "Popescu Ion" rows, one may show on both pages and the other on none ([[2026-09-30-ai-enterprise]]). The last `ORDER BY` column must be a *real* unique ID ([[2026-09-07-ai-bank]], [[2026-08-31-ai-software-vendor]]) — "one person in three groups knows it" ([[2026-09-18-ai-agentic-how]]).</span>

## Correctness bugs everyone ships

- <span style="color:red">**Sort the whole list, not the visible page.** A production bug survived 6 months because sorting applied only to the current page — nobody had combined sort + paging + page size in one test ([[2026-07-20-ai-telecom]]). The correct order is `WHERE → ORDER BY → LIMIT` ([[2026-08-03-ai-telecom-ops-qa]]).</span>
- <span style="color:red">**Sort by what the user sees.** The agent sorted by `last_name, first_name` but the grid displayed "First Last", so clicking *Name* sorted by the second word — missed by 15 people; resolved by a business decision to display "Last, First" ([[2026-08-03-ai-telecom-ops-qa]]). Sorting people by family name ("Potter, Harry") is the catalogue convention and disperses better; an agent caught it too ([[2026-08-06-ai-bank]], [[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">**Changing search or sort resets to page 1** ([[2026-08-31-ai-software-vendor]]).</span>

## Collation: "Ș" next to "S"

<span style="color:red">By default diacritics sort **after Z** ([[2026-08-03-ai-telecom-ops-qa]]). Human-like sorting — case- and diacritic-insensitive — needs a **linguistic collation** on the query or column (`unaccent` / ICU in Postgres) ([[2026-06-05-ai-consultancy]], [[2026-09-02-ai-insurer]]):</span>

- <span style="color:red">The sort index must be [created with the same collation](https://www.postgresql.org/docs/current/indexes-collations.html), or Postgres ignores it and does a full scan ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">Changing a column's collation with `ALTER COLUMN TYPE` takes an Access Exclusive lock and rewrites the table ([[2026-09-07-ai-bank]]).</span>
- <span style="color:red">A good Polish collation isn't right for Romanian — it is a per-language architecture decision, or a job for Elasticsearch's [`icu_collation_keyword`](https://www.elastic.co/docs/reference/elasticsearch/plugins/analysis-icu-collation-keyword-field) ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">The agent may propose the extension itself — verify it's installable on the production instances, not just locally ([[2026-06-05-ai-consultancy]]). In one session Opus ran its own Postgres experiment and corrected its first answer ([[2026-09-02-ai-insurer]]).</span>

## Pagination is a security control

- <span style="color:red">**Whitelist the sortable columns.** A `sort` query parameter concatenated into `ORDER BY` is SQL injection; accept only the listed columns (three, in one demo) and reject anything else ([[2026-06-05-ai-consultancy]], [[2026-07-20-ai-telecom]], [[2026-09-07-ai-bank]], [[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">**Cap the page size** server-side, or someone requests `?page_size=3000000` (or a 40-million-row page): a DoS vector and an OOM. In Spring: `PageableHandlerMethodArgumentResolverCustomizer` with `setMaxPageSize(100)` ([[2026-04-07-ai-product-team]]); stricter, accept page sizes only from a whitelist such as 5/10/20 ([[2026-08-06-ai-bank]], [[2026-06-08-ai-security-tools]]).</span>
- <span style="color:red">**Make the endpoint always paginated** — optional pagination leaves room for DoS ([OWASP API4: unrestricted resource consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/), [[2026-09-30-ai-enterprise]]).</span>

## Indexes and ORM traps

- <span style="color:red">**Index the sortable columns**, and mark the ones that make no sense to sort (address, phone, pets) ([[2026-07-09-ai-live-casino]]); handed a DB connection, Opus retracted those as sort keys itself ([[2026-09-18-ai-agentic-how]]). Prefer 2 indexes over 5 — each costs on insert ([[2026-09-07-ai-bank]]).</span>
- <span style="color:red">Sorting on a comma-joined child collection (`string_agg`) defeats indexes — don't make it sortable ([[2026-04-27-ai-healthcare-software]]).</span>
- <span style="color:red">**Joins break `LIMIT`.** An ORM query that left-joins owners → pets → types → visits and then applies skip/take paginates **in memory**, because rows multiply through the join and `LIMIT` no longer means "the first 10 owners"; in Hibernate, `LEFT JOIN FETCH` + paging is a known OOM ([[2026-06-05-ai-consultancy]], [[2026-09-16-ai-safety-critical]]). Fix: a **two-phase query** — paginate the driving table, then fetch the children separately — or ask why you load children the UI doesn't show and fetch the displayed row directly with names aggregated (`array_agg` / window query).</span>
- <span style="color:red">An N+1 visible in the SQL log is fixed with Hibernate's [`@BatchSize`](https://docs.hibernate.org/orm/6.6/javadocs/org/hibernate/annotations/BatchSize.html) on the relation (or the entity class) — no other code change ([[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">A `count` query on every request is expensive — make it an explicit decision, not a default ([[2026-08-31-ai-software-vendor]]).</span>

## API contract

- <span style="color:red">**Array → page envelope is a breaking change**; an extra field in the response is not. The question becomes "do I have other clients of this API?" — impact analysis in one session found a hidden consumer, a chatbot endpoint ([[2026-07-20-ai-telecom]], [[2026-09-07-ai-bank]], [[2026-09-14-ai-industrial]]). See [[openapi-contract-sync]].</span>
- <span style="color:red">**Don't couple the REST API to UI composition**: the front end sends only the clicked column (`name`); the back end maps it to `(lastName, firstName)` ([[2026-07-20-ai-telecom]]).</span>
- <span style="color:red">**Spring Data `Page`**: Spring Data does not forbid serialising a `PageImpl` — it [warns since 3.1](https://docs.spring.io/spring-data/commons/reference/repositories/core-extensions.html) that its JSON shape isn't stable. The stable alternative is `PagedModel` (3.3+), obtainable without a hand-written DTO via `spring.data.web.pageable.serialization-mode=via-dto` ([[2026-10-06-gray-factory-devoxx]], [[2026-09-16-ai-safety-critical]]). Earlier sessions phrased it as "never return `Page`" ([[2026-08-31-ai-software-vendor]], [[2026-09-07-ai-bank]]) — the point stands, as a contract-stability choice: a Spring upgrade shouldn't change your API. It was learned *from the agent* — "you're no longer the smartest, even alone in the room".</span>

## Front end: the URL is the source of truth

<span style="color:red">Page, sort and search live in the **query params**, so refresh, back and share rebuild the exact view ([[2026-07-09-ai-live-casino]], [[2026-07-20-ai-telecom]]). In one session a refresh lost the state — found only by looking at the screen, not the code ([[2026-09-30-ai-enterprise]]; see [[review-by-images]]). Update the URL with **replace, not push** navigation (`router.navigate(…, {replaceUrl: true})`) so history isn't polluted by every sort click ([[2026-05-04-ai-telecom]]). Reject infinite scroll by reflex ([[2026-07-20-ai-telecom]]).</span>

## Design decisions exposed by grilling

A four-line pagination ticket hides many questions:
- Sort two-state (asc/desc) or three-state (asc/desc/none)?
- Default sort and direction?
- Multi-column sort? <span style="color:red">(Tie-break within a sort: same city → by name — [[2026-09-02-ai-insurer]].)</span>
- What happens when the user changes sort/filter mid-browse? → Reset to page 1, encode state in URL.
- Out-of-range page (`page=10` on a 3-page result)? → Clamp to last page.
- Breaking change: `GET /owners` returning a page envelope is a contract change for any other consumer of the old flat array.
- <span style="color:red">Which columns are sortable at all, which page sizes are allowed, which collation, and is the total count worth its query?</span>

## See also
- [[grill-me]]
- [[spec-driven-development]]
- [[pg-trgm]]
- [[openspec]]
- <span style="color:red">[[openapi-contract-sync]]</span>
- <span style="color:red">[[review-by-images]]</span>
- <span style="color:red">[[2026-07-20-ai-telecom]]</span>
- <span style="color:red">[[2026-09-02-ai-insurer]]</span>
- <span style="color:red">[[2026-09-07-ai-bank]]</span>
- [[2026-06-22-ai-sports-betting]]
