# Stored procedures and triggers

Bootcamp grounding: B2 pp.163-180.

## Put reusable logic in the right place

Baraa uses procedures to bundle related database steps and reduce repeated client commands. Parameterize a reusable report instead of maintaining nearly identical country-specific procedures. Use appropriate parameter types, explicit result columns, and deliberate defaults. Separate input parameters from local variables.

His stated preference: complex modern data projects often become easier to maintain with Python/orchestration than large stored procedures; small or database-only projects can suit procedures well. Treat this as an architectural tradeoff, not a ban on stored procedures. Do not migrate the user's platform unasked. SQL in stored procedures can also be version controlled.

Technical caveat: procedure plan compilation/caching depends on execution and engine behavior; creation is not a promise of a permanently optimal precompiled plan. Parameter-sensitive workloads can need investigation.

## Readable, observable procedures

- Indent BEGIN/END blocks, terminate statements consistently, and explain meaningful stages with comments. Do not use comments to narrate every obvious expression.
- IF/ELSE and EXISTS can control work, but the notes' NULL-score-to-zero update is valid only if the business authorizes changing those stored values. A read-only report can use an expression without a persistent cleanup.
- Use TRY/CATCH to handle failures; record useful error context. Mentor extension: printing an error alone can hide failure from callers. For real transactional work define rollback behavior and rethrow with THROW when failure should propagate. Account for an existing caller transaction; do not indiscriminately roll it back or commit it.
- Use return/result mechanisms appropriate to callers. PRINT is diagnostic text, not a substitute for a stable data contract.

## Triggers are automatic side effects

Baraa illustrates an AFTER INSERT trigger writing employee changes to an audit table. Distinguish AFTER from INSTEAD OF behavior and attach the trigger to the requested event. Be explicit about what the audit captures; an INSERT-only trigger does not audit updates or deletes.

Mentor extensions: SQL Server DML triggers fire per statement and inserted/deleted can contain many rows. Write set-based INSERT ... SELECT from those tables, not scalar variables that silently capture one row. INSERT has inserted rows, DELETE has deleted rows, and UPDATE has both. Define the stable key for pairing old/new versions, and test multirow and zero-row operations as well as one row. See [Microsoft multirow trigger guidance](https://learn.microsoft.com/en-us/sql/relational-databases/triggers/create-dml-triggers-to-handle-multiple-rows-of-data?view=sql-server-ver17).

Keep triggers small and explain latency, transaction/failure effects, and recursion risks when relevant. A transactional trigger audit does not automatically provide tamper resistance or records of rolled-back attempts. Do not install a trigger during an ordinary query review.

## Additional procedure details from the walkthrough

B2 pp.167-176: define parameters once and pass explicit named values when clarity helps; use a default only for a deliberate common case. Declare variables with types matching the aggregates and understand that assigning an aggregate to a variable is different from naming a returned column. A procedure with several SELECT statements can return several result sets; callers must expect that contract rather than silently reading only the first.

Use explicit character lengths when converting numeric diagnostics for PRINT/concatenation, or a supported concatenation function with deliberate NULL behavior. In CATCH, useful SQL Server error context includes ERROR_NUMBER(), ERROR_MESSAGE(), ERROR_LINE(), and ERROR_PROCEDURE(); preserve the original failure for callers when appropriate. Do not treat TRY/CATCH as catching every possible SQL Server error class.

The course's IF EXISTS cleanup can skip a no-op update in some situations, but an existence precheck is not automatically faster than a single `UPDATE ... WHERE ... IS NULL`, and can race with concurrent changes. Prefer the simpler statement when it fulfills the authorized write and required atomic behavior. This qualification supplements B2 pp.171-173 rather than attributing an unmeasured performance guarantee to Baraa.
