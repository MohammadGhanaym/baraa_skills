# Schema design and data changes

Bootcamp grounding: B1 pp.26-36; B2 pp.160-163.

## Design for meaning

- Use types that represent the domain: dates as dates, numeric scores as numbers, bounded text where justified. Analyze realistic maximum lengths and future requirements instead of copying VARCHAR(MAX) or 255 everywhere.
- Mentor extension: choose decimal precision/scale for exact quantities and appropriate Unicode/collation behavior for text. Do not shorten columns based only on a small current sample.
- Define a primary/business identity and relationship constraints. Baraa favors a clustered primary key in ordinary SQL Server rowstore designs; this is a default candidate, not an invariant for every table. See indexing for physical layout decisions.
- Apply NOT NULL where the business requires a value; optional/unknown values remain nullable. Do not invent dummy values solely to satisfy a constraint or an assumed performance benefit.
- Keep DDL scripts under version control, a practice explicitly emphasized in B1 p.27. Specify schema and object names clearly.

## Make writes reviewable

Baraa's practical habit is to check the target with SELECT before UPDATE or DELETE. Inspect the same predicate and relevant identifiers/count before a targeted change. Omitting WHERE affects all rows; permit that only when the user's task actually calls for the whole table.

For INSERT, list target columns explicitly and align values or SELECT expressions by position and type. Do not rely on physical column order, silently swapped values, or implicit conversions. Explain constraints/defaults when omitted columns matter.

Mentor extensions: use a transaction when atomicity/rollback is required and supported; define transaction ownership rather than blindly committing someone else's transaction. For consequential writes, record expected affected rows and inspect the actual outcome. A preview is useful but does not freeze a concurrently changing database; use appropriate concurrency protection when exact targeting matters.

Generate the requested SQL without treating code review, a report request, or a teaching example as permission to execute mutations. Do not overwrite stored unknown values merely to improve report display. Do not drop/recreate a table just to change column display order; explicit SELECT order normally addresses presentation.

## Distinguish deletion operations

DELETE removes selected rows; TRUNCATE empties a table under engine-specific restrictions; DROP removes the object. Explain the actual target engine's transaction, identity, FK, and trigger implications when choosing among them. Do not universalize the notes' speed comparison or substitute TRUNCATE when row-level semantics matter.

## Practical details from the introductory exercises

B1 pp.26-29: inspect created/altered objects and generate their DDL from the client/catalog when useful, then keep the intentional script in version control. The exercise's required phone/email fields are example-domain choices, not a rule that every person must supply them. Mentor extension: adding NOT NULL to a populated table needs a valid default/backfill or staged migration where existing rows would otherwise violate it. Dropping a column destroys its stored data and may affect dependencies; changing presentation order does not justify rebuilding the table.

B1 pp.30-36: use one multirow VALUES insert for a small explicit batch, or INSERT ... SELECT for data already in a database; inspect the source projection before loading. An omitted column may use a default or NULL only if its definition permits that, and identity/computed columns need their own supported handling. Match source/target counts, meaning, types, and order even if SQL accepts both values as text. Do not promote the teaching shortcut of omitting all target column names into production guidance.
