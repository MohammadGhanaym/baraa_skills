# Window analytics

Bootcamp grounding: B1 pp. 125-227, especially p.138 (frame correction), pp.155-170 (contribution/rolling metrics), 187-202 (practical ranking), 211-227 (value functions).

## Translate the question into a window

Keep row detail with OVER; collapse rows with GROUP BY. Ask: what population belongs together (PARTITION BY), what defines sequence (ORDER BY), and what part contributes (frame)? Window ordering does not guarantee final output order.

| Question | Pattern |
| --- | --- |
| Contribution to whole/group | metric / NULLIF(SUM(metric) OVER (...), 0), with decimal arithmetic |
| Running total | SUM(...) OVER (... ORDER BY time, tie_breaker ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) |
| Last three observations | ... ROWS BETWEEN 2 PRECEDING AND CURRENT ROW |
| Top N, exactly N rows | ROW_NUMBER with deterministic tie-breaker, then outer filtering |
| Preserve ties | RANK (gaps) or DENSE_RANK (no gaps), ordered by business metric |
| Previous/next observation | LAG/LEAD after establishing input grain |
| Group min/max | MIN/MAX OVER (PARTITION BY ...) without unnecessary sorting |

Do not add a unique tie-breaker inside RANK/DENSE_RANK if business ties should share rank. ROW_NUMBER and row-based frames usually need deterministic ordering.

## Latest records and deduplication

Baraa emphasizes ROW_NUMBER for top/bottom analysis, inspecting duplicates, and selecting the latest version. Define the duplicate business key and winning rule first. A timestamp can tie; use a genuine stable version identifier where available.

```sql
-- T-SQL example: ArchiveRowID uniquely identifies each stored version.
WITH RankedOrders AS (
    SELECT
        a.OrderID,
        a.Status,
        a.CreationTime,
        ROW_NUMBER() OVER (
            PARTITION BY a.OrderID
            ORDER BY a.CreationTime DESC, a.ArchiveRowID DESC
        ) AS rn
    FROM Sales.OrdersArchive AS a
)
SELECT OrderID, Status, CreationTime
FROM RankedOrders
WHERE rn = 1;
```

This selects a result; it does not delete history. Inspect rn > 1 for exceptions. Mentor extension: analytical row numbers are not durable persisted keys. In SQL Server filter window results through a CTE/derived table; use QUALIFY only in supporting dialects.

## Frames and time comparisons

Technical correction to B1 p.138: SQL Server's applicable ordered-window default is RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW, not ROWS. RANGE includes peers sharing ordering values. Write explicit frames for intended behavior. For LAST_VALUE over a whole partition use `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`; the default ends at the current peer group. See [Microsoft OVER documentation](https://learn.microsoft.com/en-us/sql/t-sql/queries/select-over-clause-transact-sql?view=sql-server-ver17).

LAG/LEAD use row offsets. Aggregate to one row per month before computing monthly growth, then decide what missing months mean: previous observed month may not be previous calendar month. Add a calendar scaffold when needed. Use decimal arithmetic and NULLIF(previous_value, 0). Do not impute the first prior value as zero without a business rule.

NTILE supports segmentation and ETL batches, two uses Baraa highlights. It balances row counts, can split ties, and does not guarantee equal bytes or processing time. CUME_DIST/PERCENT_RANK describe relative positions, not shares of sales. Define population, direction, ties, and boundary semantics before implementing “top 20%”.

## Practical analytical recipes

- **Overall and multiple category totals (B1 pp.130-132,147-154):** combine OVER(), PARTITION BY ProductID, and PARTITION BY ProductID, Status in the same result when the business needs multiple comparison populations. Do not total the repeated window-total column again downstream.
- **Above/below average and extreme comparisons (B1 pp.160-164,173):** calculate AVG/MIN/MAX at the intended population, then outer-filter against the original metric or show `metric - minimum` / `maximum - metric`. Equality to MAX retains all tied maxima. The notes call above/below-average inspection “outlier detection”; mentor qualification: crossing the mean alone is not a statistical anomaly definition.
- **Business-level contribution / ranking (B1 pp.155-156,188-190):** aggregate orders to one row per customer/product before ranking entities or measuring entity shares. Ranking raw orders answers a different question. For grouped ranking, `RANK() OVER (ORDER BY SUM(Sales) DESC)` is legal after GROUP BY CustomerID; the window sees grouped rows.
- **Running versus finite and forward windows (B1 pp.164-172):** cumulative uses UNBOUNDED PRECEDING to CURRENT ROW; trailing three observations uses 2 PRECEDING to CURRENT ROW; current plus next uses CURRENT ROW to 1 FOLLOWING; centered three uses 1 PRECEDING to 1 FOLLOWING. Write full BETWEEN syntax for clarity. A three-row frame is only three months when input has one row for every relevant month. Forward windows require future observations and need an explicit business use before being used in historical evaluation.
- **First/last value versus extreme metric (B1 pp.221-226):** MIN/MAX finds extreme metric values; FIRST_VALUE/LAST_VALUE retrieves a value from the first/last row in a defined ordering. To return a product/name associated with an extreme, order/select the associated row with the requested tie rule. Reverse-order FIRST_VALUE can express the terminal value; choose a complete ordering and deliberate NULL behavior.

## Customer order-gap / retention recipe

Baraa's workflow (B1 pp.217-220): select orders, LEAD(OrderDate) within each CustomerID ordered chronologically, calculate DATEDIFF(day, OrderDate, NextOrderDate), average gaps per customer, and rank the averages ascending to study repeat-purchase behavior.

Mentor qualifications: use OrderID as a stable tie-breaker where dates tie, preserve fractional averages with a cast, and retain the valid first/last missing gap rather than replacing it with zero. A customer with one order has insufficient gap history; filter such customers from the ranking or list them separately. Prefer an explicit missingness sort flag over the course's arbitrary 99999 sentinel. The observation window and sparse history affect interpretation; small mean order gaps describe purchase frequency, not proof of loyalty or a complete cohort retention metric.

## ETL batches and temporary identifiers

B1 pp.190-192: ROW_NUMBER can label an extracted result for pagination/export, or be stored once with a snapshot. The same row number can change after a rerun, insert, or tied sort; do not recompute it as a cross-load foreign key. Use persistent key generation when durable identity is required.

Baraa normally orders NTILE export buckets by the primary key for systematic batches (B1 pp.200-202). Materialize the batch assignment or use a consistent snapshot when exporting in separate calls so changing inputs do not reassign rows mid-load. Mentor extension: reassemble disjoint batches with UNION ALL when duplicates must be preserved; the notes' informal UNION wording must not silently remove source rows. Segment spenders by an entity-level total when the requirement is customer segments rather than individual order segments.

B1 pp.202-209: CUME_DIST is the count of rows up to the current peer group in the window ordering divided by N; PERCENT_RANK is (RANK - 1)/(N - 1), returning 0 for a single-row partition in SQL Server. Ties use the same relative result; NTILE can split tied metrics. These rankings are discrete observed positions, not an infinite set of output percentages.

## SQL Server window rules

B1 pp.133-142,177,210-212: ranking/value functions require window ORDER BY; PARTITION BY can have multiple columns or be omitted. ROW_NUMBER/RANK/DENSE_RANK/CUME_DIST/PERCENT_RANK take no expression argument; NTILE requires the bucket count; LAG/LEAD accept a value and optional offset/default. Ranking and LAG/LEAD do not accept frame clauses in T-SQL.

Window functions belong in SELECT or ORDER BY, not the same level's WHERE/GROUP BY/HAVING. Do not directly nest a window inside another window or aggregate; stage the intermediate result. A grouped aggregate used as input to a later window, such as SUM(SUM(Sales)) OVER(), is different and can be valid. Filtering before calculating a window changes its population: compute first and outer-filter when the requested baseline includes rows that will be hidden later. Correct B1 p.141's “same columns” shorthand by reasoning about valid expressions in the grouped result, not textual identity.
