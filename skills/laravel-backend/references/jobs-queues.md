# Jobs, Redis, and Horizon

Use Jobs for asynchronous or background work that is slow, retryable, or operationally independent. Do not queue trivial work only because queues exist. Redis is a sensible preferred backend when the application materially uses queues; use Horizon for Redis worker monitoring when it fits the deployment.

Define intentional retries, backoff, timeout, and failure behavior. Retryable jobs must be idempotent: use stable identifiers, uniqueness/locks, or persisted state as needed. Dispatch jobs after commit when they depend on committed rows. Never assume a job runs exactly once; make external effects safe to repeat or reconcile failures.
