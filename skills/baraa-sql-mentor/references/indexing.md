# Indexing, statistics, and partitioning

Bootcamp grounding: B2 pp.70-109, 127-149, 160-163.

## Baraa's four-phase strategy

1. **Understand the workload.** For OLTP, weigh small lookups and write overhead; for OLAP, weigh broad scans and aggregations. Baraa favors restrained rowstore indexing for transactions and considering columnstore for large analytical fact tables.
2. **Study usage patterns.** Review actual query filters, joins, grouping, ordering, and selected columns. Identify repeated access patterns before proposing indexes.
3. **Fix demonstrated bottlenecks.** Analyze a slow query, propose an appropriate index, then verify its actual use and measured benefit.
4. **Monitor and maintain.** Review usage, missing/overlapping candidates, statistics, and physical health. Revisit the workload as it changes.

His central rule: avoid over-indexing. Every index consumes space and adds maintenance to writes. Present candidates with their target queries, expected benefit, cost, and validation plan; do not automatically execute schema changes during a review.

## Select an index for the access pattern

- Rowstore clustered key: usually narrow, stable, and unique when feasible. A primary key is often a good candidate, but logical identity and physical clustering are separate choices. Warehouses, columnstore designs, and existing access patterns can justify another layout.
- Nonclustered: repeated selective filters, joins, or ordering. Inspect existing definitions before adding another. Frequently used foreign keys may benefit; SQL Server does not automatically index every FK.
- Composite: key column order matters for useful seek prefixes and ordering; reason from equality/range predicates and workload, not a universal “most selective first” slogan. Include projected columns where a narrower covering index justifies its write/storage cost.
- Filtered: a stable, useful subset such as active records. Confirm query predicates can use the filter and that it is selective enough.
- Unique: enforce a real business invariant after inspecting existing duplicates, not simply to speed up a query.
- Columnstore: consider for large analytical scans, aggregation, and compression. Evaluate loading, update frequency, rowgroups, and point-lookup needs; it is not a universal replacement for rowstore.

Technical correction to B2 pp.83-84: the order of AND predicates in query text does not have to match index key order. An index on (Country, Score) does not stop being useful merely because Score is written first in WHERE. Skipped key columns can limit seek depth, but do not make every use of the index impossible. Verify actual access paths. See [Microsoft index design guide](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-index-design-guide?view=sql-server-ver17).

## Maintenance with context

Baraa points to sys.dm_db_index_usage_stats, missing-index DMVs, sys.stats with sys.dm_db_stats_properties, and sys.dm_db_index_physical_stats.

- Usage counters can reset on restart and other lifecycle events. Capture the observation window and include rare/month-end workloads before labeling an index unused. Do not drop an index supporting a constraint because its read counter is zero.
- Missing-index suggestions are candidates, not commands. Compare overlap, key/include order, write cost, and overall workload. Duplicate index checks must compare complete definitions, filters, included columns, uniqueness, and purpose, not just a repeated column name.
- Baraa suggests periodic statistics review and extra attention after major loads. Adjust scope/timing to modification rates, existing automatic settings, and measured estimation problems; do not impose a weekly full-database update everywhere.
- His fragmentation ranges (0-10% none, >10-30% reorganize, >30% rebuild) are course heuristics. Mentor extension: assess page density, index size, storage, actual workload impact, and operational cost. Do not rebuild on a percentage alone or assume an online operation has no blocking/resource effects. See [Microsoft index maintenance guidance](https://learn.microsoft.com/en-us/sql/relational-databases/indexes/reorganize-and-rebuild-indexes?view=sql-server-ver16).

## Partition very large tables for a reason

Choose a partition key aligned with filtering or data lifecycle. Define boundaries carefully (RANGE LEFT versus RIGHT), map them through the partition scheme, and verify row placement and partitions accessed. For timestamp years, midnight at the next year's start with suitable RANGE RIGHT boundaries is often clearer than year-end midnight.

Baraa demonstrates partition elimination and combining partitions with analytical storage. Mentor extension: partitioning does not guarantee faster queries or writes and does not automatically distribute SQL Server data across servers. It often helps lifecycle management; performance depends on elimination and access paths. Do not invent filegroup paths: use the actual environment/DBA-supplied storage plan.

## A usage report that includes unused indexes

B2 pp.100-102: begin with the catalog of existing indexes and LEFT JOIN usage counters, so indexes with no usage row remain visible. Use the complete database/object/index identity in the join; a DMV-only report misses the very indexes under investigation.

```sql
-- SQL Server; requires metadata/DMV permissions appropriate to the version.
SELECT
    s.name AS SchemaName,
    t.name AS TableName,
    i.name AS IndexName,
    i.type_desc AS IndexType,
    i.is_primary_key,
    i.is_unique_constraint,
    i.is_disabled,
    t.is_memory_optimized,
    CASE WHEN u.index_id IS NULL THEN 0 ELSE 1 END AS HasUsageRecord,
    COALESCE(u.user_seeks, 0) AS UserSeeks,
    COALESCE(u.user_scans, 0) AS UserScans,
    COALESCE(u.user_lookups, 0) AS UserLookups,
    COALESCE(u.user_updates, 0) AS UserUpdates,
    u.last_user_seek,
    u.last_user_scan
FROM sys.tables AS t
INNER JOIN sys.schemas AS s ON s.schema_id = t.schema_id
INNER JOIN sys.indexes AS i ON i.object_id = t.object_id
LEFT JOIN sys.dm_db_index_usage_stats AS u
    ON u.database_id = DB_ID()
   AND u.object_id = i.object_id
   AND u.index_id = i.index_id
WHERE i.index_id > 0
  AND i.is_hypothetical = 0
ORDER BY s.name ASC, t.name ASC, i.name ASC;
```

Zeros here mean no observed usage in the available counter window, not proof of permanent uselessness. Baraa explicitly says to check with the team about infrequent critical/month-end queries before dropping a candidate. This report creates a review list; it is not an automated DROP script.

Mentor extension from forward-testing: HasUsageRecord distinguishes no DMV row from a recorded row with zero read counters. This DMV does not track memory-optimized or spatial indexes; their missing rows must not be labeled unused. Use appropriate telemetry such as sys.dm_db_xtp_index_stats for memory-optimized indexes. UserUpdates counts operations, not affected rows. See [Microsoft index-usage DMV](https://learn.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-objects/sys-dm-db-index-usage-stats-transact-sql?view=sql-server-ver17).

## Further index choices from the notes

B2 pp.70-99: a rowstore table has at most one clustered index, with data at its leaf level, while nonclustered indexes locate rows through a heap RID or clustered key. An index's logical order does not imply guaranteed query output order. Baraa includes heaps for quick transient staging when read optimization is unnecessary; “no indexes is always the fastest write design” remains workload-dependent. Do not add an index to every staging copy by habit.

For columnstore, explain rowgroups, column segments, and compression when these affect analytical I/O or loading. A clustered columnstore covers the table rather than taking a normal column-list argument; a nonclustered columnstore can target supported columns while retaining rowstore storage. Compare compression/storage as well as runtime rather than treating the course's sample compression ratio as a guarantee.

B2 pp.94-99: a unique index enforces the chosen uniqueness rule; a filtered unique rowstore index can enforce uniqueness only within a subset when that is the actual requirement. The ordinary filtered-index examples use nonclustered rowstore indexes, not a filtered clustered index or a direct conversion to filtered columnstore. Verify NULL, collation, datatype, and engine-version restrictions for the actual design rather than assuming email/product-name uniqueness universally.

B2 pp.104-107: inspect statistics last_updated, row population at collection, and modification indicators together with cardinality-estimation evidence. Statistics can exist on indexed and unindexed columns. A table/index age alone does not prove stale estimates; distinguish targeted UPDATE STATISTICS from a broader sp_updatestats proposal and account for the existing maintenance policy.

B2 pp.129-130: an AI-assisted inventory of supplied SQL can list tables/columns used in WHERE, JOIN, GROUP BY, and ordering to support the proactive phase. Count observed source usage, distinguish that from actual execution frequency, and inspect parser/alias ambiguity before trusting the inventory. Do not send project SQL to an external service without authorization.

## Partition setup verification

B2 pp.139-146: partition function defines boundaries; partition scheme maps partitions to filegroups; filegroups hold the actual data files. With N boundaries expect N+1 partitions, including outer ranges. Map by position, not by a filegroup name that merely looks like a year. The course's one-filegroup-per-partition layout is a design option, not a requirement: partitions may share a filegroup.

Before proposing the DDL, confirm partition-key type, left/right boundary ownership, storage path/capacity, and mapping. Test exact boundary values, first/last ranges, row counts, and catalog mapping. For performance tests inspect Actual Partitions Accessed and Rows Read as well as the scan operator's name; a partitioned scan can be efficient if it reads only the intended partitions. Consider columnstore plus partitioning for the demonstrated large analytical workload, with measured loading and query results.
