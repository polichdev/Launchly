# Launchly — Telegram Bot Builder, Multi-Channel CRM

[![Checks & Validation](https://github.com/polchduikt/launchly/actions/workflows/checks.yml/badge.svg)](https://github.com/polchduikt/launchly/actions/workflows/checks.yml)
[![Maintainability Rating](https://sonarcloud.io/api/project_badges/measure?project=polchduikt_launchly&metric=sqale_rating)](https://sonarcloud.io/summary/new_code?id=polchduikt_launchly)
[![Reliability Rating](https://sonarcloud.io/api/project_badges/measure?project=polchduikt_launchly&metric=reliability_rating)](https://sonarcloud.io/summary/new_code?id=polchduikt_launchly)
[![Security Rating](https://sonarcloud.io/api/project_badges/measure?project=polchduikt_launchly&metric=security_rating)](https://sonarcloud.io/summary/new_code?id=polchduikt_launchly)
[![Vulnerabilities](https://sonarcloud.io/api/project_badges/measure?project=polchduikt_launchly&metric=vulnerabilities)](https://sonarcloud.io/summary/new_code?id=polchduikt_launchly)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=polchduikt_launchly&metric=bugs)](https://sonarcloud.io/summary/new_code?id=polchduikt_launchly)
[![Duplicated Lines (%)](https://sonarcloud.io/api/project_badges/measure?project=polchduikt_launchly&metric=duplicated_lines_density)](https://sonarcloud.io/summary/new_code?id=polchduikt_launchly)
[![Tests: 700+ Passing](https://img.shields.io/badge/Tests-700%2B%20passing-brightgreen.svg)](docs/CONTRIBUTING.md)
[![Gitleaks: Clean](https://img.shields.io/badge/Gitleaks-clean-brightgreen.svg?logo=git)](https://github.com/gitleaks/gitleaks)
[![OpenAPI 3.0](https://img.shields.io/badge/OpenAPI-3.0_Spec-6BA539.svg?logo=openapiinitiative&logoColor=white)](docs/API_DOCUMENTATION.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

[![Java 21](https://img.shields.io/badge/Java-21-orange.svg?logo=openjdk&logoColor=white)](https://openjdk.org/projects/jdk/21/)
[![Spring Boot 4.0.6](https://img.shields.io/badge/Spring%20Boot-4.0.6-brightgreen.svg?logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791.svg?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-7-DC382D.svg?logo=redis&logoColor=white)](https://redis.io/)
[![Apache Kafka](https://img.shields.io/badge/Apache_Kafka-7.6-231F20.svg?logo=apachekafka&logoColor=white)](https://kafka.apache.org/)
[![Liquibase](https://img.shields.io/badge/Liquibase-Migrations-006699.svg?logo=liquibase&logoColor=white)](https://www.liquibase.com/)
[![WebSockets](https://img.shields.io/badge/WebSockets-STOMP-010101.svg?logo=socketdotio&logoColor=white)](https://docs.spring.io/spring-framework/reference/web/websocket.html)
[![React 19](https://img.shields.io/badge/React-19-61dafb.svg?logo=react&logoColor=black)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue.svg?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Tailwind CSS v4](https://img.shields.io/badge/Tailwind_CSS-v4-38b2ac.svg?logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![TanStack Query](https://img.shields.io/badge/TanStack_Query-v5-FF4154.svg?logo=reactquery&logoColor=white)](https://tanstack.com/query)
[![Zod](https://img.shields.io/badge/Zod-v4-3E67B1.svg?logo=zod&logoColor=white)](https://zod.dev/)
[![AI Multi-Router](https://img.shields.io/badge/AI-Multi--Router-8E75B2.svg?logo=googlegemini&logoColor=white)](docs/backend/decisions/README.md#adr-002-multi-provider-ai-fallback-router-architecture)
[![Stripe API](https://img.shields.io/badge/Stripe-API-635BFF.svg?logo=stripe&logoColor=white)](https://stripe.com/)
[![Playwright](https://img.shields.io/badge/Playwright-E2E_Tests-2EAD33.svg?logo=playwright&logoColor=white)](https://playwright.dev/)
[![Grafana k6](https://img.shields.io/badge/Grafana_k6-Load_Testing-7D64FF.svg?logo=k6&logoColor=white)](https://k6.io/)

[![Docker](https://img.shields.io/badge/Docker-Multi--Stage-2496ED.svg?logo=docker&logoColor=white)](https://www.docker.com/)
[![Nginx](https://img.shields.io/badge/Nginx-Reverse_Proxy-009639.svg?logo=nginx&logoColor=white)](https://nginx.org/)
[![Testcontainers](https://img.shields.io/badge/Testcontainers-Integration_Tests-0B1C28.svg)](https://testcontainers.com/)
[![Telegram Bot API](https://img.shields.io/badge/Telegram-Bot_API-26A5E4.svg?logo=telegram&logoColor=white)](https://core.telegram.org/bots/api)
[![Cloudflare Turnstile](https://img.shields.io/badge/Cloudflare-Turnstile-F38020.svg?logo=cloudflare&logoColor=white)](https://www.cloudflare.com/products/turnstile/)

---

## Overview

**Launchly** is a comprehensive, enterprise-grade SaaS platform for building, automating, and scaling Telegram bots, managing live customer support via an omnichannel CRM, executing targeted broadcast campaigns, and generating conversational flows with AI. It serves as a full-scale technical showcase of building resilient, high-concurrency monoliths using **Clean Architecture**, **Domain-Driven Design (DDD)** principles, and modern reactive frontend design.

<p align="center">
  <img src="docs/images/preview.gif" alt="Launchly Platform Overview" width="100%" />
</p>

## Tech Stack

### Backend — `backend/`
- **Java 21** with **Spring Boot 4.0.6** (Spring Framework 7.x)
- **Virtual Threads (Project Loom)**: High-throughput lightweight request execution
- **Clean Architecture & Package-by-Feature Modular Design**
- **PostgreSQL** (Relational data & native `JSONB` for graph storage)
- **Liquibase**: Zero-downtime database migrations (54 versioned changelogs)
- **Redis**: Distributed caching, token blacklist, tier rate limiting, and idempotency locks
- **Transactional Outbox & DLQ**: Resilient asynchronous event delivery with retry backoff and Dead Letter Queue
- **Distributed Idempotency Layer**: Redis-backed replay protection on all critical mutation endpoints
- **Dynamic Tier-Based Rate Limiter**: Token-bucket algorithm with RFC headers and role-based quotas
- **Cloudflare Turnstile Verify API**: Server-side bot verification and credential stuffing protection on auth endpoints
- **Enterprise Security Headers**: Strict HSTS, Content-Type Options, Frame Options, Referrer and Permissions policies
- **GDPR & SOC2 PII Masking**: Automatic redacting of emails, phone numbers, and secrets in runtime logs
- **JPA Deep Entity Graphs & JDBC Batching**: High-throughput statement batching (size=50) and N+1 query elimination
- **Spring Security 7 & JWT**: Stateless authentication with HMAC-SHA256 and Google OAuth2 SSO
- **Spring WebSockets & STOMP**: Low-latency bidirectional live chat streaming over SockJS
- **Multi-Provider AI Router**: Dynamic failover across **Groq**, **Google Gemini**, **OpenRouter**, and **Cerebras**
- **Cloudinary SDK**: Media processing, image transformations, and asset management
- **Stripe Java SDK**: Recurring subscriptions, webhooks, and tier limit verification
- **Resilience4j**: Circuit breakers, rate limiters, and retry pipelines
- **Testcontainers & JaCoCo**: Hermetic PostgreSQL container testing (part of 700+ automated test suite)

### Frontend — `frontend/`
- **React 19 + TypeScript + Vite 8**
- **@xyflow/react (React Flow)**: Interactive visual bot graph and node constructor
- **Tailwind CSS v4**: High-performance modern utility styling
- **TanStack Query v5**: Server state caching, background synchronization, and optimistic mutations
- **Zustand v5**: Client-only synchronous state (auth session, canvas selection, network health)
- **React Hook Form + Zod v4**: Strict type-safe form validation and DTO transformations
- **Cloudflare Turnstile Widget**: Non-blocking bot protection with dynamic theme and flexible sizing
- **React Router v7**: Nested dashboard layouts and role-based route guards
- **STOMP & SockJS Client**: Real-time live chat subscriptions
- **TanStack Virtual & Custom Virtualizer**: Scalable windowing for high-volume conversation and contact lists
- **DOMPurify**: Industrial-grade XSS sanitization and URL validation pipeline
- **BroadcastChannel & Storage Events**: Real-time multi-tab session synchronization
- **Axios Idempotency & AbortController Interceptors**: Automatic idempotency injection and query deduplication
- **Vitest & Testing Library**: Unit and component test suites with v8 coverage
- **Playwright**: End-to-end browser automation suite

### Load & Stress Testing — `load-tests/`
- **Grafana k6 + TypeScript**: 17 automated high-concurrency performance and stress test scenarios
- **Modular Test Harness**: Automated setup hooks, isolated multi-tenant VU pools, and HTML reporting dashboards

---

## Core Features

- **Visual Bot Constructor**: Drag-and-drop conversational graph builder supporting Message, Menu, Action, Condition, and AI nodes.
- **Omnichannel CRM & Live Inbox**: Two-way Telegram chat with live agent intervention, conversation search, and lead status pipelines.
- **Cloudflare Turnstile Bot & Spam Protection**: Smart non-interactive challenge verification securing authentication flows (registration and login) with zero friction.
- **Transactional Outbox & Integrations**: Guaranteed at-least-once delivery for webhooks (Hotmart, e-commerce, CRM leads) with automatic retry backoff and DLQ tracking.
- **Distributed Idempotency Protection**: Safe concurrent execution prevention for AI generation, campaign dispatches, team invites, and support appeals.
- **Dynamic Tier Rate Limiting**: Multi-tenant token-bucket rate limiting per plan tier (`ROLE_OWNER`, `ROLE_PRO`, `ROLE_ENTERPRISE`, `ROLE_ADMIN`).
- **AI-Powered Bot Generation**: Natural language prompt-to-bot generation with automatic JSON graph repair.
- **Scheduled Broadcast Engine**: Mass cohort notifications with rich media, interactive buttons, and delivery analytics.
- **Enterprise Security & Privacy**: Production security headers and zero-leakage GDPR/SOC2 PII log masking.
- **Granular RBAC & Team Management**: Multi-tier roles (`ROLE_SUPER_ADMIN`, `ROLE_OWNER`, `ROLE_ADMIN`, `ROLE_MANAGER`) with email invites.
- **Tiered Subscriptions & Stripe Billing**: Automatic plan quota enforcement (`PlanLimitService`) and customer portal management.
- **Community Templates Marketplace**: Shareable bot flow codes with one-click installation.

Full specification & feature matrix: [docs/FEATURES.md](docs/FEATURES.md)

---

## System Architecture & Patterns

This platform follows a decoupled Client-Server architecture.

### Backend Architecture
- **Clean Architecture Monolith**: Modular, package-by-feature layout strictly separating core domain logic, infrastructure adapters, and web controllers.
- **Project Loom Virtual Threads**: Java 21 virtual threads enabled across the entire servlet container for massive concurrent I/O throughput (Telegram webhooks, AI generation streams, and external API connectors).
- **Transactional Outbox Pattern**: Guaranteed at-least-once asynchronous event dispatching (CRM actions, external webhooks, Google Sheets exports) with a Dead Letter Queue (DLQ) retry mechanism.
- **Distributed Rate Limiting & Tier Throttling**: Bucket4j integrated with Redis for dynamic token-bucket rate limiting based on subscription tiers (Free, Pro, Enterprise) and IP reputations.
- **Idempotency & Distributed Locking**: Custom `@Idempotent` annotation backed by Redis distributed locks, eliminating duplicate execution risks for payment webhooks and critical state mutations.
- **JSONB Graph Persistence & Deep Entity Graphs**: High-speed conversational graph serialization in PostgreSQL with JPA `@EntityGraph` fetching to eliminate N+1 query overhead.
- **Optimistic Concurrency Control**: Hibernate `@Version` locking to protect concurrent schema updates and multi-operator CRM interactions from race conditions.
- **Automatic JDBC Batching**: Tuned batch sizing (`50`) and insert/update statement ordering for high-throughput CRM lead ingestion and bulk broadcasts.
- **Enterprise PII Masking**: Contextual Logback converters automatically sanitizing Telegram bot tokens, credentials, and customer emails across application log outputs.
- **Hermetic Integration Testing**: Containerized PostgreSQL Testcontainers ensuring exact production parity during automated CI/CD test runs.

Detailed backend architecture & ADRs: [docs/backend/ARCHITECTURE.md](docs/backend/ARCHITECTURE.md) | [docs/backend/decisions/README.md](docs/backend/decisions/README.md)

### Frontend Architecture
- **Layer-Based & Feature-Sliced**: Strict modular hierarchy dividing low-level UI primitives, REST API clients, custom React hooks, and domain-specific route views.
- **Interactive Visual Flow Builder**: Custom node-graph editor powered by `@xyflow/react` (React Flow), featuring real-time node validation, connection rules, and seamless JSON schema serialization.
- **Strict State Separation**: Server state cached and invalidated via TanStack Query v5; UI/client state (modals, auth sessions, canvas selection, network health) isolated in Zustand stores.
- **Real-Time Live Chat (STOMP / SockJS)**: Low-latency bidirectional WebSocket connection for live CRM messaging, operator typing indicators, and instant lead status updates.
- **Optimistic UI Updates**: Immediate client-side reflection of CRM messages, notes, lead states, tag assignments, and support tickets with automatic rollback on network failure.
- **Automatic Client Idempotency**: Transparent injection of UUID v4 `Idempotency-Key` headers on `POST`, `DELETE`, and `PATCH` requests, synchronized with backend replay-protection filters.
- **Race Condition Prevention & Request Cancellation**: Automated query deduplication and superseded search request abortion via native `AbortController` in Axios.
- **Zero-CLS Skeleton Screens**: Purpose-built Neo-Brutalist skeleton states maintaining layout stability (CLS = 0) during async data fetches.
- **Multi-Tab State Synchronization**: `BroadcastChannel` event-driven architecture synchronizing auth sessions, active bot context, and logout triggers across concurrent browser tabs.
- **Defensive XSS Pipeline**: Deep DOMPurify sanitization and safe link filtering on all dynamic markdown and HTML solutions.
- **Real-Time Network & WebSocket Health**: Sticky connection banner monitoring browser connectivity and STOMP session lifecycles with debounced auto-reconnection.
- **Silent JWT Refresh Interceptor**: Axios queue mechanism ensuring transparent access token rotation on HTTP 401 without disrupting in-flight operations or prompting re-login.
- **Type-Safe Validation & Form Pipelines**: Unified runtime and compile-time validation powered by Zod v4 and React Hook Form across all bot configurations and integration settings.
- **Role & Tier-Aware Route Protection**: Granular client-side route guards restricting capabilities based on active user roles (`OWNER`, `OPERATOR`, `ADMIN`) and subscription tier limits.

Detailed frontend architecture & ADRs: [docs/frontend/ARCHITECTURE.md](docs/frontend/ARCHITECTURE.md) | [docs/frontend/decisions/README.md](docs/frontend/decisions/README.md)

### Code Quality & Tooling
- **Code Duplication Protection**: Enforces a strict maximum of **<= 6% code duplication** via `jscpd` across all Java backend and TypeScript/TSX frontend files, validated in CI.
- **SonarCloud & JaCoCo**: Continuous inspection of code quality, security vulnerabilities, reliability rating, and test coverage on every push.
- **Gitleaks Secret Scanning**: Automated secret scanning on every pull request and push to prevent credential leakage.
- **Grafana k6 Load Testing**: 17 stress test scenarios validating SLAs, sub-millisecond query performance, and rate limit enforcement under 1,000+ RPS.

---

## Testing & Code Quality

The platform undergoes rigorous automated testing and static analysis across both backend and frontend, boasting over **700+ automated tests** and **17 k6 load scenarios**:

- **700+ Automated Full-Stack Tests**: Complete test coverage spanning 389+ Spring Boot JUnit 5 & PostgreSQL Testcontainers integration/unit tests alongside 310+ Vitest, React Testing Library, and Playwright E2E tests.
- **17 k6 Load & Stress Test Scenarios**: High-concurrency performance verification across Authentication, Telegram Webhooks, CRM Pipelines, AI Streaming, Transactional Outbox Ingress, JDBC Batching, Tier Rate Limits, and Deep Entity Graphs.
- **JaCoCo Coverage**: Automated backend code coverage analysis reporting to SonarCloud.
- **Vitest & Testing Library**: Component lifecycle, state mutations, and integration tests with v8 code coverage.
- **ESLint & TypeScript Compiler**: Zero-warning strict typechecking gate.
- **Docker Validation**: Multi-stage production container build validation in CI.

To run tests locally:

```bash
# Run backend tests with Testcontainers
cd backend
mvn clean verify -B

# Run frontend test suite
cd ../frontend
npm run test:coverage
```

---

## Repository Structure

```
launchly/
├── .github/
│   └── workflows/
│       ├── checks.yml               # CI: Test, JaCoCo, SonarCloud, Lint, Docker build
│       └── deploy.yml               # CD: Build and push production images to GHCR
├── backend/
│   ├── src/main/java/com/launchly/  # Modular domain features (auth, bot, crm, ai, billing)
│   ├── src/main/resources/          # Liquibase migrations, application.properties
│   ├── src/test/                    # 389+ Backend JUnit 5 & Testcontainers integration tests
│   └── Dockerfile                   # Multi-stage production Java 21 build
├── frontend/
│   ├── src/                         # React 19 SPA (components, pages, store, hooks, router)
│   ├── nginx.conf                   # High-performance SPA reverse proxy config
│   └── Dockerfile                   # Multi-stage Nginx static container build
├── docs/
│   ├── backend/                     # Backend architecture specifications & ADRs
│   │   ├── API_SURFACE.md           # Complete REST & WebSocket API specification
│   │   ├── ARCHITECTURE.md          # Backend architecture & request lifecycle
│   │   └── decisions/               # Backend Architecture Decision Records (ADRs)
│   ├── frontend/                    # Frontend architecture specifications & ADRs
│   ├── deployment/                  # Production Docker & CI/CD deployment guide
│   ├── API_KEYS_GUIDE.md            # Third-party credentials & API guide
│   ├── CONTRIBUTING.md              # Commit conventions & pull request guidelines
│   ├── DATA_MODEL.md                # PostgreSQL schema & Entity Relationship diagrams
│   ├── DEV_SETUP.md                 # Step-by-step local development guide
│   ├── FEATURES.md                  # Comprehensive platform feature matrix
│   └── TECH_DEBT.md                 # Technical debt tracking & maintenance log
├── docker-compose.yml               # Local infrastructure (PostgreSQL, Redis)
└── docker-compose.prod.yml          # Production multi-container stack
```

---

## Running Locally

Detailed setup instructions, including environment variables and third-party API key configurations, can be found in the documentation:

[Local Development Setup Guide (docs/DEV_SETUP.md)](docs/DEV_SETUP.md)

### Option 1: Hybrid Development (Recommended)

Run PostgreSQL & Redis in Docker, and run Backend & Frontend locally on your host with hot-reloading:

```bash
# 1. Start infrastructure containers
docker compose up -d postgres redis

# 2. Start Backend API
cd backend
mvn spring-boot:run

# 3. Start Frontend SPA
cd ../frontend
npm install
npm run dev
```

### Option 2: Full Docker Stack

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

### Available Local Endpoints:
- **Frontend Client**: [http://localhost:5173](http://localhost:5173) (or `http://localhost:80` in Docker)
- **Backend REST API**: [http://localhost:8080/api/v1](http://localhost:8080/api/v1)
- **Swagger / OpenAPI Documentation**: [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)
- **PostgreSQL Database**: `localhost:5432`

---

## Documentation Index

All architectural choices, setup guides, and project specifications are documented in the `docs/` directory:

- [docs/DEV_SETUP.md](docs/DEV_SETUP.md) — Step-by-step local setup checklist.
- [docs/## Engineering & Architecture Specifications

Launchly maintains an extensive suite of senior-grade architectural specifications, threat models, failure runbooks, and protocol standards:

### Core Systems Specifications
- [Repository Structure (docs/STRUCTURE.md)](docs/STRUCTURE.md) — Monorepo design, package-by-feature layout, and architectural boundary rules.
- [Threat Model & Security Architecture (docs/THREAT_MODEL.md)](docs/THREAT_MODEL.md) — STRIDE security model, Turnstile verification, and trust boundaries.
- [Concurrency & Resource Architecture (docs/CONCURRENCY.md)](docs/CONCURRENCY.md) — Java 21 Project Loom virtual threads, carrier thread pinning prevention, and connection pools.
- [Failure Modes & Recovery Runbook (docs/FAILURES_AND_RECOVERY.md)](docs/FAILURES_AND_RECOVERY.md) — Multi-provider AI fallback ladder, Telegram 429 backoff, and Outbox DLQ re-drive.
- [Privacy, GDPR & Compliance (docs/PRIVACY.md)](docs/PRIVACY.md) — GDPR compliance, automated PII log scrubbing, retention policies, and data erasure.

### Formal Protocols & Subsystems
- [Bot Graph JSON AST Specification (docs/specs/BOT_GRAPH_SCHEMA.md)](docs/specs/BOT_GRAPH_SCHEMA.md) — Node definitions, edge transitions, and variable execution context.
- [STOMP WebSocket Live Chat Protocol (docs/specs/WEBSOCKET_PROTOCOL.md)](docs/specs/WEBSOCKET_PROTOCOL.md) — Full-duplex STOMP framing, topic registry, and heartbeat recovery.
- [Bot Flow Execution Engine (docs/modules/BOT_ENGINE.md)](docs/modules/BOT_ENGINE.md) — Polymorphic node executors, webhook dispatching, and call stack manager.
- [Omnichannel CRM & Live Inbox (docs/modules/CRM_INBOX.md)](docs/modules/CRM_INBOX.md) — Two-way chat streaming, operator takeover protocol, and lead pipelines.
- [Multi-Provider AI Fallback Router (docs/modules/AI_ROUTER.md)](docs/modules/AI_ROUTER.md) — Dynamic failover across Groq, Gemini, OpenRouter, and Cerebras with circuit breakers.
- [Broadcast Campaigns Subsystem (docs/modules/BROADCAST_CAMPAIGNS.md)](docs/modules/BROADCAST_CAMPAIGNS.md) — High-volume messaging, Telegram 30 msg/s rate throttling, and JDBC batch staging.

### Architecture Decision Records (ADRs)
- [Architecture Decision Log (docs/adr/README.md)](docs/adr/README.md) — Standardized Nygard ADRs (ADR-001 through ADR-010).

All architectural choices, setup guides, and project specifications are documented in the `docs/` directory:

- [docs/README.md](docs/README.md) — Master documentation index and navigation hub.
- [docs/DEV_SETUP.md](docs/DEV_SETUP.md) — Step-by-step local setup checklist.
- [docs/API_KEYS_GUIDE.md](docs/API_KEYS_GUIDE.md) — External services & API keys configuration guide.
- [docs/FEATURES.md](docs/FEATURES.md) — Full feature specification & platform capabilities.
- [docs/DATA_MODEL.md](docs/DATA_MODEL.md) — Database ER diagram & table specifications.
- [docs/backend/API_SURFACE.md](docs/backend/API_SURFACE.md) — Complete REST & WebSocket API specification.
- [docs/backend/ARCHITECTURE.md](docs/backend/ARCHITECTURE.md) — Backend architecture & request lifecycles.
- [docs/frontend/ARCHITECTURE.md](docs/frontend/ARCHITECTURE.md) — Frontend architecture & state design.
- [docs/deployment/DEPLOYMENT.md](docs/deployment/DEPLOYMENT.md) — Production Docker & CI/CD deployment guide.
- [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md) — Repository commit conventions & PR guidelines.
- [docs/TECH_DEBT.md](docs/TECH_DEBT.md) — Technical debt & refactoring plans.
- [CHANGELOG.md](CHANGELOG.md) — Versioned changelog following Keep a Changelog.
- [SECURITY.md](SECURITY.md) — Security policy and vulnerability disclosure SLA.
- [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) — Contributor Covenant v2.1.

---

## Status

Launchly is actively maintained, thoroughly tested, and continuously updated with new features, integrations, and architectural enhancements.

---

## License

This project is licensed under the MIT License.
