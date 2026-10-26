# ADR-005: STOMP over WebSocket Protocol for Live CRM Inbox

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-08-18 |
| Deciders | Fullstack Lead, Backend Team |

---

## Context

Customer support agents monitoring the Launchly CRM dashboard require real-time visibility into customer conversations, order notifications, and lead score updates. HTTP polling introduces severe database overhead and latency, while Server-Sent Events (SSE) only support unidirectional server-to-client streaming without standardized topic subscription abstractions.

## Decision

We adopted **STOMP (Simple Text Oriented Messaging Protocol) over Spring WebSockets with SockJS**:
1. WebSocket endpoint registered at `/ws` with SockJS fallback support (`WebSocketConfig`).
2. In-memory simple message broker configured with `/topic` prefix for fan-out broadcasts:
   - `/topic/crm/{botId}/messages` — Inbound and outbound Telegram messages.
   - `/topic/crm/{botId}/orders` — E-commerce store purchases.
   - `/topic/crm/{botId}/leads` — Lead qualification updates.
3. Client application destination prefix set to `/app` for bidirectional agent actions.

## Consequences

### Positive
- **Standardized Framing**: STOMP frames (`SUBSCRIBE`, `UNSUBSCRIBE`, `SEND`) provide native pub/sub ergonomics.
- **Low Overhead**: Eliminates constant HTTP polling; messages arrive sub-100ms.
- **Graceful Fallback**: SockJS ensures connectivity even through restrictive corporate firewalls or proxies.

### Negative / Trade-offs
- State synchronization during network dropouts requires client-side message deduplication.
