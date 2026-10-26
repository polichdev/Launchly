# ADR-006: Transactional Outbox Pattern & Dead Letter Queue for Domain Events

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-08-20 |
| Deciders | Backend Team, Architecture Lead |

---

## Context

When an entity state changes (e.g., a Telegram message is received, an order is placed, or an external webhook fires), downstream actions must occur: external Telegram API calls, email dispatches, or third-party webhooks. Direct dual-writing (writing to PostgreSQL and immediately invoking the remote API) creates data inconsistency: if the remote network call fails or times out, the local transaction cannot easily rollback, or conversely, if the database transaction fails after sending, duplicate external actions occur.

## Decision

We implemented the **Transactional Outbox Pattern with a Dead Letter Queue (DLQ)**:
1. **Atomic Ingestion**: All events are inserted into an `outbox_events` table within the same database transaction modifying business state.
2. **Asynchronous Polling Worker**: `OutboxProcessor` periodically queries pending records using scheduled batch runs.
3. **Exponential Backoff**: Transient errors increment a `retry_count` and compute `next_retry_at`.
4. **Dead Letter Queue (DLQ)**: Events exceeding max retries (5 attempts) are transitioned to status `DEAD_LETTER` for administrative review via `AdminOutboxController`.

## Consequences

### Positive
- **Guaranteed At-Least-Once Delivery**: Eliminates dual-write anomalies; events survive application crashes.
- **Decoupled Database from External Latencies**: Primary user requests commit in milliseconds without waiting on third-party HTTP responses.

### Negative / Trade-offs
- Slight eventual consistency delay (~500ms - 1s) for asynchronous delivery.
- Requires regular table maintenance / archiving of processed events.
