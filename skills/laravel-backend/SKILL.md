---
name: laravel-backend
description: Build and maintain Laravel backend applications with a pragmatic, Laravel-native architecture. Use for Laravel models, migrations, HTTP endpoints, application workflows, jobs, integrations, and backend tests; inspect the target project's Laravel version and conventions first.
metadata:
  short-description: Pragmatic Laravel backend engineering standard
---

# Laravel backend standard

Use this skill for provider-neutral Laravel backend work in Claude Code or Codex. First inspect the application, its installed Laravel version, package choices, tests, and local conventions. Existing intentional conventions take precedence over these defaults. Keep the smallest maintainable design: do not add layers, events, caching, repositories, observers, or abstractions without a requirement that benefits from them.

For a normal write flow, prefer:

`Route → Controller → FormRequest → Policy → Action → Model`

Keep controllers thin, use Laravel-native features, make related writes transactional, and update meaningful tests for behavior changes. Use `config()` in application code; use `env()` only in configuration/bootstrap locations.

## Load references selectively

Read only the references relevant to the task:

- Design and boundaries: [architecture](references/architecture.md), [controllers and requests](references/controllers-requests.md), [actions](references/actions.md), [services](references/services.md), [queries](references/queries.md), [models](references/models.md).
- Data and domain behavior: [database](references/database.md), [migrations](references/migrations.md), [identifiers](references/identifiers.md), [dates and timezones](references/dates-timezones.md), [money](references/money.md), [factories and seeders](references/factories-seeders.md).
- Security and failure handling: [authorization](references/authorization.md), [exceptions](references/exceptions.md), [configuration](references/configuration.md), [logging](references/logging.md), [files and storage](references/files-storage.md).
- Async and integrations: [jobs and queues](references/jobs-queues.md), [events and listeners](references/events-listeners.md), [caching](references/caching.md), [commands and scheduler](references/commands-scheduler.md), [notifications and mail](references/notifications-mail.md).
- HTTP and verification: [API](references/api.md), [testing](references/testing.md).
- End-to-end shape: [Department CRUD example](examples/department-crud.md).

Do not apply the example mechanically. Adapt names, namespaces, auth, route style, test framework, and framework APIs to the inspected project.
