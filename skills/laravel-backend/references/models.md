# Models

Models may contain relationships, casts, scopes, accessors/mutators, factories, and small state transitions. Use explicit fillable/guarded policy consistent with the project and prefer casts/enums for stable domain values.

Keep HTTP concerns, large workflows, external API calls, and unrelated orchestration out of models. Define relationships and indexes to support actual access patterns. Avoid hidden side effects in accessors or model events unless lifecycle behavior is deliberate, tested, and genuinely reusable.
