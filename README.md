# baraa_skills

Verified reusable skills inspired by Baraa's practical teaching and project experience.

## Available skills

| Skill | Purpose | Verification |
| --- | --- | --- |
| [Baraa SQL Mentor](skills/baraa-sql-mentor/SKILL.md) | Write, review, debug, optimize, and learn SQL with source-grounded senior mentoring | Skill validation, source coverage audit, semantic fixtures, and independent forward-testing |

The SQL mentor has nine categories: query design; joins and sets; data quality; windows; architecture; performance; indexing and partitioning; schema and data changes; procedures and triggers. It includes a [coverage ledger](skills/baraa-sql-mentor/references/coverage.md) with 159 source-to-skill mappings and a [source map](skills/baraa-sql-mentor/references/sources.md). These mappings are not a count of unique official tips. SQL Server integration/performance was not benchmarked.

## Use

Copy the complete `skills/baraa-sql-mentor` directory to your personal Codex skills directory, then invoke it by name:

```text
$baraa-sql-mentor review this query like my senior
$baraa-sql-mentor category: windows mode: teach
```

The references are part of the skill and must remain with SKILL.md.

## Future additions

After creating and verifying a new skill, ask Mohammad whether he wants it added here. Add it only after he agrees, unless his current request already explicitly authorizes that addition.

Keep each skill in its own folder under `skills/`, include its required references, and record the checks actually performed. Do not claim checks or performance results that were not run.

## Attribution

Baraa SQL Mentor is a synthesis of user-supplied bootcamp study notes, with clearly labeled technical qualifications and mentor extensions. It is not an official Baraa product. The original bootcamp PDFs and raw extraction files are not included.
