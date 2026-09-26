# Migrations

Use consistent Laravel migration names and reversible migrations where practical. Inspect the target Laravel/database version before relying on framework or engine-specific behavior. Add explicit foreign keys, delete behavior, uniqueness, nullability, and indexes based on the domain and query patterns.

Application-owned primary IDs use prefixed ULIDs by project convention, for example `dept-01...` or `usr-01...`; the foreign-key column must use a compatible string/ULID representation. Technical primary IDs and business references are separate. Do not modify third-party package tables merely to impose application IDs unless supported and required. Treat production data migrations as a separate, carefully tested concern from schema changes.
