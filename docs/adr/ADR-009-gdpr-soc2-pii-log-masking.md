# ADR-009: Logback GDPR & SOC2 Zero-Leakage PII Data Masking

| Metadata | Value |
|---|---|
| Status | Accepted |
| Date | 2026-08-25 |
| Deciders | Security Lead, Compliance Officer |

---

## Context

Under GDPR Article 32 and SOC 2 Type II controls, Personally Identifiable Information (PII) and secret credentials (passwords, authorization tokens, payment details) must never be logged in plaintext. Third-party log collectors (Grafana Loki, Datadog) aggregate stdout logs; inadvertent PII logging constitutes a severe data protection breach.

## Decision

We introduced a custom **Logback PII Masking Converter** (`PiiMaskingConverter`):
1. Configured as a conversion rule in `logback-spring.xml` targeting the `%msg` log pattern.
2. High-performance compiled regex patterns identify and redact sensitive data tokens:
   - Email addresses: `john.doe@example.com` $\to$ `j***e@e***e.com`.
   - International phone numbers: `+380671234567` $\to$ `+38067***4567`.
   - Telegram Bot Tokens: `bot123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11` $\to$ `[REDACTED_BOT_TOKEN]`.
   - Authorization Headers: `Bearer eyJhbGciOi...` $\to$ `Bearer [REDACTED_JWT]`.

## Consequences

### Positive
- **Guaranteed Compliance**: Eliminates human error where developers use `log.info("Processing order: {}", order)` containing PII.
- **Auditability Preserved**: Domain identifiers remain masked but correlatable for debugging.

### Negative / Trade-offs
- Negligible regex string substitution latency during high-frequency debug logging.
