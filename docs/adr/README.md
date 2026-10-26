# Architecture Decision Records (ADRs)

This directory documents the technical decisions made throughout the lifecycle of **Launchly**. Each record adheres to the [Michael Nygard format](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions), capturing the context, decision, consequences, and evaluated alternatives.

---

## Decision Log

| ID | Title | Status | Date | Primary Driver |
|:---|:---|:---|:---|:---|
| **[ADR-001](ADR-001-postgresql-jsonb-bot-graph.md)** | Use PostgreSQL with JSONB for Flow Graphs & Schema State | Accepted | 2026-07-10 | Data Modeling & Schema Agility |
| **[ADR-002](ADR-002-multi-provider-ai-fallback-router.md)** | Multi-Provider AI Fallback Router Architecture | Accepted | 2026-07-25 | Resilience & High Availability |
| **[ADR-003](ADR-003-java-21-virtual-threads-loom.md)** | Adoption of Java 21 Virtual Threads (Project Loom) | Accepted | 2026-08-01 | Concurrency & I/O Scalability |
| **[ADR-004](ADR-004-testcontainers-hermetic-testing.md)** | Hermetic Integration Testing with Testcontainers | Accepted | 2026-08-15 | Software Quality & CI Parity |
| **[ADR-005](ADR-005-websocket-stomp-live-crm-inbox.md)** | STOMP over WebSocket Protocol for Live CRM Inbox | Accepted | 2026-08-18 | Real-time Communication |
| **[ADR-006](ADR-006-transactional-outbox-and-dlq.md)** | Transactional Outbox Pattern & Dead Letter Queue | Accepted | 2026-08-20 | Data Integrity & Eventual Consistency |
| **[ADR-007](ADR-007-distributed-redis-idempotency.md)** | Distributed Redis Idempotency on High-Value Mutations | Accepted | 2026-08-22 | Concurrency & Fault Tolerance |
| **[ADR-008](ADR-008-tier-based-token-bucket-rate-limiting.md)** | Multi-Tenant Dynamic Tier-Based Rate Limiting | Accepted | 2026-08-24 | Security & Fair Resource Allocation |
| **[ADR-009](ADR-009-gdpr-soc2-pii-log-masking.md)** | Zero-Leakage PII Masking in Observability Pipelines | Accepted | 2026-08-25 | Compliance (GDPR & SOC2) |
| **[ADR-010](ADR-010-deep-entity-graphs-and-jdbc-batching.md)** | Deep JPA Entity Graphs & Automated JDBC Statement Batching | Accepted | 2026-08-26 | Database Performance & Throughput |
