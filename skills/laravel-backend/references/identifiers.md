# Identifiers and references

Application-owned models use a reusable convention such as `HasPrefixedUlid`, producing IDs like `dept-01...`, `emp-01...`, `usr-01...`, or `inv-01...`. Keep technical primary IDs separate from business references: a Department can have `id=dept-01...` and `code=ENG`; an Invoice can have `id=inv-01...` and `invoice_no=INV-20260926-000001`.

Reference generation may expose focused strategies such as `ReferenceNumber::random('INV', 6)`, `sequence('INV')`, and `datedSequence('INV')`. Sequence values are generated internally by the sequence mechanism; callers must not manually supply the next number. Sequential generation must be concurrency-safe using a transaction, lock, or database-native mechanism. Never use `count() + 1` or an unsafe `max() + 1`. Random references that require uniqueness must handle collisions and retry safely. Add database uniqueness constraints regardless; the database remains the final uniqueness protection.

Reference generation should normally happen inside the owning Action or application workflow, not in arbitrary controllers or callers. Business references are immutable by default unless requirements explicitly allow modification. If rules become domain-specific, use a dedicated generator rather than bloating the generic `ReferenceNumber` utility.

Prefix generation must be compatible with the project's storage length, collation, and package expectations. Do not force this convention onto third-party tables without support.
