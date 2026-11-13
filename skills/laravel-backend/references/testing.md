# Testing

Use Action-Based Testing as the default for business logic: one meaningful Action has one dedicated test file containing multiple relevant business scenarios. This organizes tests; it does not require an Action for every operation or a test for every method. Follow intentional, consistent project naming conventions, and do not reorganize existing tests solely to enforce this structure.

```text
app/Actions/Products/
├── CreateProductAction.php
├── UpdateProductAction.php
└── PublishProductAction.php

tests/Feature/Products/
├── CreateProductActionTest.php
├── UpdateProductActionTest.php
└── PublishProductActionTest.php
```

Choose scenarios from the Action's actual responsibilities: successful execution, business validation and rejected operations, state transitions, persistence and consistency, transaction rollback, duplicates and idempotency, concurrency-sensitive behavior, and relevant events, jobs, or notifications. Not every Action needs every scenario. Do not add empty or placeholder tests or tests merely to increase counts.

## Test boundaries

- **Action Feature tests** verify business operations and observable outcomes. Invoke the Action directly where practical, using the Laravel application and test database when needed.
- **HTTP Feature tests** verify routes, middleware, authentication, authorization, FormRequest validation, and response contracts. Keep them separate when meaningful; do not repeat every Action scenario through HTTP.
- **Unit tests** cover pure calculations, value objects, and isolated business utilities without unnecessary Laravel bootstrapping or database access.
- **Integration tests** verify real integration boundaries when needed. Fake or mock external services in ordinary tests, and explicitly separate tests that require actual third-party services.

Actions own business workflows; controllers orchestrate HTTP; Policies own resource authorization; FormRequests own input validation and may handle request-level authorization. Test each responsibility at the appropriate boundary, respecting the project's authorization convention. Assert public behavior and durable state rather than private implementation details. Do not introduce Actions, Services, Repositories, or other abstractions solely for testability.

Use descriptive names that communicate business behavior. Prefer Pest when the inspected project already uses it; otherwise match its existing runner, including PHPUnit, without requiring a conversion. For example, in a Pest project using the Department Action and factory from the [CRUD example](../examples/department-crud.md), a dedicated `CreateDepartmentActionTest.php` could contain:

```php
<?php

use App\Actions\Departments\CreateDepartmentAction;
use App\Models\Department;
use Illuminate\Foundation\Testing\RefreshDatabase;

uses(RefreshDatabase::class);

it('creates a department with the supplied attributes', function () {
    $department = app(CreateDepartmentAction::class)->handle([
        'code' => 'ENG',
        'name' => 'Engineering',
        'status' => 'active',
    ]);

    expect($department)->toBeInstanceOf(Department::class);
    $this->assertDatabaseHas('departments', [
        'id' => $department->id,
        'code' => 'ENG',
        'name' => 'Engineering',
        'status' => 'active',
    ]);
});
```

Adapt namespaces and APIs to the project, and add other scenarios only when the Action owns those behaviors. For queued, evented, mailed, or notified work, use framework fakes and assert dispatch or content when part of the contract. Control time for temporal behavior. Keep tests deterministic and compatible with the installed Laravel version.

## Database safety

Tests must never modify development, staging, or production databases. Before running database tests, inspect the effective test configuration and connection target; never assume `.env.testing` is safe. Use an isolated testing database or an appropriate isolated in-memory database, `RefreshDatabase` or equivalent isolation where applicable, and factories for relevant test data. Obtain explicit approval before running destructive commands against an existing database. When SQLite cannot reproduce relevant MySQL behavior, also verify against an isolated MySQL test database.

## Workflow for Claude Code and Codex

Whenever creating or modifying an Action:

1. Identify its corresponding Action test file and review existing coverage.
2. Add or update tests for affected business scenarios, including relevant edge cases and failure paths.
3. Run focused tests first, then broader tests when appropriate.
4. Report test results and remaining coverage gaps. Do not report the task as fully verified if relevant tests were not executed.
