# ADR-008: Multi-Tenant Tier-Based Token-Bucket Rate Limiting

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-08-24 |
| Deciders | Backend Team, Product Owner |

---

## Context

As a multi-tenant SaaS platform, Launchly hosts tenants ranging from individual developers (`FREE`) to large enterprises (`ENTERPRISE`). Unrestricted API usage risks DDoS vulnerabilities, resource starvation for paying tenants, and unexpected infrastructure billing surges (especially for AI generation).

## Decision

We instituted **Tier-Based Rate Limiting using the Token-Bucket Algorithm** backed by Redis:
1. **Tier Quotas**:
   - `FREE`: 60 requests/minute, strict AI quota caps.
   - `PRO`: 300 requests/minute.
   - `ENTERPRISE`: 1,200 requests/minute.
   - `SUPER_ADMIN`: Unmetered.
2. **Filter Level Enforcement**: Implemented in `TierRateLimitFilter` prior to controller invocation, reducing unnecessary thread mounts.
3. **RFC Standards Compliance**: Every response includes standardized rate limit headers:
   - `X-RateLimit-Limit`
   - `X-RateLimit-Remaining`
   - `X-RateLimit-Reset`
   - `Retry-After` (when returning HTTP 429).

## Consequences

### Positive
- **Tenant Isolation**: One tenant's traffic spike cannot degrade service quality for another.
- **Fair Monetization**: Clean boundary enforcement encouraging upgrades to higher tiers.

### Negative / Trade-offs
- Adds a small Redis lookup overhead (~0.5ms) on incoming API requests.
