# NULLs, strings, dates, and quality

Bootcamp grounding: B1 pp. 63-113, 145-152, 157-163.

## Missing values are a business decision

- Distinguish unknown NULL, known empty string, whitespace, and numeric zero. Agree on a policy before replacing them.
- Baraa's preference: normalize blank text to NULL in preparation/ETL and add readable defaults in reports. Apply only where blanks mean missing; retain meaningful whitespace or codes.
- `NULLIF(TRIM(category), '')` normalizes ordinary surrounding spaces and empty text; `COALESCE(..., 'Unknown')` adds a display label. TRIM does not remove every Unicode whitespace character by default.
- Prefer COALESCE for portable fallback logic. SQL Server ISNULL has different type/nullability semantics; do not claim it is always faster. Check lengths and types when mixing fallback expressions. See [Microsoft COALESCE documentation](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/coalesce-transact-sql?view=sql-server-ver16).
- COUNT(*) counts rows; COUNT(column) counts non-NULL values. SUM/AVG/MIN/MAX normally ignore NULL inputs. AVG of 15, 25, NULL is 20; adding a zero changes the denominator. In T-SQL cast integer inputs to decimal when a fractional average is required.
- `AVG(COALESCE(x, 0))` and `COALESCE(AVG(x), 0)` answer different questions. Do not replace stored unknowns with zero merely to fix a report.
- Guard zero denominators with NULLIF and use decimal arithmetic: `100.0 * Sales / NULLIF(TotalSales, 0)`. Decide whether resulting NULL is appropriate.
- For NULLs last in T-SQL: `ORDER BY CASE WHEN Score IS NULL THEN 1 ELSE 0 END ASC, Score ASC`. Avoid arbitrary high replacement scores.

## Clean with observable checks

Use TRIM, REPLACE, case normalization, or substring extraction for an identified defect. Inspect raw and cleaned values together and check whether distinct keys collapse. SQL Server LEN ignores trailing spaces; DATALENGTH measures bytes including trailing spaces, not characters universally. The notes mix LENGTH/LEN terminology: use the target engine's function.

B1 pp.65-79: compose transformations in the intended order (for example trim before LEFT/RIGHT extraction), and inspect the intermediate expression before adding another function. Baraa uses REPLACE to normalize phone punctuation and filename-extension text. Replacing '.txt' with '.csv' in a value only changes text; it does not convert a real file's content. Constrain replacements to the intended occurrence, such as the extension, when a global replacement could corrupt other text. For full-name output decide how missing name components and separators should appear rather than accidentally propagating NULL through concatenation.

Baraa prefers a direct raw-versus-TRIM comparison for whitespace detection (B1 pp.71-72). Technical caveat: SQL Server pads strings for most comparisons, so a trailing-space-only defect may compare equal. For variable-length text, compare `DATALENGTH(name)` with `DATALENGTH(TRIM(name))`; handle NULL separately and account for fixed-width CHAR padding. See [Microsoft string comparisons](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/string-comparison-assignment?view=sql-server-ver17). Choosing NULL over blank text is primarily a semantic consistency policy; do not claim it is universally the smallest or fastest storage representation.

## Dates and conversion

- Keep dates/numbers typed during filtering, joining, and aggregation. Format for display at the boundary. Month names alone merge years and may sort alphabetically; group by a year-month key or month-start date for multi-year metrics.
- Prefer half-open timestamp ranges. For all of 2025 in T-SQL: `OrderDate >= '20250101' AND OrderDate < '20260101'`. BETWEEN ending December 31 midnight misses the rest of that day.
- DATETRUNC is version-dependent. DAY/MONTH/YEAR, DATEPART, DATENAME, EOMONTH, DATEADD, and DATEDIFF serve distinct extraction/arithmetic/display needs. Do not replace a real period key with a formatted label.
- Mentor extensions: DATEDIFF counts datepart boundaries, not precise elapsed duration. Establish timezone and elapsed/calendar meaning. TRY_CONVERT/TRY_CAST can quarantine invalid text; conversion failures and original missingness may need separate flags.

## Validate before analysis

Compare COUNT(*) with COUNT(required_column), profile duplicate business keys, invalid dates, blank text, and out-of-range values. Window COUNT partitioned by a key retains duplicate rows for inspection. Return exceptions for investigation; do not automatically delete or overwrite source data.

B1 pp.108-109: NULLIF can normalize a documented sentinel (for example -1 meaning unknown) as well as protect division; do not turn legitimate negative values into missing data. B1 pp.149-151,195: exported/staged data may have lost source key constraints, so verify the declared business key in the model and actual staged data. COUNT(*) OVER(PARTITION BY key) > 1 identifies every row in a duplicate group; ROW_NUMBER > 1 identifies rows beyond the chosen survivor. Preserve the exception list so the source owner can fix the cause; sending it is a separate action requiring the user's instruction.

## Date-specific practices and qualifications

B1 pp.83-98: choose the function by the result needed: DAY/MONTH/YEAR or DATEPART for numeric parts, DATENAME for labels, DATETRUNC for period starts, EOMONTH for month ends. For a particular year's month, raw date ranges usually express filtering better than MONTH(column); “every February across all years” is a different requirement.

Use CAST for supported type changes, CONVERT for type changes with an explicit supported style, and FORMAT for presentation/culture-specific strings. Check actual tokens: `MM` is month and `mm` is minute in .NET-style FORMAT patterns; use `yyyy` and `dd` as appropriate. State text lengths, for example `CONVERT(varchar(10), OrderDate, 101)` for US mm/dd/yyyy or style 104 for dd.mm.yyyy. Technical correction to B1 pp.87-94: do not copy the notes' ISO '6801' typo, CONVERT style numbers 32/34, claim that every type converts to every other, or an assumed universal session date format. See [Microsoft conversions and styles](https://learn.microsoft.com/en-us/sql/t-sql/functions/cast-and-convert-transact-sql?view=sql-server-ver17).

B1 pp.96-98: DATEADD supports forward/backward interval calculations; DATEDIFF supports shipping duration and order gaps. Age measured by DATEDIFF(year, birthdate, as_of) alone can be one year too high before the birthday: adjust using the agreed birthday/leap-day rule if completed age is requested. Group average shipping days by a year-month key and cast integer day counts before AVG if fractional days are required.

The course uses ISDATE to inspect invalid date text. Technical caveat: it tests datetime compatibility, depends on language/dateformat, and is not a general datetime2 validator. Prefer TRY_CONVERT to the actual target type with the expected input style when validating typed ingestion. Keep invalid values distinguishable from genuine NULLs; do not silently substitute a remote future date. See [Microsoft ISDATE](https://learn.microsoft.com/en-us/sql/t-sql/functions/isdate-transact-sql?view=sql-server-ver17).
