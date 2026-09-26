# Logging

Use structured, contextual logs at appropriate levels. Log events that help diagnose operations, failures, security, and integrations; do not log every successful line. Include safe identifiers and correlation/request IDs when the project supports them.

Never log passwords, API keys, bearer tokens, authorization headers, OTPs, private keys, or secrets. Redact sensitive payload fields. Avoid logging the same exception repeatedly across controller, Action, job, and global handler; choose the layer that can add the most useful context.
