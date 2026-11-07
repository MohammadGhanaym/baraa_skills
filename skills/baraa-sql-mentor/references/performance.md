# Evidence-led query tuning

Bootcamp grounding: B2 pp.109-127, 149-163. For indexes load [indexing](indexing.md); for rewrites involving sets load [joins-sets](joins-sets.md).

## Baraa's guiding philosophy

Keep readable SQL unless a real bottleneck justifies change. Compare plans before and after; if alternatives behave alike, prefer the clearer version. Large tables make costs easier to observe, but row count alone does not determine which syntax wins.

For a concrete slow query, establish dialect/version, parameters, result requirements, approximate sizes, existing indexes, and available measurements from the artifacts at hand. Request only missing evidence needed for the diagnosis; an offline review can still give labeled hypotheses.

## Diagnose before prescribing

- Inspect actual plans where safely available. They require execution; do not run a mutating statement merely to collect one. Estimated plans can support an initial review but lack actual row/runtime observations.
- In SSMS follow data flow generally right to left; inspect operator properties rather than only graphical percentages. Compare estimated and actual row counts, rows read versus returned, repeated lookups, sorts, joins, and warnings. Mentor extensions: inspect spills, memory grants, and parameter skew where relevant.
- Capture logical reads, CPU and elapsed time under representative parameters and load. In T-SQL, STATISTICS IO/TIME can support permitted test runs. Estimated cost percentages are not elapsed-time benchmarks.
- Check one proposed change at a time. Verify row values and multiplicities, then compare measured resource use. Preserve useful filtering, required ordering, ties, and freshness.

## Candidate improvements

- Project needed columns; remove unnecessary DISTINCT and sorting. Do not remove output ordering required by the consumer.
- Use bounded exploration. Favor searchable predicates on raw typed columns, avoiding unnecessary functions/conversions on the indexed side. Convert parameters to matching types when appropriate.
- Rewrite year filtering into a half-open date range; use prefix LIKE only when prefix matching satisfies the requirement. Changing contains/suffix search into prefix search changes the question.
- IN can improve readability over repeated equality ORs; do not promise a different plan. Functions and leading wildcards often prevent ordinary index seeks, but “no index can ever be used” is too broad. Computed indexes and other access paths can change the options.
- Consider filtering/aggregation before joins when it reduces work and preserves grain. CTE syntax does not force execution order or materialization. The optimizer may already push filters down.
- EXISTS can express existence without multiplying rows. Correlated subqueries are not categorically slow: inspect the physical plan before replacing them.
- Consider window functions when repeated scans or repeated totals obscure a report, but account for their sorting/memory costs. Reusing a CTE expression does not itself prove a single physical scan.
- Reuse precomputed summaries for repeated slow reporting only when refresh/staleness requirements permit; see architecture.

## Do not optimize by folklore

- Scans can be appropriate for large fractions of a table; a seek with many lookups may cost more. There is no universal seek > index scan > table scan rule for whole-query efficiency.
- Nested loops can be appropriate even with a large inner table and selective indexed probes. Table size alone does not justify forcing a hash join.
- UNION ALL, UNION, OR rewrites, explicit prefiltering, and DISTINCT combinations need semantic equivalence and measurement. Do not guarantee improvement from syntax alone.
- Baraa treats hints as temporary workarounds while investigating statistics/indexing/root causes. Keep any proposed hint tied to evidence, representative parameters, and a removal/review condition. Do not add hints automatically.

Mentor extension: establish correctness with edge cases (NULL, duplicate, tie, empty input, date boundary), and compare duplicate counts as well as set differences when order is irrelevant. Without access to the target engine, say that tuning remains unmeasured. See [Microsoft query processing architecture](https://learn.microsoft.com/en-us/sql/relational-databases/query-processing-architecture-guide?view=sql-server-ver16).

## Concrete lessons from Baraa's plan walkthroughs

B2 pp.114-120: a matching index order can avoid an explicit Sort, but an index is never a replacement for final ORDER BY in the query contract. A seek followed by a Key Lookup can indicate that requested columns are absent from the nonclustered access path. First remove genuinely unneeded projection columns; if all are required, evaluate whether a covering index is worth the storage/write cost. Do not remove required data merely to make a plan look simpler.

B2 pp.119-127: nested loops, hash match, and merge join are physical algorithms, distinct from logical INNER/LEFT joins. Merge can exploit compatible sorted inputs; hash has build/probe and memory costs; loops can exploit selective indexed probes. Save a baseline actual plan and use SSMS Save Execution Plan As / Compare Showplan for side-by-side comparison. Actual plans add runtime observations to the chosen plan, not necessarily a different physical strategy than the estimate; discrepancies in row estimates have several possible causes beyond stale statistics.

The source illustrates join hints, forcing a particular index, and FORCESEEK. Apply the same evidence-and-temporary-workaround rule to each; an impossible forced access path can fail compilation. A development improvement needs representative production-like parameters/data, not an assumption that the hint transfers.

B2 p.159's redundant-logic example classifies salaries above/below average with repeated UNION ALL branches. A window baseline plus CASE can express this once:

```sql
WITH Compared AS (
    SELECT
        EmployeeID,
        Salary,
        AVG(CAST(Salary AS decimal(19, 4))) OVER () AS AverageSalary
    FROM Sales.Employees
)
SELECT
    EmployeeID,
    CASE
        WHEN Salary IS NULL OR AverageSalary IS NULL THEN 'Unknown'
        WHEN Salary > AverageSalary THEN 'Above Average'
        WHEN Salary < AverageSalary THEN 'Below Average'
        ELSE 'Average'
    END AS SalaryBand
FROM Compared;
```

Mentor qualification: this complete classification intentionally includes equal and unknown rows, whereas original strict > / < branches omit them. If equivalence is required, outer-filter to those same comparisons. Sharing an expression in SQL text still does not prove exactly one physical scan: inspect the plan.
