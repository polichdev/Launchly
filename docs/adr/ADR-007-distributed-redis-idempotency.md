# ADR-007: Distributed Redis Idempotency on High-Value Mutations

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-08-22 |
| Deciders | Backend Team, Core Infrastructure |

---

## Context

Network instability often causes clients, Telegram webhook servers, or Stripe payment emitters to retry HTTP requests when a socket timeout occurs before receiving a 200 OK. If mutation endpoints (such as `/api/billing/checkout`, `/api/broadcasts/launch`, or external order webhooks) are not strictly idempotent, retried requests create duplicate broadcast runs, duplicate billing records, or corrupted CRM lead histories.

## Decision

We designed a declarative **Redis-Backed Distributed Idempotency Layer**:
1. **Annotation**: High-value controller methods are annotated with `@Idempotent(expireAfter = 24, timeUnit = TimeUnit.HOURS)`.
2. **Client Key Extraction**: Requests supply an `Idempotency-Key` HTTP header (or derive one from payload hash + tenantId).
3. **Atomic Mutual Exclusion**:
   - The aspect executes a Redis atomic `SET lock:idempotency:{key} "PROCESSING" NX PX {lockTimeout}`.
   - If the key exists with `"PROCESSING"`, the request is rejected with `409 Conflict`.
   - If the key exists with `"COMPLETED:{cachedResponse}"`, the aspect short-circuits and replays the cached response.
   - Upon completion, the result is stored in Redis for 24 hours.

## Consequences

### Positive
- **Guaranteed Exactly-Once Processing**: Eliminates double charges and duplicate broadcast batches.
- **Transparent Client Replay**: Retried requests receive identical responses without executing business logic twice.

### Negative / Trade-offs
- Requires Redis cluster availability (fallback logic must be defined for disaster recovery).
