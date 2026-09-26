# Controllers and Form Requests

Controllers are HTTP, Inertia, or API entry points. They should receive a validated request, authorize when appropriate, invoke an Action or Query, and return a redirect, response, Inertia result, or API Resource. Keep substantial workflows out of controllers.

Use FormRequests for non-trivial validation and request-level authorization when appropriate. Keep rules in one place rather than duplicating them across controller methods. Use route model binding and validated input. Validate uploaded files, allowlisted filters, and nested data explicitly.

Prefer separate store/update requests when rules or authorization differ. Do not force a FormRequest for trivial internal calls if the project convention does not need one. Match the installed Laravel version's request and response APIs.
