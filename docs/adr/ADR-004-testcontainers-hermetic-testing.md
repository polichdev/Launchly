# ADR-004: Hermetic Integration Testing with Testcontainers

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-08-15 |
| Deciders | QA Lead, Backend Team |

---

## Context

Previous iterations relied on in-memory H2 databases for running unit and integration tests in CI. However, H2 lacks full parity with PostgreSQL 16:
- Native `JSONB` operators (`->`, `->>`, `@>`) and GIN indexes fail or behave differently under H2.
- Liquibase migration scripts using PostgreSQL-specific syntax broke in staging environments despite passing H2 CI tests.
- Mocking database interactions in repository tests created a false sense of test coverage.

## Decision

We mandated **Testcontainers for all integration test suites**:
1. Integration test classes inherit from `BaseIntegrationTest`.
2. A single, reused `PostgreSQLContainer` container runs via Docker during test execution using Spring Boot 3.1+ `@ServiceConnection`.
3. Liquibase executes the entire 54-migration changelog against the real container instance prior to test execution.

## Consequences

### Positive
- **100% Production Parity**: Eliminates subtle SQL dialect bugs between dev/CI and production.
- **Reliable Verification**: Tests run against real PostgreSQL JSONB indexing and constraints.

### Negative / Trade-offs
- Requires Docker daemon availability in local environments and GitHub Actions runners.
- Slightly higher test suite startup time (~5-10s initial container cold start).
