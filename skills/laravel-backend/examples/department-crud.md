# Department CRUD example

This is a compact Laravel-shaped example for a basic Department CRUD. Adapt namespaces, auth middleware, route style, enum support, ULID implementation, and response conventions to the inspected project. It intentionally has no Service, Repository, Query, Observer, Event, Job, cache, or custom exception.

## Migration

```php
Schema::create('departments', function (Blueprint $table) {
    $table->string('id', 32)->primary();
    $table->string('code', 20)->unique();
    $table->string('name');
    $table->string('status', 20)->index();
    $table->timestamps();
});
```

The exact ULID string length should match the project's `HasPrefixedUlid` implementation. The business `code` is separate from the primary `id`.

## Enum and model

```php
enum DepartmentStatus: string
{
    case Active = 'active';
    case Inactive = 'inactive';
}
```

```php
final class Department extends Model
{
    use HasPrefixedUlid;

    protected $table = 'departments';
    protected $keyType = 'string';
    public $incrementing = false;

    protected $fillable = ['code', 'name', 'status'];

    protected function casts(): array
    {
        return ['status' => DepartmentStatus::class];
    }
}
```

`HasPrefixedUlid` is a project-level reusable trait that generates the `dept-` primary key. Its implementation is intentionally omitted here because Laravel version and existing ID infrastructure must be inspected first.

## Requests

```php
final class StoreDepartmentRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'code' => ['required', 'string', 'max:20', 'alpha_dash', 'unique:departments,code'],
            'name' => ['required', 'string', 'max:255'],
            'status' => ['required', Rule::enum(DepartmentStatus::class)],
        ];
    }
}

final class UpdateDepartmentRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        $department = $this->route('department');

        return [
            'code' => ['required', 'string', 'max:20', 'alpha_dash', Rule::unique('departments', 'code')->ignore($department)],
            'name' => ['required', 'string', 'max:255'],
            'status' => ['required', Rule::enum(DepartmentStatus::class)],
        ];
    }
}
```

## Actions

```php
final class CreateDepartmentAction
{
    public function handle(array $attributes): Department
    {
        return Department::create($attributes);
    }
}

final class UpdateDepartmentAction
{
    public function handle(Department $department, array $attributes): Department
    {
        $department->update($attributes);

        return $department->refresh();
    }
}

final class DeleteDepartmentAction
{
    public function handle(Department $department): void
    {
        $department->delete();
    }
}
```

If creation later writes related records, put the related writes inside one `DB::transaction()` in the Action. Do not add a transaction around this single insert/update without a need.

## Policy and controller

```php
final class DepartmentPolicy
{
    public function viewAny(User $user): bool { return $user->can('departments.view'); }
    public function view(User $user, Department $department): bool { return $user->can('departments.view'); }
    public function create(User $user): bool { return $user->can('departments.create'); }
    public function update(User $user, Department $department): bool { return $user->can('departments.update'); }
    public function delete(User $user, Department $department): bool { return $user->can('departments.delete'); }
}
```

```php
final class DepartmentResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'code' => $this->code,
            'name' => $this->name,
            'status' => $this->status->value,
        ];
    }
}
```

```php
final class DepartmentController extends Controller
{
    public function index()
    {
        $this->authorize('viewAny', Department::class);

        return DepartmentResource::collection(Department::query()->latest()->paginate());
    }

    public function store(StoreDepartmentRequest $request, CreateDepartmentAction $action): JsonResponse
    {
        $this->authorize('create', Department::class);
        $department = $action->handle($request->validated());

        return (new DepartmentResource($department))->response()->setStatusCode(201);
    }

    public function update(UpdateDepartmentRequest $request, Department $department, UpdateDepartmentAction $action): JsonResponse
    {
        $this->authorize('update', $department);

        return (new DepartmentResource($action->handle($department, $request->validated())))->response();
    }

    public function destroy(Department $department, DeleteDepartmentAction $action): Response
    {
        $this->authorize('delete', $department);
        $action->handle($department);

        return response()->noContent();
    }
}
```

For an Inertia or web application, return the project's established view/redirect and application-message convention instead of JSON. Avoid duplicating authorization if the chosen FormRequest and controller policy checks are intentionally centralized; use one clear project convention.

## Routes

```php
Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('departments', DepartmentController::class)
        ->only(['index', 'store', 'update', 'destroy']);
});
```

Use the existing API version prefix and middleware stack rather than introducing Sanctum or versioning without inspecting the project.

## Factory

```php
final class DepartmentFactory extends Factory
{
    protected $model = Department::class;

    public function definition(): array
    {
        return [
            'code' => fake()->unique()->bothify('DPT-###'),
            'name' => fake()->company(),
            'status' => DepartmentStatus::Active,
        ];
    }
}
```

## Representative Pest feature tests

```php
it('creates a department', function () {
    $user = User::factory()->create();
    // When Spatie Laravel Permission is installed and configured:
    $user->givePermissionTo('departments.create');

    $response = $this->actingAs($user, 'sanctum')->postJson('/api/v1/departments', [
        'code' => 'ENG', 'name' => 'Engineering', 'status' => 'active',
    ]);

    $response->assertCreated()->assertJsonPath('data.code', 'ENG');
    $this->assertDatabaseHas('departments', ['code' => 'ENG', 'name' => 'Engineering']);
});

it('rejects a duplicate code', function () {
    $user = User::factory()->create();
    // When Spatie Laravel Permission is installed and configured:
    $user->givePermissionTo('departments.create');
    Department::factory()->create(['code' => 'ENG']);

    $this->actingAs($user, 'sanctum')
        ->postJson('/api/v1/departments', ['code' => 'ENG', 'name' => 'Other', 'status' => 'active'])
        ->assertUnprocessable()
        ->assertJsonValidationErrors('code');
});

it('forbids a user without the permission', function () {
    $user = User::factory()->create();

    $this->actingAs($user, 'sanctum')
        ->postJson('/api/v1/departments', ['code' => 'ENG', 'name' => 'Engineering', 'status' => 'active'])
        ->assertForbidden();
});
```

Add update/delete and not-found tests when those paths are part of the task. Use the project's actual API Resource shape, auth guard, permission setup, and Pest/Laravel version APIs.
