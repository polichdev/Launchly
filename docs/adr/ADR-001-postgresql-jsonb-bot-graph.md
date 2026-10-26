# ADR-001: Use PostgreSQL with JSONB for Flow Graphs & Schema State

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-07-10 |
| Deciders | Backend Team, System Architects |
| Consulted | Frontend Team |

---

## Context

Launchly allows users to construct visual conversational Telegram bots using a canvas editor (`@xyflow/react`). The conversational graph comprises an arbitrary number of nodes (Message, Menu, Condition, Action, AI, Delay) and directional edges connecting them. Each node carries type-specific payloads, nested configuration, media references, and custom validation rules.

A purely normalized relational design would require splitting nodes, attributes, and edges into multiple tables (`nodes`, `node_attributes`, `edges`, `node_actions`), leading to deep `JOIN` operations, complex transaction locking, and difficult schema migrations whenever a new node type is introduced. Conversely, a pure NoSQL database (e.g., MongoDB) would sacrifice relational integrity across users, workspaces, billing plans, and audit trails.

## Decision

We chose a **Hybrid Relational + JSONB architecture** within **PostgreSQL 16**:
1. Core business domains (`workspaces`, `bots`, `bot_users`, `crm_messages`, `orders`) remain strictly relational with ACID guarantees and foreign keys.
2. The dynamic conversational graph schema is stored within the `flow_schemas` table using PostgreSQL native `JSONB` columns for `nodes` and `edges`.
3. GIN indexes (`CREATE INDEX idx_flow_nodes ON flow_schemas USING gin (nodes)`) are utilized where path lookups or specific node query attributes are required.

## Consequences

### Positive
- **Schema Agility**: Introducing new node types or frontend visual metadata requires zero database migration scripts.
- **Single-Query Hydration**: The entire graph canvas can be fetched or persisted in a single SQL operation.
- **Transactional Consistency**: Changes to flow schemas commit atomically alongside bot metadata within standard JPA transactions.

### Negative / Trade-offs
- **Application-Level Validation**: Database constraints cannot validate deeply nested JSON structures; validation must be strictly enforced via backend JSON Schema validators (`FlowSchemaValidator`).
