# Launchly Technology Inventory & Technical Constraints

Comprehensive catalog of runtime dependencies, frameworks, architectural constraints, and library versions across the Launchly platform.

---

## 1. Backend Core Inventory

| Technology | Version / Specification | Architectural Role | Constraints & Rules |
|---|---|---|---|
| **Java** | 21 LTS (OpenJDK 64-bit) | Language runtime | Enforce modern Java 21 syntax (Records, Pattern Matching, Virtual Threads). |
| **Spring Boot** | 4.0.6 (Spring Framework 7.x) | Core application framework | Avoid WebFlux reactive dependencies. Rely on Loom virtual threads. |
| **Virtual Threads** | Project Loom | Concurrency runtime | **Invariant**: Never perform blocking I/O inside `synchronized` blocks. Use `ReentrantLock`. |
| **PostgreSQL** | 16.x | Primary relational store | Native `JSONB` for flow schemas and variables. GIN indexes on graph paths. |
| **Liquibase** | 4.x | Database schema migrations | Zero manual DDL. All changes versioned in `db/changelog/`. |
| **Redis** | 7.x | Distributed cache & locks | Used for rate limiting, token blacklist, idempotency locks, and session data. |
| **Spring Security** | 7.x | Identity & Access Control | Stateless JWT. `@PreAuthorize` method security for RBAC. |
| **Spring WebSockets** | STOMP over SockJS | Real-time live chat | Standard destinations under `/topic/crm/` and `/app/`. |
| **Resilience4j** | 2.x | Fault tolerance | Circuit breaker + retry wrappers on all external HTTP clients. |
| **Testcontainers** | 1.20+ | Integration testing | All integration tests inherit `BaseIntegrationTest` using PostgreSQL container. |

---

## 2. Frontend Core Inventory

| Technology | Version / Specification | Architectural Role | Constraints & Rules |
|---|---|---|---|
| **React** | 19.x | UI library | Functional components with hooks; strict avoidance of legacy class components. |
| **Vite** | 8.x | Frontend build tool | Optimized ES module bundling and fast HMR. |
| **TypeScript** | 5.x | Type safety | Strict mode enabled (`noImplicitAny`, `strictNullChecks`). |
| **@xyflow/react** | React Flow 12+ | Visual node canvas | Graph state decoupled from backend persistence via DTO mappers. |
| **Tailwind CSS** | v4 | Modern utility styling | Utility-first responsive styling without external CSS frameworks. |
| **TanStack Query** | v5 | Server state cache | Optimistic mutations for chat messages and flow updates. |
| **Zustand** | v5 | Client state store | Sync state only: active session, modal toggles, canvas viewport. |
| **DOMPurify** | 3.x | Security sanitizer | All customer message HTML/Markdown must pass DOMPurify before render. |
| **Playwright** | 1.x | E2E browser automation | End-to-end tests in `frontend/tests/e2e/`. |

---

## 3. External SaaS & API Dependencies

| Provider / API | Purpose | Integration Pattern |
|---|---|---|
| **Telegram Bot API** | Primary messaging channel | Webhook ingestion + Outbox dispatch with 30 msg/s rate throttling. |
| **Stripe Java SDK** | Subscriptions & payments | Webhook verification via `Stripe-Signature` + Idempotency keys. |
| **Cloudflare Turnstile** | Invisible anti-bot defense | Server-side verification of client tokens on `/api/auth/**`. |
| **Groq API** | Primary fast LLM inference | LPU-accelerated `llama-3.3-70b-versatile` HTTP client. |
| **Google Gemini API** | Secondary fallback LLM | Google AI REST client with enterprise SLA. |
| **Cloudinary** | Media processing & CDN | Direct signed uploads and dynamic image transformations. |
