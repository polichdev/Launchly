# ADR-010: Deep Entity Graphs & Automatic JDBC Batching for Bulk Operations

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-08-26 |
| Deciders | Database Architect, Backend Team |

---

## Context

Complex bot configurations involve deep relationships (Bot $\to$ FlowSchema, Bot $\to$ Members, Bot $\to$ ModerationRules, Bot $\to$ Tags). Standard JPA queries generated notorious N+1 `SELECT` problems, causing hundreds of queries for a single dashboard page load. Furthermore, bulk CRM lead imports and high-volume mass broadcasts generated separate `INSERT` statements for each record, exhausting HikariCP connection pools.

## Decision

We enforced **JPA Entity Graphs and Hibernate JDBC Batching**:
1. **Zero-N+1 Retrieval**: Critical composite queries employ `@EntityGraph(attributePaths = {...})` or explicit `JOIN FETCH` queries, ensuring single-query entity hydration.
2. **Global Statement Batching**: Configured in `application.properties`:
   - `spring.jpa.properties.hibernate.jdbc.batch_size=50`
   - `spring.jpa.properties.hibernate.order_inserts=true`
   - `spring.jpa.properties.hibernate.order_updates=true`
   - `spring.jpa.properties.hibernate.jdbc.batch_versioned_data=true`
3. **Open-in-View Disabled**: Enforced `spring.jpa.open-in-view=false` to prevent lazy-loading database transactions inside web controllers.

## Consequences

### Positive
- **Drastic Latency Reduction**: p95 database query times dropped from ~45ms to < 4ms.
- **Bulk Insert Throughput**: Mass broadcasts insert 5,000 delivery records in under 200ms using JDBC batch packets.

### Negative / Trade-offs
- Developers must explicitly declare `@EntityGraph` for composite queries to prevent `LazyInitializationException`.
