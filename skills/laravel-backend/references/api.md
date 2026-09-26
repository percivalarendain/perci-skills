# APIs

Use versioned routes such as `/api/v1` and inspect existing versioning before adding a new scheme. Sanctum is the default for first-party/mobile token authentication when appropriate. Use FormRequests, Policies, API Resources, pagination, and Laravel RateLimiter.

Filtering and sorting fields must be allowlisted; never pass arbitrary request fields into SQL order/filter clauses. Return stable Resource contracts and correct status codes: validation errors, authentication/authorization failures, not-found, conflicts, successful creation, and no-content should remain distinguishable. Do not return HTTP 200 for every failure. Keep provider/internal error details out of public responses.
