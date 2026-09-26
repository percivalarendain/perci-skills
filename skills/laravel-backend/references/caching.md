# Caching

Do not cache by default. Add caching only when it provides measurable or operational value, and keep the database as source of truth. Prefer Laravel Cache and `Cache::remember()` for suitable reads; Redis may be the configured store. Use tags only when the selected store supports them.

Choose invalidation deliberately. Model observers are appropriate only when lifecycle invalidation is genuinely reusable and reliable. Use locks for concurrency-sensitive cache rebuilds or business operations. Define keys, TTLs, stale behavior, and invalidation tests; do not build cache infrastructure for every model.
