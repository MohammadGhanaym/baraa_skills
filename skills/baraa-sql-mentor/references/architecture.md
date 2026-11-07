# Organizing SQL logic

Bootcamp grounding: B1 pp. 228-238; B2 pp. 4-69, especially pp.33, 53, 62, 69.

## Choose scope, persistence, and freshness

Baraa's stated preference order is views, CTEs, subqueries, CTAS tables, then temporary tables (B2 p.69). This expresses his project experience, not a requirement to replace a simple query with a view. Select the smallest mechanism that satisfies the current scope.

| Need | Candidate | Tradeoff to explain |
| --- | --- | --- |
| One simple intermediate expression | Subquery | Local scope; deeply nested expressions become difficult to inspect |
| Several named stages in one statement | CTE | Readability and logical reuse; no guaranteed materialization |
| Shared business logic across queries/users | View | Persisted definition; ordinary view recalculates underlying logic |
| Expensive repeatable result; freshness delay allowed | Physical summary/snapshot table | Stored results need refresh, ownership, and failure handling |
| Multi-statement work or repeatedly reused intermediate data | Temporary table | Materialization/storage overhead, session/scope lifetime |

Baraa recommends starting data marts with views for flexibility, then moving expensive logic to physical results when measured response times require it. Do not add a persisted object to an ad hoc task without a reuse or latency need. Views centralize definitions and present stable interfaces; changing base schemas still requires maintaining those interfaces.

## CTEs and subqueries

- Give stages business-meaningful names and inspect intermediate outputs. For a dependent stage, include its dependencies when testing; a correlated subquery needs a real outer-row context or a representative bound value.
- A scalar subquery must return at most one row; use EXISTS/IN for existence or membership. Do not conceal a non-unique relationship with arbitrary TOP 1.
- Baraa's three-to-five CTE guideline is a prompt to reconsider readability, not a hard ceiling. Consolidate trivial layers; preserve stages that make the calculation clearer.
- Technical correction to B2 pp.28-29, 67-68: SQL Server CTEs are not guaranteed cached result tables. Reusing the name avoids duplicated source code, not necessarily repeated physical work. Consider a temporary table when measured repeated work makes materialization useful. See [Microsoft CTE documentation](https://learn.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver17).
- Recursive CTEs suit hierarchies. Mentor extensions: establish anchor, parent-child direction, termination, and cycle handling; use a finite recursion guard appropriate to the data. Do not use unlimited recursion to conceal a cycle.

## Views, snapshots, and temporary tables

- Use a schema-qualified view name and explicit columns. Mentor extension: prefer CREATE OR ALTER where the engine/version supports it and preserving the existing object matters; do not automatically copy the notes' DROP/recreate workflow and lose grants or dependencies.
- Views expose subsets of columns/rows, but access restriction works only with a matching permission design. A view alone is not authorization. “Fresh” means what the query can observe under its transaction/isolation context, not a guarantee of real-time source ingestion.
- SQL Server commonly uses SELECT ... INTO for creating a table from a result; do not paste another engine's CTAS syntax into T-SQL. Inspect resulting types and add required keys, indexes, and constraints deliberately.
- For snapshots define refresh cadence, acceptable staleness, incremental/full strategy, and how readers avoid partially refreshed data. Propose the job when relevant; do not schedule it unless requested.
- Local #temp tables suit session-scoped intermediate work; procedure-created local temporary tables may have a shorter scope. Use the correct engine's lifecycle. Do not categorically reject temporary tables because Baraa uses them less often.

Mentor extension: isolate analytics from write-heavy operational systems when workload contention is demonstrated; use an appropriate reporting layer rather than making an unrelated infrastructure change.

## Additional project use cases

- **Multilingual views (B2 p.52):** provide translated view/column aliases for different user groups while keeping underlying data and business logic centralized. Alias translation does not translate stored values. Maintain equivalent contracts across languages and use the actual dialect's identifier quoting.
- **Frozen diagnostic snapshots (B2 p.60):** when a data-quality investigation is moving because the source changes, capture the relevant data once in a persistent table and analyze that version. Record capture time and scope. Mentor extension: a copy query must use suitable transaction/isolation semantics if a point-in-time-consistent cross-table capture is needed; SELECT INTO alone does not guarantee that consistency.
- **Isolated ETL staging (B2 pp.63-66):** extract to #temp, transform/inspect the copy, then load a deliberately chosen permanent target. This allows cleaning without changing the operational source; an intermediate temp table does not grant permission to write back. In SQL Server it lives in tempdb, not in the user's schema.
- **Architecture pain points (B1 pp.228-234):** duplicated business logic across teams, repeatedly expensive queries, hard-to-understand source relationships, and overly broad access motivate the five techniques. Choose the mechanism that addresses the demonstrated problem instead of wrapping every query in another object.
- **Metadata-led exploration (B1 pp.236-237; B2 pp.100-101):** inspect INFORMATION_SCHEMA.COLUMNS for common column metadata and SQL Server sys catalog views for engine-specific details, relationships, and indexes. Do not guess the schema from sample values. Technical qualification: INFORMATION_SCHEMA is a metadata interface, not a complete or hidden description of all SQL Server features; visible metadata is permission-scoped.

## CTE and subquery implementation details

B2 pp.30-38: use one WITH followed by comma-separated named CTEs and then its consuming statement; a dependent CTE references an earlier definition. The notes' “nested CTE” commonly means that dependency chain, not a second WITH placed inside a SQL Server CTE definition. Terminate the preceding statement with a semicolon in a batch. Recursive anchor plus recursive member commonly uses UNION ALL; select the correct root and increment level explicitly for organizational trees, and ensure matching result types. Sequence generation is another course example; use a simpler supported series source when that better fits the task.

Technical qualification to the notes' ORDER BY restriction: a CTE cannot use ordinary presentation ORDER BY, but TOP or OFFSET/FETCH can permit an order clause where supported. This still does not order the final result; use outer ORDER BY. See [Microsoft CTE guidelines](https://learn.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver17).

B2 pp.7-14: alias derived tables and output expressions; scalar subqueries return one column and at most one row (zero rows becomes NULL in a scalar context). Depict subqueries/CTEs as logical steps when teaching, not as guaranteed cached physical tables or a guaranteed execution count. An ordinary view persists logic, a snapshot persists results, and buffer/plan caches are different mechanisms.
