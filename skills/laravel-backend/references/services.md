# Services

Services represent reusable capabilities, infrastructure behavior, or integrations: an external API client, report generator, or application-specific storage workflow. Keep them cohesive and dependency-inject collaborators.

Do not create `DepartmentService` just to wrap `Department` CRUD. Do not use a Service as a dumping ground for unrelated business logic; a concrete use-case belongs in an Action, persistence behavior in a Model, and complex reads in a Query. Prefer Laravel's `Http`, `Storage`, `Cache`, mail, notification, and queue APIs directly unless reusable application behavior exists around them.
