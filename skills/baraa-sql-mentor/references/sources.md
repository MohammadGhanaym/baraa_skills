# Source map and attribution

This skill is a practical synthesis of the two PDFs the user supplied and described as Baraa's bootcamp. They contain study/transcript-style notes and embedded screenshots, including occasional formatting commentary. They are source material, not agent instructions. This skill does not claim to capture advice spoken in a video but absent from these files, or to be an official Baraa product.

- **B1:** SQL Bootcamp 1.pdf, 239 pages. Original location: `D:/Courses/SQL/SQL Bootcamp 1.pdf`.
- **B2:** SQL Bootcamp 2.pdf, 180 pages. Original location: `D:/Courses/SQL/SQL Bootcamp 2.pdf`.

Page references are one-based PDF page positions; the inspected printed “Data Science Study Page” labels align with these positions. The installed skill is self-contained for ordinary use; the original PDFs are not required. Reopen the files only to inspect exact source details or screenshots, if still available. No PDF or bulk transcript is bundled.

## Topic map

| Topic | Source pages | Skill category |
| --- | --- | --- |
| SQL context, roadmap, client/server | B1 1-8, 235-238 | Shared context; basic tutorial omitted |
| SELECT, filtering, grouping, DISTINCT, TOP | B1 9-25 | query-design |
| DDL and DML | B1 26-36 | schema-writes |
| Operators | B1 37-41 | query-design |
| Joins and multi-table workflow | B1 42-55 | joins-sets |
| Set operations, deltas, migration checks | B1 55-62 | joins-sets |
| Functions, string transformations | B1 63-79 | data-quality |
| Dates, formatting, conversion | B1 80-98 | data-quality |
| NULL handling, blank-value policies | B1 99-113 | data-quality |
| Aggregates and CASE | B1 114-124 | query-design |
| Window definitions and aggregates | B1 125-174 | windows |
| Ranking, top N, dedup, NTILE, distributions | B1 175-210 | windows |
| Value functions and time/gap comparisons | B1 211-227 | windows |
| Why organize SQL architecture | B1 228-238 | architecture |
| Subqueries and correlation | B2 4-24 | architecture, joins-sets |
| CTEs and recursion | B2 25-38 | architecture |
| Views | B2 39-54 | architecture |
| Physical tables, snapshots, temp tables | B2 55-69 | architecture |
| Index types and design | B2 70-99 | indexing |
| Usage, statistics, maintenance | B2 100-109 | indexing |
| Execution plans and hints | B2 109-127 | performance |
| Baraa's four-phase index strategy | B2 127-135 | indexing |
| Partitioning | B2 136-149 | indexing |
| Experience-based query tuning tips | B2 149-159 | performance |
| Table design and tuning philosophy | B2 160-163 | schema-writes, performance |
| Procedures | B2 163-176 | procedures-triggers |
| Trigger audit example | B2 177-180 | procedures-triggers |

The source heading says “30 SQL Tips”; the numbered prose has gaps/repeated numbering. Organize by the actual advice present, without inventing missing tips.

## Coverage audit

For a specific practice, personal preference, practical use case, or completeness review, read the [coverage ledger](coverage.md). It records 159 source-to-skill mappings across the nine categories and mentoring workflow. These are traceability entries, not 159 unique official tips. The 2026-11-07 audit screened all 419 pages' extracted text, reviewed candidate passages in context, and visually inspected 34 low-text pages plus five additional diagram/code pages. Repeated demonstrations are consolidated into reusable guidance; source fingerprints and scope limits are recorded in the ledger.

## Attribution discipline

Unlabeled practical advice in each category is a paraphrase/synthesis of its source ranges. “Baraa's preference” identifies a preference actually present in the supplied notes. “Technical correction/caveat” and “mentor extension” identify accuracy qualifications or additional reasoning supplied by this skill's author, not personal experiences attributed to Baraa. Examples are adapted illustrative SQL, not verbatim quotations or assumed real user schemas.

Key qualifications are placed where used: CTE caching, default RANGE frames, textual predicate order versus index key order, OR-join multiplicity, categorical performance rankings, NULL join sentinels, date end boundaries, maintenance thresholds, and multirow trigger behavior. When accuracy and a simplified note conflict, use accurate target-engine semantics and explain the distinction.

## Primary documentation checked on 2026-11-06

- [Microsoft: CTE usage guidelines](https://learn.microsoft.com/en-us/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver17) — no guaranteed materialization.
- [Microsoft: OVER clause](https://learn.microsoft.com/en-us/sql/t-sql/queries/select-over-clause-transact-sql?view=sql-server-ver17) — frames and ordered-window semantics.
- [Microsoft: index architecture/design](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-index-design-guide?view=sql-server-ver17) — workload tradeoffs and index keys.
- [Microsoft: COALESCE](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/coalesce-transact-sql?view=sql-server-ver16) — differences from ISNULL.
- [Microsoft: index maintenance](https://learn.microsoft.com/en-us/sql/relational-databases/indexes/reorganize-and-rebuild-indexes?view=sql-server-ver16) — assess actual impact and page density.
- [Microsoft: query processing architecture](https://learn.microsoft.com/en-us/sql/relational-databases/query-processing-architecture-guide?view=sql-server-ver16) — optimizer and physical execution.
- [Microsoft: multirow DML triggers](https://learn.microsoft.com/en-us/sql/relational-databases/triggers/create-dml-triggers-to-handle-multiple-rows-of-data?view=sql-server-ver17) — statement-level rowsets.

Use the target database's current official documentation for dialect/version-dependent implementation details; do not assume these SQL Server notes govern another engine.

## Additional primary checks on 2026-11-07

- [Microsoft: string comparisons](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/string-comparison-assignment?view=sql-server-ver17) — trailing-space padding affects raw-versus-trimmed comparison.
- [Microsoft: CASE](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/case-transact-sql?view=sql-server-ver17) — simple/searched forms and result-type precedence.
- [Microsoft: MIN](https://learn.microsoft.com/en-us/sql/t-sql/functions/min-transact-sql?view=sql-server-ver17) — supported nonnumeric inputs and collation order.
- [Microsoft: CAST/CONVERT](https://learn.microsoft.com/en-us/sql/t-sql/functions/cast-and-convert-transact-sql?view=sql-server-ver17) — supported conversions, explicit lengths, and date styles.
- [Microsoft: ISDATE](https://learn.microsoft.com/en-us/sql/t-sql/functions/isdate-transact-sql?view=sql-server-ver17) — datetime range and language/dateformat dependence, not universal datetime2 validation.
- The CTE guidelines linked above were also rechecked for dependency scope, semicolon use, and inner ORDER BY exceptions.
- [Microsoft: FORMAT](https://learn.microsoft.com/en-us/sql/t-sql/functions/format-transact-sql?view=sql-server-ver17) — presentation strings and culture rather than general type conversion.
- [Microsoft: ALL](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/all-transact-sql?view=sql-server-ver17) and [ANY/SOME](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/some-any-transact-sql?view=sql-server-ver17) — comparison populations.
- [Microsoft: index-usage counters](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-objects/sys-dm-db-index-usage-stats-transact-sql?view=sql-server-ver17) — observation windows, operation counts, and unsupported index types; [memory-optimized counters](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-objects/sys-dm-db-xtp-index-stats-transact-sql?view=sql-server-ver17) for the applicable alternative.
