# Joins, existence, and sets

Bootcamp grounding: B1 pp. 42-62; B2 pp. 12-23, 153-159.

## Choose preserved rows first

Baraa's stated preference: start analytics from the master/fact table and enrich it through LEFT JOINs; prefer LEFT over RIGHT by swapping sides. Apply this when unmatched master rows belong in the report. Use INNER when only matches belong; FULL for reconciliation of both sides; CROSS for an intentional combination of every row.

- Inspect schema/ER relationships instead of guessing similarly named IDs. Use explicit join types, clear aliases, and qualified columns.
- Add joins one at a time. Check row counts, unmatched keys, and right-key uniqueness. LEFT JOIN preserves left rows but can multiply them when the relationship is one-to-many.
- Put right-table filters in ON to restrict enrichment while preserving left rows, or WHERE to filter final rows. A WHERE comparison against the right side commonly removes NULL-extended rows.
- Use EXISTS when the question is whether a related record exists, rather than joining and deduplicating. SELECT 1 communicates intent; it has no special speed advantage over SELECT * inside EXISTS.
- Use NOT EXISTS for absence, especially when nullable values make NOT IN unsafe. A LEFT JOIN anti-join also works if its IS NULL test uses a right-side column that cannot be NULL in a genuine match.

```sql
SELECT c.CustomerID, c.FirstName
FROM Sales.Customers AS c
WHERE NOT EXISTS (
    SELECT 1
    FROM Sales.Orders AS o
    WHERE o.CustomerID = c.CustomerID
);
```

Mentor extensions: check both sides' grain before joining facts; aggregate child data to the intended join grain where appropriate. Never replace NULL join keys with a shared empty string or zero merely to match them: this can cause false matches and many-to-many expansion. NULL-equal matching needs a business rule.

## Stack rows deliberately

- UNION ALL appends when duplicates are allowed or ruled out by guarantees. UNION removes duplicates across the entire projected row, not just an ID.
- Align column count, order, compatible types, and meaning in every branch. Use explicit columns. A source label preserves lineage.
- Use EXCEPT for directional differences and INTERSECT for overlap. Migration checks need both directions and duplicate multiplicity checks; set difference alone cannot prove complete equality.
- Baraa uses EXCEPT to detect new pipeline records. Comparing IDs detects new IDs, not changed attributes; choose the projection that matches the requirement.
- B1 pp.56-58: the first branch supplies output names; put final ORDER BY after the combined result. Branch-local row limiting/order requires a valid dialect-specific subquery shape and does not establish final output ordering.

## Additional practical patterns

- B1 pp.48-50: reconciliation can need both customers without orders and orphan orders without customers. Inspect unmatched rows from both directions, using two NOT EXISTS queries or a properly keyed FULL OUTER JOIN with unmatched tests.
- B1 p.51: CROSS JOIN supports product/color combinations and test-data grids; estimate the product of input row counts before building one. Do not use it to compensate for an unknown relationship.
- B2 pp.16-23,158-159: Baraa favors readable JOINs for small/medium datasets and notes EXISTS can help on very large tables when only existence matters. Technical qualification: JOIN is an equivalent filter only if matching multiplicity is acceptable or the other key is unique. IN is also a clear membership choice; a positive IN subquery tolerates duplicate listed keys without duplicating the source row. NOT IN has different NULL behavior from NOT EXISTS. Size alone does not decide performance.
- B2 pp.17-18: use ANY for at least one qualifying comparison and ALL for every comparison. Mentor extension: test empty and NULL-containing subquery results before rewriting to MIN/MAX: ALL over an empty set is true and ANY is false, while aggregates on an empty set yield NULL. A logically correlated expression does not guarantee row-by-row physical execution.

## Rewrite caveats

B2 p.156 prose recommends UNION ALL for an OR join, while its screenshot uses UNION. Neither is automatically equivalent: UNION ALL can duplicate a pair satisfying both conditions; UNION can collapse distinct source pairs with identical projected values. Establish pair identity and overlap first. DISTINCT over UNION ALL is not inherently faster than UNION. Preserve multiplicity and benchmark.

There is no universal INNER/LEFT/FULL speed ranking. Choose correct preservation semantics, then tune with the actual plan.
