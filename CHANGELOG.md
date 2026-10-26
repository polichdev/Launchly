# Changelog

All notable changes to the **Launchly** platform will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Added
- Enterprise engineering documentation hub: STRIDE Threat Model (`docs/THREAT_MODEL.md`), Loom Concurrency Model (`docs/CONCURRENCY.md`), Failure Modes & Recovery Runbook (`docs/FAILURES_AND_RECOVERY.md`), and GDPR/Privacy Spec (`docs/PRIVACY.md`).
- Formalized Architecture Decision Records (`docs/adr/ADR-001` through `ADR-010`).
- Bot Graph JSON AST specification (`docs/specs/BOT_GRAPH_SCHEMA.md`) and WebSocket STOMP wire format (`docs/specs/WEBSOCKET_PROTOCOL.md`).

---

## [1.2.0] - 2026-09-15

### Added
- **Multi-Provider AI Fallback Router**: Dynamic cascading failover supporting Groq (`llama-3.3-70b`), Google Gemini 1.5/2.0, OpenRouter, and Cerebras inference engines with circuit breaker recovery.
- **Transactional Outbox & DLQ**: Exactly-once delivery guarantee for Telegram notifications and webhooks with retry backoff and Dead Letter Queue management.
- **Cloudflare Turnstile Bot Defense**: Seamless invisible bot verification across user registration and login endpoints.
- **Real-Time Live Chat CRM**: STOMP over WebSocket SockJS channel `/topic/crm/{botId}/messages` with unread counts and operator takeover.

### Changed
- Migrated backend runtime to **Java 21 Project Loom** with virtual threads enabled (`spring.threads.virtual.enabled=true`).
- Enhanced PostgreSQL query throughput with JDBC statement batching (`batch_size=50`) and deep JPA Entity Graphs.
- Upgraded frontend UI engine to **React 19**, Vite 8, and Tailwind CSS v4.

### Fixed
- Fixed carrier thread pinning risks by replacing synchronized blocks with `ReentrantLock` in high-throughput channels.
- Resolved race conditions in broadcast campaign scheduling using Redis distributed locks.

---

## [1.1.0] - 2026-08-01

### Added
- Visual drag-and-drop conversational graph constructor powered by `@xyflow/react`.
- Multi-tier dynamic rate limiting using Redis token bucket algorithm with standard RFC rate limit headers.
- GDPR-compliant automated PII masking filter for runtime application logs.
- Stripe subscription integration with automatic tier limits and webhook signature verification.

### Security
- Integrated **Gitleaks** and **SonarCloud Quality Gate** into GitHub Actions CI pipeline.
- Enforced strict Content Security Policy (CSP), HSTS, and X-Content-Type-Options headers.

---

## [1.0.0] - 2026-06-01

### Added
- Initial production release of Launchly SaaS platform.
- Telegram Bot API webhook and long-polling connection managers.
- Multi-tenant workspace RBAC model (`OWNER`, `ADMIN`, `MEMBER`, `AGENT`).
- PostgreSQL schema baseline with Liquibase migrations.
