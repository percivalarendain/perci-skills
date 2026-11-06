# Rate limiting

Rate limiting is a default security and availability requirement for public-facing routes, authentication flows, sensitive operations, and endpoints that are costly or easy to abuse. Apply it by route group or named limiter, and document any intentional exception. A limit reduces application-level abuse; it is not a substitute for authorization, input validation, provider controls, network-level DDoS protection, or capacity planning.

## Laravel-native implementation

Use Laravel's `Illuminate\Support\Facades\RateLimiter`, `Illuminate\Cache\RateLimiting\Limit`, and `throttle` middleware. Prefer a named limiter registered with `RateLimiter::for(...)` and attached to the relevant route/group using the installed Laravel version's supported route and middleware APIs. Use the built-in throttle response behavior unless the project has a centralized API error contract that requires a customized response.

Illustrative named-limiter shape (verify imports and registration location for the installed Laravel version):

```php
RateLimiter::for('public-api', function (Request $request) {
    $key = $request->user()
        ? 'user:'.$request->user()->getAuthIdentifier()
        : 'ip:'.$request->ip();

    return Limit::perMinute(60)->by('public-api:'.$key);
});
```

Do not build limiter keys from raw, untrusted headers. Use stable, opaque identifiers and prefix keys by purpose so unrelated traffic does not consume the same bucket. Where appropriate, enforce more than one dimension together (for example, per-IP and per-account) rather than choosing only one. Laravel's named limiter can return multiple `Limit` instances; confirm exact syntax against the installed framework version.

## Client identity and limiter dimensions

Choose identity according to the route's risk and whether identity is available before the operation:

- **Per IP:** useful for anonymous endpoints and as a broad abuse guard. Resolve the address from Laravel's trusted-proxy-aware request API, not directly from `X-Forwarded-For` or another client-supplied header.
- **Per user:** use the authenticated user's stable primary identifier for authenticated features. Retain a secondary IP limit where account sharing or compromised credentials are concerns.
- **Per account or tenant:** use the stable account/tenant ID for account-scoped operations so members share the intended capacity and cannot multiply it by creating users.
- **Per merchant:** use the authenticated merchant's stable ID for merchant-facing payment or integration operations.
- **Per API key:** use a non-secret key ID or fingerprint, never the credential itself. A key-specific quota should be combined with a broader account or IP control when useful.

For login and other pre-authentication flows, combine normalized account identity (for example, a keyed hash of the submitted login identifier) with an IP limit. Do not store raw email addresses or secrets in cache keys or logs. Do not reveal whether an account exists in the response. Avoid a single global bucket that allows one client to exhaust capacity for all clients.

Configure trusted proxies explicitly for the deployment topology using the Laravel version's supported `TrustProxies` configuration. Trust only known load balancers/proxy networks or a tightly controlled proxy boundary. Do not trust arbitrary forwarded headers from the public internet: attackers could spoof their apparent IP and evade or poison IP-based controls. Verify behavior through the actual ingress chain in deployment tests or operational checks.

Account for shared NATs (offices, schools, mobile carriers) by pairing IP controls with user/account/API-key identity and by choosing thresholds that do not penalize an entire group unfairly. IPv6 addresses may rotate within a prefix; decide whether anonymous-IP controls should group addresses by a carefully chosen IPv6 prefix, while keeping authenticated identity controls. Normalize addresses consistently and avoid overly broad prefix aggregation that can block unrelated users.

## Starting policy defaults

These are configurable starting points, not universal capacity guarantees. Tune them using expected legitimate volume, cost per request, provider contracts, observed traffic, and the application's latency and capacity. Apply limits at the appropriate route boundary and consider both short bursts and sustained rates.

| Route class | Suggested starting limit | Identity and notes |
| --- | --- | --- |
| Authentication attempts (login, OTP verification, password reset requests) | 5 attempts per minute per IP and per normalized account identifier; add a slower account window such as 20 per hour | Keep account enumeration-resistant responses. For OTP delivery, separately limit sends/resends and enforce cooldowns. |
| General public API | 60 requests per minute per IP for anonymous clients; 120 per minute per user or API key for authenticated clients | Add per-account ceilings where keys/users belong to a tenant. Use route-specific lower limits for expensive operations. |
| Payment operations initiated by a client | 10 requests per minute per user/account/merchant per operation class, plus a broader IP guard | Set limits based on expected checkout/retry bursts and payment-provider capacity. Keep reads and writes in separate buckets if their costs differ. |
| Payment-provider webhooks | Do not apply a generic low per-IP or user throttle. As an initial configurable application ceiling, allow at least 600 requests per minute per verified provider integration, or a higher measured peak with headroom; queue accepted events and bound downstream concurrency | This is a starting capacity guard, not a universal provider limit. Account for retries, event bursts, source IP changes, and provider delivery guarantees. Reject invalid signatures consistently and monitor them. Adjust to measured volume and coordinate any ceiling with provider retry behavior. |

Express values in configuration where the application needs operators to tune them without code changes. Avoid hard-coding a quota in many route definitions. Do not make Redis, Docker, or any other infrastructure mandatory for local development or every project.

## Responses, observability, and tests

Return HTTP `429 Too Many Requests` consistently for a rejected request, with the application's normal API error envelope and no internal limiter details. Include `Retry-After` in seconds so clients can safely retry. Laravel throttle middleware normally adds throttling headers; verify that custom exception rendering preserves `Retry-After`, and use one shared response path if custom named limiters need it. Do not encourage immediate retry loops; document exponential backoff and jitter for clients where relevant.

Log and monitor aggregate throttling events, including route/limiter name, safe client dimension (such as opaque user/account/key ID or a privacy-reviewed IP representation), and request/correlation ID when available. Do not log credentials, raw API keys, submitted passwords, OTPs, or unnecessary personal data. Alert on meaningful changes in 429 rates, repeated abuse patterns, saturation, and webhook backlog/verification failures. Avoid one log entry per rejected request at a volume that can itself exhaust logging capacity; use aggregation or sampling where needed.

Automated tests are required for each limiter policy:

- Verify requests below the limit succeed and the next request is rejected with status 429 and a usable `Retry-After` header.
- Verify the bucket resets after its window, using the framework's time-travel helpers or a deterministic cache/limiter setup supported by the installed version.
- Verify correct client identification: distinct users/accounts/API keys receive the intended independent buckets, shared identities share the intended bucket, and anonymous clients are keyed from the trusted request IP.
- Verify a forged forwarding header cannot change client identity when sent from an untrusted source; test trusted proxy behavior according to the configured topology.
- Verify payment retries remain safe through idempotency tests independently of the rate-limit tests, and verify legitimate provider webhook bursts/signatures follow the provider-specific strategy.

## Distributed deployments and payment safety

In a multi-server production deployment, use a shared Redis-backed cache/limiter store so all application instances observe the same counters. Confirm that Laravel's rate limiter is configured to use the intended shared store for the installed version; a per-process or per-server store allows clients to multiply their effective quota across instances. Keep local/test environments compatible with the application's existing cache driver. If Redis is unavailable, define and document the failure behavior rather than silently assuming counters are shared.

Allow legitimate short bursts where user experience or provider delivery requires them, but cap sustained rates and expensive work. Consider queuing webhook processing, limiting concurrent downstream calls, and enforcing request/body size limits. Application rate limiting cannot stop volumetric denial-of-service traffic before it reaches the application; use the deployment's edge/load-balancer/CDN protections when available, while preserving a trusted proxy boundary. Ensure limiter-store outages, queue growth, and throttling spikes are observable.

Payment idempotency is separate from rate limiting. Rate limits control request volume; idempotency keys prevent duplicate effects when clients or providers retry. For payment mutations, use the provider's idempotency mechanism or a durable application-level idempotency record with a well-defined scope and uniqueness constraint. Apply idempotency before side effects, and test duplicate requests independently. A rate-limit rejection must not be treated as proof that a payment was or was not processed.

For webhooks, verify the provider's signature and replay/timestamp requirements before accepting the event. Use provider event IDs for deduplication and queue work when appropriate. Do not blindly throttle by client IP: provider ranges can change, multiple providers may share egress, and legitimate retry bursts are expected. Define capacity from provider-specific delivery behavior, monitor incoming volume and queue depth, and coordinate any protective ceiling with the provider's retry policy.
