# Bootcamp practice coverage ledger

Audit date: 2026-11-07. This ledger is for source lookup and coverage review; ordinary SQL tasks should load the relevant category directly.

The audit records **159 source-to-skill mappings** from the supplied PDFs. A practice can appear under more than one category; these are not 159 unique official tips. Repeated demonstrations are consolidated. This is a traceability inventory, not a claim to reconstruct material absent from the files.

## How to read it

- **Preserved:** the practical decision is carried into the skill.
- **Preference:** explicitly identified as Baraa's preference rather than a universal requirement.
- **Qualified:** the lesson is retained with accuracy, semantics, dialect, measurement, or execution-scope qualifications; these qualifications are not attributed to Baraa.

Each destination is a focused reference containing operational guidance, not just a coverage label. Entries tagged qualified may already have been qualified in the first version.

| # | Practice / project lesson | Source PDF pages | Where to use it | Treatment |
| --- | --- | --- | --- | --- |
| 1 | Confirm the intended database before execution | B1 9 | [query-design](query-design.md) | Preserved |
| 2 | Select needed columns; bound exploratory reads | B1 10-11; B2 149-150 | [query-design](query-design.md) | Preserved |
| 3 | Indent expressions, alias metrics, specify sort direction and precedence | B1 11,13-15 | [query-design](query-design.md) | Preserved |
| 4 | Separate row filters from aggregate filters and logical from written order | B1 17-19,24-25 | [query-design](query-design.md) | Qualified |
| 5 | Choose output grain before grouping or windowing | B1 15-17,125-127 | [query-design](query-design.md) | Preserved |
| 6 | Remove unnecessary DISTINCT rather than using it by habit | B1 21; B2 150 | [query-design](query-design.md) | Qualified |
| 7 | Use ordered top/bottom limiting with deliberate ties | B1 21-23 | [query-design](query-design.md) | Qualified |
| 8 | Prioritize CASE conditions and define boundaries/defaults | B1 117-120 | [query-design](query-design.md) | Qualified |
| 9 | Profile distinct codes before mapping; choose simple/searched CASE | B1 121-122 | [query-design](query-design.md) | Preserved |
| 10 | Use compatible CASE branch types and explicit conversions | B1 120,124 | [query-design](query-design.md) | Qualified |
| 11 | Apply NULL policy and conditional aggregation deliberately | B1 122-123 | [query-design](query-design.md) | Preserved |
| 12 | COUNT star versus constant; aggregate input-type distinctions | B1 144,146,173 | [query-design](query-design.md) | Qualified |
| 13 | Choose join semantics by required preserved rows | B1 44-47,53-55 | [joins-sets](joins-sets.md) | Preserved |
| 14 | Prefer LEFT over RIGHT and master-table enrichment when appropriate | B1 45-47,52-55 | [joins-sets](joins-sets.md) | Preference |
| 15 | Explicit join types, aliases, qualified columns, verified schema keys | B1 47,54-55 | [joins-sets](joins-sets.md) | Preserved |
| 16 | Build joins incrementally and inspect result cardinality | B1 54-55 | [joins-sets](joins-sets.md) | Qualified |
| 17 | Distinguish matching predicates from filters of the completed outer join | B1 52-55 | [joins-sets](joins-sets.md) | Qualified |
| 18 | Anti-joins for absent matches; inspect both sides in reconciliation | B1 48-50,110-111 | [joins-sets](joins-sets.md) | Qualified |
| 19 | CROSS JOIN for deliberate test grids and attribute combinations | B1 51 | [joins-sets](joins-sets.md) | Qualified |
| 20 | Set branches must align count, meaning, types, and positions | B1 56-58 | [joins-sets](joins-sets.md) | Preserved |
| 21 | First-branch names and final ordering for combined results | B1 56-58 | [joins-sets](joins-sets.md) | Qualified |
| 22 | UNION ALL when duplicate preservation is intended; label sources | B1 59-61; B2 157 | [joins-sets](joins-sets.md) | Preserved |
| 23 | EXCEPT deltas and migration checks; INTERSECT overlap | B1 60-62 | [joins-sets](joins-sets.md) | Qualified |
| 24 | JOIN versus IN/EXISTS for cross-table filtering; preserve source preference | B2 16-23,158-159 | [joins-sets](joins-sets.md) | Preference + qualified |
| 25 | ANY versus ALL comparisons, empty and NULL-containing populations | B2 17-18 | [joins-sets](joins-sets.md) | Qualified |
| 26 | SELECT 1 inside EXISTS expresses existence intent | B2 21 | [joins-sets](joins-sets.md) | Qualified |
| 27 | OR-join rewrites and DISTINCT/UNION alternatives require equivalence | B2 156-157 | [joins-sets](joins-sets.md) | Qualified |
| 28 | Transform in intended order and inspect intermediate expressions | B1 65-79 | [data-quality](data-quality.md) | Preserved |
| 29 | Combine strings with deliberate separators and missing components | B1 67-70,105-106 | [data-quality](data-quality.md) | Qualified |
| 30 | Detect and trim surrounding spaces; account for SQL Server padding | B1 70-72 | [data-quality](data-quality.md) | Preference + qualified |
| 31 | Normalize phone punctuation or filename text with REPLACE | B1 73-74 | [data-quality](data-quality.md) | Qualified |
| 32 | Use dialect-correct lengths and trim before character extraction | B1 75-79 | [data-quality](data-quality.md) | Qualified |
| 33 | Prefer portable COALESCE and support ordered fallback columns/defaults | B1 99-104 | [data-quality](data-quality.md) | Preference + qualified |
| 34 | Do not universalize ISNULL speed or interchangeable typing | B1 103-104 | [data-quality](data-quality.md) | Qualified |
| 35 | Align NULL replacement and aggregate denominators with business meaning | B1 104-105,157-159 | [data-quality](data-quality.md) | Preserved |
| 36 | Handle NULLs in arithmetic/concatenation without inventing values | B1 105-107 | [data-quality](data-quality.md) | Qualified |
| 37 | Inspect nullable join keys; avoid false matches from sentinels | B1 106-107 | [data-quality](data-quality.md) | Qualified |
| 38 | Sort missing values explicitly instead of arbitrary high sentinels | B1 107 | [data-quality](data-quality.md) | Preserved |
| 39 | Normalize documented sentinel values with NULLIF | B1 108-109 | [data-quality](data-quality.md) | Qualified |
| 40 | Guard zero denominators and use appropriate numeric precision | B1 109 | [data-quality](data-quality.md) | Qualified |
| 41 | Use IS NULL/IS NOT NULL to inspect missing information | B1 109-111 | [data-quality](data-quality.md) | Preserved |
| 42 | Agree blank/empty/NULL policies; normalize in ETL and label reports | B1 111-114 | [data-quality](data-quality.md) | Preference + qualified |
| 43 | Do not infer a universal NULL/blank storage-speed hierarchy | B1 112 | [data-quality](data-quality.md) | Qualified |
| 44 | Compare row counts/nonmissing counts; identify duplicate business keys | B1 145-152 | [data-quality](data-quality.md) | Preserved |
| 45 | Validate unconstrained extracts and retain exceptions for source repair | B1 150-151,195 | [data-quality](data-quality.md) | Qualified |
| 46 | Choose numeric date parts, names, period starts, and month ends by need | B1 83-87 | [data-quality](data-quality.md) | Qualified |
| 47 | Keep period keys typed and use complete year-month/date boundaries | B1 85-91; B2 151-152 | [data-quality](data-quality.md) | Qualified |
| 48 | Distinguish display formatting from type conversion and culture | B1 87-94 | [data-quality](data-quality.md) | Qualified |
| 49 | Check format-token case, explicit text length, and actual CONVERT styles | B1 87-94 | [data-quality](data-quality.md) | Qualified |
| 50 | Use date arithmetic for age, shipping duration, and order gaps | B1 95-97 | [data-quality](data-quality.md) | Qualified |
| 51 | Validate incoming date text against its intended type and style | B1 97-98 | [data-quality](data-quality.md) | Qualified |
| 52 | Retain row detail and calculate overall/multiple grouped totals together | B1 125-132 | [windows](windows.md) | Preserved |
| 53 | Translate population, ordering, and frame separately | B1 128-134 | [windows](windows.md) | Preserved |
| 54 | Running, trailing, forward, and centered frames answer different questions | B1 134-138,164-172 | [windows](windows.md) | Qualified |
| 55 | SQL Server default ordered frame is RANGE, not ROWS | B1 138 | [windows](windows.md) | Qualified |
| 56 | Window placement, staging, no direct nesting, and grouped-window legality | B1 139-142 | [windows](windows.md) | Qualified |
| 57 | Filter placement determines the population used for window baselines | B1 141,160-161 | [windows](windows.md) | Qualified |
| 58 | Overall/group counts and sales totals with order details | B1 147-154 | [windows](windows.md) | Preserved |
| 59 | Window COUNT flags every row in a duplicate group | B1 149-152 | [windows](windows.md) | Preserved |
| 60 | Part-to-whole share with correct entity grain and decimal arithmetic | B1 155-156 | [windows](windows.md) | Qualified |
| 61 | Average/extreme comparisons and deviations, including all tied maxima | B1 160-164,173 | [windows](windows.md) | Qualified |
| 62 | Distinguish above/below-mean inspection from a statistical anomaly rule | B1 173 | [windows](windows.md) | Qualified |
| 63 | Cumulative and finite moving averages; rows versus calendar periods | B1 168-170 | [windows](windows.md) | Qualified |
| 64 | ROW_NUMBER, RANK, DENSE_RANK tie and gap behavior | B1 175-187 | [windows](windows.md) | Qualified |
| 65 | Baraa's frequent practical use of ROW_NUMBER | B1 186-187 | [windows](windows.md) | Preference |
| 66 | Top/bottom analysis; aggregate to the ranked entity first | B1 187-190 | [windows](windows.md) | Qualified |
| 67 | Result identifiers and pagination/export; persist identity when needed | B1 190-192 | [windows](windows.md) | Qualified |
| 68 | Choose latest record using business key and stable survivor ordering | B1 192-195 | [windows](windows.md) | Qualified |
| 69 | NTILE segmentation, remainder buckets, and ties | B1 195-199 | [windows](windows.md) | Qualified |
| 70 | ETL load buckets, primary-key ordering, and recombination | B1 200-202 | [windows](windows.md) | Preference + qualified |
| 71 | CUME_DIST/PERCENT_RANK formulas and distribution-based filtering | B1 202-210 | [windows](windows.md) | Qualified |
| 72 | LEAD/LAG time comparisons, growth, missing prior observations | B1 211-217 | [windows](windows.md) | Qualified |
| 73 | Order-gap averages and customer repeat-purchase ranking | B1 217-220 | [windows](windows.md) | Qualified |
| 74 | FIRST/LAST value frame versus MIN/MAX and reversed ordering | B1 221-226 | [windows](windows.md) | Qualified |
| 75 | Function argument requirements and frame restrictions | B1 177,210-212 | [windows](windows.md) | Qualified |
| 76 | Reduce duplicate logic, repeated cost, complexity, and broad base access | B1 228-234 | [architecture](architecture.md) | Preserved |
| 77 | Use catalog metadata; distinguish storage, buffers, and plan cache | B1 235-238 | [architecture](architecture.md) | Qualified |
| 78 | Derived tables, scalar contexts, aliases, and stepwise debugging | B2 4-14 | [architecture](architecture.md) | Qualified |
| 79 | Correlated subquery debugging needs the outer-row context | B2 19-24 | [architecture](architecture.md) | Qualified |
| 80 | Logical CTE reuse does not guarantee materialized/cached results | B2 25-29 | [architecture](architecture.md) | Qualified |
| 81 | CTE dependency chains and one-statement scope | B2 30-32 | [architecture](architecture.md) | Qualified |
| 82 | Inner CTE ordering restrictions and final ordering | B2 30 | [architecture](architecture.md) | Qualified |
| 83 | Three-to-five CTE readability guideline is not a hard limit | B2 33,38 | [architecture](architecture.md) | Preference |
| 84 | Recursive sequences/hierarchies, anchor, level, and termination guard | B2 33-38 | [architecture](architecture.md) | Qualified |
| 85 | Centralize reusable logic in schema-qualified views | B2 43-46 | [architecture](architecture.md) | Preserved |
| 86 | Rerunnable object definitions without careless DROP/recreate | B2 46-47 | [architecture](architecture.md) | Qualified |
| 87 | Abstract complex multi-table relationships behind a view | B2 48-49 | [architecture](architecture.md) | Preserved |
| 88 | Restricted projections/rows require a matching permission design | B2 49-51 | [architecture](architecture.md) | Qualified |
| 89 | Maintain a stable view contract when physical models change | B2 51-52 | [architecture](architecture.md) | Preserved |
| 90 | Multilingual view/column aliases for international teams | B2 52 | [architecture](architecture.md) | Qualified |
| 91 | Start with virtual data marts; materialize demonstrated slow paths | B2 53,61-62 | [architecture](architecture.md) | Preference + qualified |
| 92 | Persist logic versus persist data; target-engine create-from-query syntax | B2 55-59 | [architecture](architecture.md) | Qualified |
| 93 | Refresh materialized results deliberately with acceptable staleness | B2 59-60 | [architecture](architecture.md) | Qualified |
| 94 | Freeze data-quality investigations with a diagnostic snapshot | B2 60 | [architecture](architecture.md) | Qualified |
| 95 | Tempdb staging, session/scope lifetime, isolated transforms and load | B2 62-66 | [architecture](architecture.md) | Qualified |
| 96 | Personal technique ranking and evolution from query to shared/persisted logic | B2 67-69 | [architecture](architecture.md) | Preference |
| 97 | Estimated/actual/live plans and actual-versus-estimated row observations | B2 109-112 | [performance](performance.md) | Qualified |
| 98 | Inspect data flow, rows read, lookups, sorts, and physical operators | B2 112-119 | [performance](performance.md) | Qualified |
| 99 | Index order can avoid a Sort without guaranteeing output order | B2 114-115 | [performance](performance.md) | Qualified |
| 100 | Remove unneeded projection before covering expensive key lookups | B2 118-120 | [performance](performance.md) | Preserved |
| 101 | Physical loop/hash/merge tradeoffs and analytical storage comparison | B2 119-123 | [performance](performance.md) | Qualified |
| 102 | Save baseline plans and compare showplans side by side | B2 123-124 | [performance](performance.md) | Preserved |
| 103 | Join/index/seek hints as measured temporary workarounds | B2 124-127 | [performance](performance.md) | Preference + qualified |
| 104 | Optimize demonstrated bottlenecks; readable code when results/plans align | B2 149,163 | [performance](performance.md) | Preserved |
| 105 | Needed projection, limited exploration, necessary DISTINCT/sorting | B2 149-150 | [performance](performance.md) | Preserved |
| 106 | Searchable typed predicates, date ranges, appropriate prefix search | B2 150-152 | [performance](performance.md) | Qualified |
| 107 | IN improves repeated-equality readability without a guaranteed speedup | B2 152 | [performance](performance.md) | Qualified |
| 108 | Explicit JOIN semantics and join-key index candidates | B2 153-154 | [performance](performance.md) | Qualified |
| 109 | Prefilter/preaggregate when it preserves grain and reduces measured work | B2 154-155 | [performance](performance.md) | Qualified |
| 110 | Do not categorically reject correlated subqueries by syntax | B2 155 | [performance](performance.md) | Qualified |
| 111 | OR-join rewrites preserve pair multiplicities | B2 156 | [performance](performance.md) | Qualified |
| 112 | Investigate costly loops rather than force hash from table size alone | B2 156 | [performance](performance.md) | Qualified |
| 113 | UNION ALL and UNION/DISTINCT alternatives depend on required deduplication | B2 157 | [performance](performance.md) | Qualified |
| 114 | Consider columnstore and refreshed summaries for repeated analytic work | B2 157-158 | [performance](performance.md) | Qualified |
| 115 | Existence filters and joins chosen for semantics plus measurement | B2 158-159 | [performance](performance.md) | Qualified |
| 116 | Consolidate repeated scans/calculations with windows/CASE; preserve equal/NULL rows | B2 159 | [performance](performance.md) | Qualified |
| 117 | Clustered/nonclustered/heap organization and read/write tradeoffs | B2 70-79 | [indexing](indexing.md) | Qualified |
| 118 | Stable clustered key and workload-driven nonclustered candidates | B2 79-82 | [indexing](indexing.md) | Qualified |
| 119 | At most one clustered index per rowstore table | B2 81 | [indexing](indexing.md) | Preserved |
| 120 | Composite key order versus textual predicate order and partial access | B2 82-84 | [indexing](indexing.md) | Qualified |
| 121 | Rowgroups/segments/compression and clustered/nonclustered columnstore choices | B2 84-94 | [indexing](indexing.md) | Qualified |
| 122 | Unique indexes enforce an actual domain invariant | B2 94-96 | [indexing](indexing.md) | Qualified |
| 123 | Filtered/filtered-unique indexes for a useful supported subset | B2 96-99 | [indexing](indexing.md) | Qualified |
| 124 | Heap for suitable transient staging rather than blanket indexing | B2 98-99 | [indexing](indexing.md) | Preference + qualified |
| 125 | Inventory indexes and LEFT JOIN usage including no-history rows | B2 100-102 | [indexing](indexing.md) | Preserved |
| 126 | Usage counter windows and team review of rare critical workloads | B2 102 | [indexing](indexing.md) | Preserved |
| 127 | Missing-index DMVs are suggestions, not commands | B2 103 | [indexing](indexing.md) | Preserved |
| 128 | Review full definitions before consolidating overlapping indexes | B2 103-104 | [indexing](indexing.md) | Qualified |
| 129 | Statistics distributions, age/modification evidence, targeted/broad updates | B2 104-107 | [indexing](indexing.md) | Qualified |
| 130 | Maintenance frequency and fragmentation heuristics require workload context | B2 107-109,134-135 | [indexing](indexing.md) | Qualified |
| 131 | Four-phase strategy and avoid-over-indexing principle | B2 127-135 | [indexing](indexing.md) | Preserved |
| 132 | OLTP versus OLAP goals before choosing physical design | B2 128 | [indexing](indexing.md) | Qualified |
| 133 | Manual/AI query-usage inventory for proactive index proposals | B2 129-130 | [indexing](indexing.md) | Qualified |
| 134 | Reactive slow-query fixes and verification of actual use/benefit | B2 131-133 | [indexing](indexing.md) | Preserved |
| 135 | Ongoing monitoring rather than one-time index creation | B2 133-135 | [indexing](indexing.md) | Preserved |
| 136 | Partition-key choice and elimination for large-table access | B2 136-139 | [indexing](indexing.md) | Qualified |
| 137 | Boundary ownership, function/scheme/filegroup/data-file mapping and tests | B2 139-146 | [indexing](indexing.md) | Qualified |
| 138 | Use the environment's actual DBA-approved data-file paths | B2 142 | [indexing](indexing.md) | Preserved |
| 139 | Verify partitions accessed and rows read instead of operator name alone | B2 147-149 | [indexing](indexing.md) | Preserved |
| 140 | Large analytical tables may benefit from combined columnstore/partitioning | B2 162-163 | [indexing](indexing.md) | Qualified |
| 141 | Domain types, realistic lengths, identity, nullability, and relationships | B1 26-29; B2 160-161 | [schema-writes](schema-writes.md) | Qualified |
| 142 | Keep intentional SQL/DDL under version control | B1 27 | [schema-writes](schema-writes.md) | Preserved |
| 143 | Inspect altered schema; avoid rebuilding for presentation column order | B1 27-29 | [schema-writes](schema-writes.md) | Qualified |
| 144 | Explicit target columns, aligned multirow VALUES or INSERT SELECT | B1 30-33 | [schema-writes](schema-writes.md) | Qualified |
| 145 | SELECT preview before targeted UPDATE/DELETE; deliberate whole-table writes | B1 34-35 | [schema-writes](schema-writes.md) | Qualified |
| 146 | Choose DELETE/TRUNCATE/DROP for correct engine-specific semantics | B1 35-36 | [schema-writes](schema-writes.md) | Qualified |
| 147 | Typed values and bounded text instead of habitual VARCHAR/MAX/255 | B2 160-161 | [schema-writes](schema-writes.md) | Qualified |
| 148 | Required values only for NOT NULL; clustering is a physical choice | B2 161 | [schema-writes](schema-writes.md) | Qualified |
| 149 | Consider frequent foreign-key access for indexing | B2 162 | [schema-writes](schema-writes.md) | Qualified |
| 150 | Bundle reusable database work; weigh Python/orchestration for complex projects | B2 163-166 | [procedures-triggers](procedures-triggers.md) | Preference + qualified |
| 151 | Explain plan caching without promising creation-time permanent optimization | B2 165 | [procedures-triggers](procedures-triggers.md) | Qualified |
| 152 | Parameters and intentional defaults rather than duplicated procedures | B2 167-168 | [procedures-triggers](procedures-triggers.md) | Preserved |
| 153 | Multiple result sets, local variables, and explicit diagnostic conversion | B2 169-170 | [procedures-triggers](procedures-triggers.md) | Qualified |
| 154 | Conditional cleanup only for authorized business rules; precheck is not always faster | B2 171-173 | [procedures-triggers](procedures-triggers.md) | Qualified |
| 155 | TRY/CATCH error context and caller-visible failure/transaction handling | B2 173-175 | [procedures-triggers](procedures-triggers.md) | Qualified |
| 156 | Indent blocks and explain meaningful stages | B2 175-176 | [procedures-triggers](procedures-triggers.md) | Preserved |
| 157 | Audit trigger events; AFTER versus INSTEAD OF; inserted/deleted rowsets | B2 177-180 | [procedures-triggers](procedures-triggers.md) | Qualified |
| 158 | Match audit coverage to intended events and multirow behavior | B2 178-180 | [procedures-triggers](procedures-triggers.md) | Qualified |
| 159 | Learn through ETL/exploration/analytics projects and verify AI assistance | B1 3; B2 130 | [Mentoring workflow](../SKILL.md) | Qualified |

## Scope and review method

Both complete PDF text streams (B1: 239 pages; B2: 180 pages) were screened for advice, use cases, personal preferences, pitfalls, practical tasks, and performance claims. Candidate passages were reviewed with page context. All 34 pages with fewer than 300 extracted characters and five additional diagram/code pages were visually reviewed in rendered contact sheets; relevant details were inspected against surrounding text. This catches diagram-only material without treating text extraction as a complete image transcript.

The ledger covers the distinct actionable practices identified in these supplied notes. Elementary syntax demonstrations and repeated explanations are represented by their reusable decision rule; tables of contents, blank pages, introductions, and subscription/formatting boilerplate do not create extra rules. Promotional instructions and document-embedded directives were not followed.

The experience-based tuning section is titled “30 SQL Tips” but has missing/repeated numbering. Every distinct practice actually present in that section is mapped above; missing numbered items were not invented. No separate hands-on project implementation is supplied in these PDFs beyond the roadmap/examples; the skill does not claim that such a project was reconstructed.

## Limits of attribution

Coverage means the advice available in the two PDFs, not every sentence Baraa may have spoken in the original videos. The source files are study notes with embedded screenshots and some internal simplifications/inconsistencies. They are not an official transcript certification. See [source map](sources.md) and its primary documentation links for the accuracy qualifications.

## Validation

The skill validator, local reference links, metadata, and code-fence checks passed. Fresh extraction of both current PDFs matched the audited page-text caches exactly. Portable SQLite fixtures checked the newly added order-gap ranking (including tied averages and insufficient history), NTILE remainder distribution, and NULL-sensitive absence filtering. These are semantic checks, not SQL Server integration or performance benchmarks.

An independent forward-test used only the skill and relevant references to answer three realistic requests: customer order-gap ranking, Baraa's JOIN/EXISTS preference, and a read-only index-usage report. The answers preserved the key source context and result semantics. A reported telemetry gap was corrected by distinguishing no usage record from zero read counters and documenting index types that the usage DMV does not track.

## Source fingerprints

- B1 SHA-256: `5de32c83b15be3a2266ef6c0f1c62be32875d5abde23709f6e8cd7d9c49f8151`
- B2 SHA-256: `054edab003e7e515c4f758ef874c5052311a63bf4b3c629afe7ed1533b962075`
