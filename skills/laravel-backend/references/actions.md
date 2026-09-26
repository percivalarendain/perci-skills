# Actions

An Action is one application/business use-case with one clear public entry method, for example `CreateDepartmentAction` or `ApproveRequestAction`. It may coordinate models, transactions, reusable services, events, and jobs when those are justified.

Actions are useful for writes and workflows reused by HTTP, commands, jobs, or other entry points. Keep input explicit and return a useful domain/model result. Put `DB::transaction()` in the operation that owns the atomic boundary. Do not create Actions for trivial one-line persistence merely to satisfy a pattern, and do not let an Action become a miscellaneous service.
