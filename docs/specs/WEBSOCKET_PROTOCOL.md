# Specification: STOMP Real-Time Live Chat Protocol

| Metadata | Value |
|---|---|
| Target Subsystem | `backend/com/launchly/crm/websocket/` & `frontend/hooks/` |
| Transport Layer | WebSocket (RFC 6455) / SockJS Fallback |
| Subprotocol | STOMP (Simple Text Oriented Messaging Protocol) v1.2 |
| Handshake URL | `https://api.launchly.app/ws` |

---

## 1. Handshake & Connection Flow

Clients establish a full-duplex session using STOMP over WebSocket or SockJS:

```mermaid
sequenceDiagram
    autonumber
    actor Client as Agent Browser (React 19)
    participant Nginx as Nginx (Proxy Headers)
    participant Spring as Spring STOMP Broker

    Client->>Nginx: GET /ws (Upgrade: websocket)
    Nginx->>Spring: Forward Upgrade
    Spring-->>Client: 101 Switching Protocols
    Client->>Spring: CONNECT (heart-beat: 10000,10000; Authorization: Bearer JWT)
    Spring-->>Client: CONNECTED (version: 1.2, heart-beat: 10000,10000)
    Client->>Spring: SUBSCRIBE /topic/crm/{botId}/messages
```

---

## 2. Topic Destination Registry

| Destination Pattern | Direction | Payload Type | Description |
|---|---|---|---|
| `/topic/crm/{botId}/messages` | Server $\to$ Client | `MessageResponse` | Real-time inbound customer and outbound agent messages. |
| `/topic/crm/{botId}/orders` | Server $\to$ Client | `OrderResponse` | E-commerce orders placed inside bot funnels. |
| `/topic/crm/{botId}/leads` | Server $\to$ Client | `LeadResponse` | New leads captured, status changes, and tag updates. |
| `/app/chat/send` | Client $\to$ Server | `SendMessageRequest` | Live agent responds directly to Telegram user. |
| `/app/chat/typing` | Client $\to$ Server | `AgentTypingPayload` | Emits typing indicator to Telegram chat. |

---

## 3. Frame Wire Format Examples

### 3.1 Inbound Message Broadcast (`/topic/crm/{botId}/messages`)

```text
MESSAGE
subscription:sub-0
message-id:msg-98421-42
destination:/topic/crm/12/messages
content-type:application/json

{
  "id": 98421,
  "botId": 12,
  "botUserId": 45102,
  "telegramChatId": 89234812,
  "sender": "CUSTOMER",
  "text": "Hello, I need help with my recent payment.",
  "mediaType": "NONE",
  "mediaUrl": null,
  "createdAt": "2026-10-26T18:15:00.000Z",
  "status": "DELIVERED"
}
```

### 3.2 Lead Lifecycle Update (`/topic/crm/{botId}/leads`)

```text
MESSAGE
subscription:sub-1
message-id:lead-7718-01
destination:/topic/crm/12/leads
content-type:application/json

{
  "id": 7718,
  "botId": 12,
  "botUserId": 45102,
  "name": "Jane Smith",
  "username": "janesmith",
  "pipelineStage": "QUALIFIED",
  "leadScore": 85,
  "tags": ["VIP", "Stripe-Customer"],
  "updatedAt": "2026-10-26T18:15:30.000Z"
}
```

---

## 4. Heartbeats & Disconnect Recovery

1. **Heartbeat Negotiation**: Default configuration specifies heartbeats every 10,000ms (`10000,10000`).
2. **Missing Heartbeats**: If no heartbeat frame is observed for 25,000ms, the client initiates reconnection.
3. **Exponential Backoff**: Frontend `useStompClient` reconnects with intervals of 1s, 2s, 5s, 10s up to a maximum of 30s.
4. **Offline Synchronization**: Upon reconnection, the client queries `/api/crm/messages?since={lastTimestamp}` to reconcile missed messages.
