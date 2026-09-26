# Testing

Prefer Pest when the project already uses Pest; otherwise match the existing runner. Use Feature tests for HTTP flows, validation, authorization, and important application workflows. Use Unit tests for isolated logic where they clarify behavior. Cover meaningful success, denial, invalid input, conflicts, and edge cases rather than writing tests only for coverage.

Use factories for setup, fake external integrations, and control time for temporal behavior. Assert public behavior and durable state, not private implementation details. For queued, evented, mailed, or notified work, use framework fakes and assert dispatch/content where that behavior is part of the contract. Keep tests deterministic and aligned with the installed Laravel version.
