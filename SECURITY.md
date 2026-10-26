# Security Policy & Vulnerability Disclosure

| Metadata | Value |
|---|---|
| Target Platform | Launchly SaaS Application |
| Document Version | 1.1.0 |
| Security Contact | security@launchly.app |
| Response SLA | Initial acknowledgment within 24 hours |

---

## 1. Supported Versions

Launchly provides security patches and critical vulnerability mitigations for the following releases:

| Release Line | Supported | Minimum Runtime | Status |
|---|---|---|---|
| `v1.x` (Current Main) | Yes | Java 21 LTS / Node 20+ | Active Production Support |
| `< 1.0` (Alpha / Beta) | No | Java 17 | Unsupported |

---

## 2. Reporting a Vulnerability

We treat the security of our platform, user credentials, and Telegram integrations with the highest priority. If you discover a security vulnerability in Launchly, please disclose it responsibly.

### Responsible Disclosure Guidelines
- **Do NOT** open a public GitHub issue or discuss potential vulnerabilities in public forums.
- Send an encrypted email to **security@launchly.app** containing:
  - Description of the vulnerability and its potential impact.
  - Step-by-step reproduction instructions or proof-of-concept (PoC) script.
  - Affected components (`backend`, `frontend`, `auth`, `bot-engine`, or third-party dependencies).
- Allow us a reasonable window (up to 30 days) to investigate, patch, and deploy fixes before public disclosure.

---

## 3. Vulnerability Response SLA

| Priority | Definition | Initial Response | Patch Deployment Target |
|---|---|---|---|
| **P0: Critical** | Remote code execution, SQL injection, privilege escalation, bypass of Turnstile/auth. | < 12 hours | < 24-48 hours |
| **P1: High** | Horizontal data leakage across tenants, CSRF on mutating endpoints, unauthenticated bot manipulation. | < 24 hours | < 5 business days |
| **P2: Medium** | Rate limiter circumvention, reflected XSS, sensitive metadata disclosure. | < 48 hours | < 14 business days |
| **P3: Low** | Missing security headers, non-exploitable timing vulnerabilities. | < 72 hours | Next scheduled sprint |

---

## 4. Key Security Controls in Launchly

- **Server-Side Bot Shield**: Cloudflare Turnstile token verification on authentication endpoints.
- **Stateless Authentication**: Short-lived JWT (15-minute access tokens) with Redis-backed revocation blacklist.
- **Distributed Idempotency & Rate Limiting**: Token-bucket algorithm with Redis locks on financial and webhook endpoints.
- **PII Log Redaction**: Automated sanitization of emails, phone numbers, and bearer credentials in runtime logs.
- **Static Analysis & Secret Scanning**: Automated CI/CD checks running **Gitleaks**, **SonarCloud Quality Gate**, and **OWASP Dependency-Check**.
