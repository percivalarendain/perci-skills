# Project Instructions

## Project First

Before making changes:

1. Inspect the project's actual stack and installed versions.
2. Inspect existing code near the requested change.
3. Follow established project conventions unless the task intentionally changes them.
4. Do not assume packages, versions, architecture, or available tooling.

## Laravel Backend

For Laravel backend work, use the `laravel-backend` skill.

Load only the skill references relevant to the current task.

Examples:

- CRUD or write operation → architecture, controllers/requests, actions, models, authorization, testing
- database change → migrations and database
- API work → API plus relevant backend references
- queued work → jobs/queues
- caching → caching
- scheduled work → commands/scheduler

Do not load unrelated references merely because they exist.

## Development Principles

- Prefer Laravel-native functionality.
- Keep controllers thin.
- Use FormRequests for non-trivial request validation.
- Use Actions for application use-cases when they provide a clear responsibility.
- Use Services for reusable capabilities and integrations.
- Use Query classes only for complex or reusable reads.
- Use Policies for resource authorization.
- Add architectural layers only when required.
- Avoid speculative abstractions and unrelated refactoring.
- Preserve backward compatibility unless the task explicitly requires otherwise.

## Verification

For behavior changes:

1. Add or update appropriate tests.
2. Run focused tests first.
3. Run the project's formatter.
4. Run relevant static-analysis or quality tools already available in the project.
5. Report failures clearly rather than bypassing them.

Project-specific instructions and established project conventions override reusable skill defaults.