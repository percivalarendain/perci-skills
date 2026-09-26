# Architecture

Inspect before changing: Laravel version, route style, modules, namespaces, packages, test runner, database conventions, authorization, and existing patterns. Preserve intentional local conventions.

Use the smallest design that makes responsibilities clear:

`Route → Controller → FormRequest → Policy → Action → Model`

Controllers are transport adapters; Actions represent meaningful write use-cases; Models hold persistence and small state behavior; Queries are for complex reusable reads; Services are for reusable capabilities or integrations. Support is for genuinely generic, reusable application primitives that are not owned by a specific business module, such as `HasPrefixedUlid`, `ReferenceNumber`, small reusable Value Objects, or generic application-message utilities. Prefer Laravel-native functionality before adding Support abstractions. Do not use Support as a miscellaneous helper directory, a place for domain workflows, a dumping ground for static helpers, or a wrapper around Laravel functionality that provides no application-specific value. A layer is optional, not a checklist item. A basic CRUD usually does not need a repository, CRUD service, query class, observer, event, job, cache, or custom exception.

Use `DB::transaction()` around multiple related writes that must be atomic. Keep external effects after durable state is committed and design retries/idempotency where needed. Avoid unrelated refactors.

Use a small project convention for reusable user-facing messages, conceptually exposing `AppMessage::success(...)`, `AppMessage::failed(...)`, and `AppMessage::custom(...)`. Standard success and failure messages should derive a human-readable module/resource label when practical—for example, `Department` becomes “Department added successfully.” Use explicit or custom messages when a generic template would be inaccurate. Keep this compatible with Laravel localization when the project uses it; do not build a large message or translation framework solely for this convention.

For authorization, a FormRequest `authorize()` may handle authorization for making that specific request. Policies remain the standard for model/resource authorization, and controllers may invoke Policies explicitly when that makes the flow clearer. FormRequests may also call Policies when appropriate. Avoid performing the same authorization check redundantly in multiple layers; follow an existing project convention when it is already clear and consistent.
