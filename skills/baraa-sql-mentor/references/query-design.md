# Query design

Bootcamp grounding: B1 pp. 9-25, 37-41, 114-124; B2 pp. 149-153, 159, 163. See [sources](sources.md) for B1/B2 and page numbering.

## Build from the business question

- Confirm the intended server/database before executing SQL, an explicit habit in B1 p.9; qualify object names to the needed scope. Do not issue USE on engines/services that require a separate database connection.
- Identify the source table and output grain. Begin with a small exploratory result, then select explicit columns. Keep SELECT * for limited inspection when appropriate, not a habitual report contract.
- Use readable aliases for calculated metrics. Indent column lists and logical blocks; specify sort direction. These are clarity preferences, subordinate to established project conventions.
- Teach logical processing as FROM/JOIN, WHERE, grouping/aggregation, HAVING, SELECT/window evaluation, DISTINCT, ORDER BY, row limiting. This explains visibility and filtering; it is not the physical execution plan.
- Filter individual rows with WHERE and aggregated groups with HAVING. Excluding low-scoring customers before totaling country scores differs from excluding low-total countries afterward.
- Choose GROUP BY when the result should collapse to one row per group; choose a window when original row detail must remain. Adding a grouping column changes the grain, even if it fixes a syntax error.
- Do not add DISTINCT to hide a broken join. Identify why rows repeat and what uniqueness means. An ID column is unique only if the schema or data guarantees it.
- Use TOP/LIMIT for exploration; add ORDER BY for meaningful highest/latest results. Add a tie-breaker if exactly N repeatable rows are required.

## Conditional metrics

Use CASE for meaningful categories, mappings, flags, and conditional aggregation. Put overlapping conditions in the intended priority order and choose ELSE deliberately. Missing values should not silently become a real category such as 'Low'.

```sql
-- T-SQL. Assumes one row per order and decimal Sales.
SELECT
    o.CustomerID,
    COUNT(*) AS OrderCount,
    SUM(CASE WHEN o.Status = 'Delivered' THEN 1 ELSE 0 END) AS DeliveredCount,
    SUM(o.Sales) AS TotalSales
FROM Sales.Orders AS o
WHERE o.OrderDate >= @StartDate
  AND o.OrderDate < @EndDate
GROUP BY o.CustomerID
HAVING SUM(o.Sales) > @MinimumSales;
```

Mentor extensions: parenthesize mixed AND/OR logic; bind external inputs through the host's parameter mechanism. CASE is not a universal guarantee against expression-evaluation errors. HAVING can also operate on an implicit aggregate group without explicit GROUP BY; the introductory notes oversimplify this.

## Mapping and aggregate details from the course

B1 pp.121-124: profile actual distinct codes before writing a CASE mapping so unexpected values are visible. Simple CASE compares one expression by equality; searched CASE supports ranges and multiple conditions. Explicitly decide how NULL, unmapped values, and exact threshold boundaries are classified. Technical caveat: result branches need compatible types, not literally identical declared types; SQL Server resolves a result type by precedence and implicit conversion can fail. Use explicit casts where needed. See [Microsoft CASE semantics](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/case-transact-sql?view=sql-server-ver17).

B1 p.146: Baraa finds little practical performance difference between COUNT(*) and COUNT(1); do not spend tuning effort on this cosmetic change. Both count rows because 1 is non-NULL; neither counts distinct business entities. Technical correction to B1 pp.144,173: SUM/AVG require appropriate numeric inputs, but MIN/MAX can also compare supported text/date types; text order follows collation. See [Microsoft MIN](https://learn.microsoft.com/en-us/sql/t-sql/functions/min-transact-sql?view=sql-server-ver17).

## Verify

Check expected groups, reconcile a sample total with the source, and test boundaries and missing values. If counts change after a join, inspect joins-sets before accepting the report.
