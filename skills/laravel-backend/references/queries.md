# Queries

Keep simple reads simple Eloquent: `Department::query()->where(...)->get()`. Create a Query class only for complex or reusable read logic, such as multiple filters, joins, aggregates, authorization-aware reporting, or a query used by several entry points.

Keep filtering and sorting allowlisted. Make pagination explicit at the boundary. Avoid query objects that merely rename one `Model::query()` call. Inspect generated SQL and eager-load relationships needed by the response to prevent N+1 behavior.
