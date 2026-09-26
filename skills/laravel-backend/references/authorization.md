# Authorization

Use Spatie Laravel Permission for roles and permissions when that package is part of the project. Use Laravel Policies for model/resource authorization and authorize at the request/controller boundary or through the project's established mechanism.

Prefer permissions over hard-coded role checks. Roles primarily group permissions. If explicitly required, implement one centralized super-admin bypass through the package/policy mechanism. Do not scatter `if ($user->role === 'admin')` through application code. Test both allowed and denied paths, including ownership and tenant boundaries.
