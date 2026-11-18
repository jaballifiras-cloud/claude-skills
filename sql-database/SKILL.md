---
name: sql-database
description: Design, query, optimise, and migrate relational databases (PostgreSQL, MySQL/MariaDB, SQL Server, SQLite) — schemas, constraints, indexes, slow queries, migrations, reporting queries. Use when the user works with SQL, database schemas, migrations, or slow queries, or says "قاعدة البيانات" / "استعلام SQL".
---

# SQL and databases

## Safety first
- Identify the database engine and version before writing SQL (syntax differs).
- Read-only by default. For `UPDATE`/`DELETE`/`ALTER`/`DROP` on real data: show the statement, show how many rows it affects (`SELECT COUNT(*)` with the same `WHERE`), require a backup, wrap in a transaction, and get the user's explicit approval. Never run destructive statements on production on your own.
- Always use parameterised queries in application code.

## Schema design
- Primary keys on every table; foreign keys with appropriate `ON DELETE` behaviour (usually `RESTRICT` for financial data).
- `NOT NULL`, `UNIQUE`, and `CHECK` constraints to enforce business rules in the database (e.g. `CHECK (debit >= 0 AND credit >= 0)`).
- Money: `NUMERIC(18,3)` / `DECIMAL` — never `FLOAT`/`REAL`. Timestamps: `timestamptz` (PostgreSQL) / UTC.
- Normalise to avoid update anomalies; denormalise only deliberately (and keep it in sync transactionally).
- Add `created_at`, `updated_at`, `created_by` for auditability; consider history/audit tables for critical data.

## Performance
1. Get the plan: `EXPLAIN (ANALYZE, BUFFERS)` (PostgreSQL), `EXPLAIN ANALYZE` (MySQL 8+), actual execution plan (SQL Server).
2. Look for sequential scans on large tables, bad row estimates, sorts spilling to disk, nested loops over many rows.
3. Index the columns used in `WHERE`, `JOIN`, and `ORDER BY`; composite index column order = equality columns first, then range. Avoid functions on indexed columns in `WHERE` (`WHERE date(created_at) = ...` → use a range).
4. Avoid `SELECT *`, N+1 queries from ORMs (use joins/eager loading), `OFFSET` pagination on big tables (use keyset pagination), `NOT IN` with NULLs (use `NOT EXISTS`).
5. Re-measure after each change.

## Reporting queries
- Aggregate **before** joining one-to-many tables, or you will double-count (`SUM` over a join that duplicates rows).
- Date ranges: `>= start AND < next_day` instead of `BETWEEN` with timestamps.
- Use window functions for running balances: `SUM(amount) OVER (PARTITION BY account_id ORDER BY posting_date, id)`.

## Migrations
- Use the project's migration tool (Alembic, Django, Prisma, Knex, Flyway, Liquibase, EF Core, Laravel).
- Every migration reversible where possible; test up and down on a copy of the data.
- Large tables: add columns as nullable then backfill in batches; create indexes concurrently (`CREATE INDEX CONCURRENTLY` in PostgreSQL); avoid long locks.
