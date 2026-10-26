# ADR-003: Adoption of Java 21 Virtual Threads (Project Loom)

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-08-01 |
| Deciders | Backend Team, DevOps |

---

## Context

Launchly's backend architecture is heavily I/O-bound:
- Inbound Telegram Bot API webhooks and continuous user chats.
- Remote HTTP calls to Stripe, Cloudinary, and external LLM APIs (Groq, Gemini).
- Real-time STOMP WebSocket connections for CRM live agents.

In traditional Spring Boot applications, Tomcat assigns one heavy OS platform thread (~1 MB RAM) per incoming HTTP connection. With standard pools capped at 200 threads, concurrent slow external API calls easily result in thread pool starvation, causing 504 Gateway Timeouts for all users. The alternative—reactive programming with Spring WebFlux—introduces immense cognitive complexity, non-intuitive debugging stacks, and loss of `ThreadLocal` context (SecurityContext, Transactional boundaries).

## Decision

We enabled **Java 21 Project Loom Virtual Threads**:
1. Enabled via Spring Boot property: `spring.threads.virtual.enabled=true`.
2. Tomcat dispatches each HTTP request and WebSocket interaction onto a dedicated virtual thread.
3. HikariCP connection pool remains bounded (`maximum-pool-size=20-50`) to prevent overwhelming PostgreSQL.
4. Audited and replaced blocking `synchronized` blocks with `ReentrantLock` in critical paths to eliminate carrier thread pinning risks.

## Consequences

### Positive
- **Massive Concurrency**: Ability to handle 10,000+ concurrent connections without pool exhaustion.
- **Maintainable Imperative Code**: Clean synchronous code style preserves `try-catch`, standard stack traces, and standard JPA `@Transactional` semantics.

### Negative / Trade-offs
- Requires discipline regarding thread pinning: blocking I/O must never occur inside `synchronized` blocks.
- ThreadLocal state must be carefully managed to avoid memory retention in long-lived tasks.
