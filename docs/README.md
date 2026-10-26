# Launchly — Enterprise Documentation Hub

Master engineering documentation and architectural specifications for the **Launchly** platform.

---

## 1. System Engineering & Reliability Specifications

Comprehensive engineering documents detailing security, concurrency, failure modes, data privacy, and monorepo structure:

| Document | Focus & Highlights |
|---|---|
| **[Repository Structure](STRUCTURE.md)** | Monorepo layout, package-by-feature modular design, and architectural boundary rules. |
| **[Threat Model & Security](THREAT_MODEL.md)** | STRIDE security analysis, trust boundaries, Turnstile anti-bot verification, and webhook signatures. |
| **[Concurrency & Resource Architecture](CONCURRENCY.md)** | Java 21 Project Loom virtual threads, carrier thread pinning prevention, HikariCP sizing, and Redis locks. |
| **[Failure Modes & Recovery Runbook](FAILURES_AND_RECOVERY.md)** | Multi-provider AI fallback ladder, Telegram 429 throttling, Outbox DLQ re-drive, and Redis degradation. |
| **[Privacy, GDPR & Compliance](PRIVACY.md)** | GDPR & SOC 2 controls, automated PII log masking, retention policies, and Right to be Forgotten. |

---

## 2. Technical Specifications & Protocols

Formal wire protocols, JSON schemas, and integration contracts:

- **[Bot Graph JSON AST Specification](specs/BOT_GRAPH_SCHEMA.md)** — Node schemas, edge routes, variable contexts, and loop execution guards.
- **[STOMP WebSocket Protocol](specs/WEBSOCKET_PROTOCOL.md)** — Destination topics (`/topic/crm/...`), frame wire formats, and heartbeat negotiation.
- **[API Surface Specification](backend/API_SURFACE.md)** — Full catalog of REST endpoints and WebSocket endpoints.
- **[Data Model & Schema ERD](DATA_MODEL.md)** — PostgreSQL 16 entity-relationship diagrams and Liquibase migration models.

---

## 3. Subsystem Technical Deep-Dives

Detailed architectural and implementation documentation for each core subsystem:

- **[Bot Flow Execution Engine](modules/BOT_ENGINE.md)** — Polymorphic node executors, Telegram update dispatching, and call stack management.
- **[Omnichannel CRM & Live Inbox](modules/CRM_INBOX.md)** — Full-duplex STOMP messaging, operator takeover protocol, and lead stage pipelines.
- **[Multi-Provider AI Fallback Router](modules/AI_ROUTER.md)** — Dynamic failover ladder (Groq $\to$ Gemini $\to$ OpenRouter $\to$ Cerebras) with circuit breakers.
- **[Broadcast Campaigns & Mass Messaging](modules/BROADCAST_CAMPAIGNS.md)** — Audience segmentation, Telegram 30 msg/s rate throttling, and JDBC batch staging.

---

## 4. Architecture Decision Records (ADRs)

Standardized architectural decisions capturing context, decision rationale, and evaluated alternatives:

- **[ADR Index & Log](adr/README.md)**
  - [ADR-001: PostgreSQL with JSONB for Flow Graphs](adr/ADR-001-postgresql-jsonb-bot-graph.md)
  - [ADR-002: Multi-Provider AI Fallback Router Architecture](adr/ADR-002-multi-provider-ai-fallback-router.md)
  - [ADR-003: Adoption of Java 21 Virtual Threads (Loom)](adr/ADR-003-java-21-virtual-threads-loom.md)
  - [ADR-004: Hermetic Testing with Testcontainers](adr/ADR-004-testcontainers-hermetic-testing.md)
  - [ADR-005: STOMP over WebSocket for Live CRM](adr/ADR-005-websocket-stomp-live-crm-inbox.md)
  - [ADR-006: Transactional Outbox Pattern & Dead Letter Queue](adr/ADR-006-transactional-outbox-and-dlq.md)
  - [ADR-007: Distributed Redis Idempotency on High-Value Mutations](adr/ADR-007-distributed-redis-idempotency.md)
  - [ADR-008: Multi-Tenant Tier-Based Token-Bucket Rate Limiting](adr/ADR-008-tier-based-token-bucket-rate-limiting.md)
  - [ADR-009: Logback GDPR & SOC2 Zero-Leakage PII Masking](adr/ADR-009-gdpr-soc2-pii-log-masking.md)
  - [ADR-010: Deep Entity Graphs & JDBC Statement Batching](adr/ADR-010-deep-entity-graphs-and-jdbc-batching.md)

---

## 5. Operations, Deployment & Guidelines

- **[Local Development Setup](DEV_SETUP.md)** — Docker, Java 21, and Vite setup guide.
- **[Production Deployment Runbook](deployment/DEPLOYMENT.md)** — Multi-stage Docker, Nginx SSL reverse proxy, and systemd daemons.
- **[Third-Party API Credentials Guide](API_KEYS_GUIDE.md)** — Configuring Telegram, Stripe, Cloudinary, Groq, and Gemini keys.
- **[Contributing Guidelines](CONTRIBUTING.md)** — Commit standards, branch workflows, and PR checklists.
- **[Technical Debt Registry](TECH_DEBT.md)** — Tracked refactoring milestones and optimization targets.
