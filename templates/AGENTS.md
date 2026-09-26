# Project Instructions

## Project First

Before modifying code:

1. Inspect the project's actual stack and installed versions.
2. Inspect existing implementations near the requested change.
3. Identify established project conventions.
4. Do not assume packages, versions, architecture, or available tooling.

## Laravel Backend

For Laravel backend work, use the `laravel-backend` skill.

Read only the skill references relevant to the current task.

Do not load unrelated references by default.

## Development Principles

- Prefer Laravel-native functionality.
- Implement the smallest maintainable solution that satisfies the requirement.
- Keep controllers thin.
- Use FormRequests for non-trivial request validation.
- Use Actions for application use-cases when appropriate.
- Use Services for reusable capabilities and integrations.
- Use Query classes only for complex or reusable reads.
- Use Policies for resource authorization.
- Do not introduce architectural layers without a concrete responsibility.
- Avoid speculative abstractions.
- Avoid unrelated refactoring.
- Preserve backward compatibility unless the task explicitly requires otherwise.

Do not automatically create:

- Services
- Repositories
- Queries
- Events
- Listeners
- Jobs
- Observers
- custom Exceptions
- caching layers

Create them only when the requirement justifies them.

## Verification

For behavior changes:

1. Add or update appropriate tests.
2. Run focused tests first.
3. Run the project's formatter.
4. Run relevant static-analysis or quality tools already installed in the project.
5. Do not hide or bypass failures.

Project-specific instructions and established project conventions override reusable skill defaults.