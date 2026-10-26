# ADR-002: Multi-Provider AI Fallback Router Architecture

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-07-25 |
| Deciders | Backend Team, AI Infrastructure Lead |

---

## Context

Conversational bot generation and runtime AI nodes depend on external Large Language Model APIs. Individual AI inference providers regularly experience temporary availability dips (HTTP 503), quota rate-limiting (HTTP 429), or elevated p99 latency spikes during peak hours. Binding Launchly to a single provider (e.g., OpenAI or Groq) exposes user bots to downtime outside of our SLA control.

## Decision

We designed and implemented a **Multi-Provider AI Fallback Router** (`AiFallbackRouterService`):
1. **Fallback Priority Chain**:
   - Primary: **Groq** (`llama-3.3-70b-versatile` via Groq LPU — sub-500ms time to first token).
   - Secondary: **Google Gemini** (Gemini 1.5 Flash / 2.0 Flash via REST API — high concurrency capacity).
   - Tertiary: **OpenRouter** (Unified aggregator routing to backup models).
   - Quaternary: **Cerebras** (Ultra-fast wafer-scale inference engine).
2. **Circuit Breaking & Throttling**: Each provider is wrapped with a Resilience4j circuit breaker to avoid hammering failing providers.
3. **Graceful Fallback**: If all external providers fail, the bot engine emits an intelligible fallback message rather than crashing the flow.

## Consequences

### Positive
- **Fault-Tolerant AI Execution**: Transparent failover prevents end-user disruption during upstream outages.
- **Cost & Latency Optimization**: Groq provides ultra-fast cheap inference as the primary engine; larger fallback providers absorb overflow.

### Negative / Trade-offs
- Slight behavioral variance between models across failovers (mitigated by strict system prompt templates).
- Maintenance of multiple provider API keys and SDK clients.
