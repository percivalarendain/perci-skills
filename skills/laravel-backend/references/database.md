# Database practices

Treat the database as the source of truth. Use `DB::transaction()` when several related writes must all succeed or fail together, not around every individual operation. A rollback cannot undo HTTP calls, mail, filesystem changes, or already-dispatched external effects; persist state first and dispatch after commit where appropriate.

Use constraints for invariants: foreign keys, unique indexes, non-nullability, checks where supported, and indexes for real lookup patterns. Match foreign-key types to referenced primary keys. Consider locking or atomic database operations for concurrency-sensitive updates.
