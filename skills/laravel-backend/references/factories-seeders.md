# Factories and seeders

Factories create model data for tests and development. Keep states named and purposeful, and use factories for test setup rather than hand-building large object graphs.

Separate idempotent system/reference seeders from development/demo seeders. System seeders may create permissions, roles, or required reference records and should use `firstOrCreate`/upserts where practical. Demo seeders may be destructive or random and must not be required for production. Never place random demo data in production/reference seeders.
