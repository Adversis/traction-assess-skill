# Identity & Access Domains

## Domain 3: Authentication Features
Focus: SSO, MFA, session management capabilities

### 3.1 Single Sign-On
**Question**: Can customers use their IdP?

- **L1** — Username/password only: Local authentication only. No SSO support.
  - Why: Deal-breaker for enterprise. Blocks centralized identity management.
- **L2** — SAML 2.0 or OIDC: Supports one major SSO protocol. Works with most enterprise IdPs.
  - Why: Opens enterprise market. Enables centralized authentication and offboarding.
- **L3** — SAML + OIDC + SCIM: Multiple protocols plus SCIM for automated user provisioning/deprovisioning.
  - Why: Full identity lifecycle management. Automated onboarding/offboarding critical for security.
- **L4** — + JIT, group sync, directory: Just-in-time provisioning, group-based access, directory sync for complex org structures.
  - Why: Scales to large enterprises. Supports complex organizational hierarchies.

### 3.2 MFA
**Question**: What MFA options exist?

- **L1** — No MFA or optional TOTP: MFA not available or only optional TOTP (Google Authenticator style).
  - Why: Minimum acceptable. Optional MFA often means unenforced.
- **L2** — TOTP + SMS for all users: TOTP and SMS options available and can be required for all users.
  - Why: SMS is weak but accepted fallback. Enforceable MFA is key.
- **L3** — Enforceable by admin, push: Admins can enforce MFA policy. Push notification options (Duo, Okta Verify) available.
  - Why: Better UX with push. Admin enforcement ensures compliance.
- **L4** — Hardware keys (WebAuthn/FIDO2): Support for hardware security keys. Phishing-resistant authentication.
  - Why: Strongest MFA. Required for high-security environments. Phishing-resistant.

### 3.3 Session Management
**Question**: What session controls exist?

- **L1** — Fixed timeout: Sessions expire after a fixed period. No configuration options.
  - Why: Basic session security. Fixed timeouts may not meet PCI DSS 8.6 (idle timeout) or HIPAA session requirements for healthcare buyers.
- **L2** — Configurable timeout: Admins can set session timeout duration for their organization.
  - Why: Balances security and usability per organization's needs.
- **L3** — Concurrent limits, remote logout: Limit simultaneous sessions. Admins can terminate sessions remotely.
  - Why: Prevents credential sharing. Enables immediate access revocation.
- **L4** — Session binding, step-up auth: Sessions bound to device/IP. Step-up authentication for sensitive operations.
  - Why: Advanced session security. Prevents session hijacking. Adaptive authentication.

### 3.4 Password Controls
**Question**: What password features?

- **L1** — Basic requirements: Minimum length and complexity requirements.
  - Why: Baseline password security. May not prevent weak passwords.
- **L2** — Admin-configurable policy: Admins can set password requirements for their organization.
  - Why: Supports varying compliance requirements.
- **L3** — Breach password checking: Passwords checked against known breach databases (HaveIBeenPwned).
  - Why: Prevents use of compromised credentials. Major security uplift.
- **L4** — Passwordless options: Magic links, passkeys, or other passwordless authentication methods.
  - Why: Best UX and security. Eliminates password-related attacks.

### 3.5 Social Sign-In `[B2C / Both]`
**Question**: Can users sign in with social accounts?

- **L1** — No social login: Only email/password registration available.
  - Why: Friction for consumer signup. Higher drop-off rates.
- **L2** — Google + Apple: Support for Google and Apple Sign-In.
  - Why: Covers majority of consumer market. Apple required for iOS apps.
- **L3** — + Facebook, Microsoft, others: Multiple social providers including Facebook, Microsoft, and regional options.
  - Why: Broader market coverage. Regional preferences vary.
- **L4** — Configurable + account linking: Admins can enable/disable providers. Users can link multiple social accounts to one identity.
  - Why: Flexibility for different markets. Prevents duplicate accounts.

### 3.6 Consumer Passwordless `[B2C / Both]`
**Question**: What frictionless auth options for consumers?

- **L1** — Email/password only: Traditional username/password authentication.
  - Why: High friction. Password fatigue leads to weak credentials.
- **L2** — Magic links: Email-based passwordless login via one-time links.
  - Why: Reduces friction. Works across all devices.
- **L3** — SMS OTP + biometrics: SMS one-time passwords plus device biometric authentication.
  - Why: Familiar to consumers. Biometrics for mobile-first.
- **L4** — Passkeys (WebAuthn): Full passkey support with cross-device sync.
  - Why: Future of consumer auth. Phishing-resistant with great UX.

### 3.7 Auth Rate Limiting
**Question**: How is brute force prevented?

- **L1** — No rate limiting: Login endpoints have no rate limiting or lockout mechanisms.
  - Why: Allows unlimited brute force attempts. Critical vulnerability for any auth system.
- **L2** — IP-based rate limiting: Rate limiting on login attempts per IP address. Account lockout after N failed attempts.
  - Why: Blocks basic brute force. IP-based limits can be bypassed with distributed attacks but stop most automated tools.
- **L3** — Adaptive + CAPTCHA: Adaptive rate limiting that adjusts based on risk signals. CAPTCHA challenges for suspicious attempts. Separate limits per account and IP.
  - Why: Stops sophisticated attacks. Risk-based approach balances security with user experience.
- **L4** — Credential stuffing protection: Bot detection, device fingerprinting, behavioral analysis. Integration with threat intelligence for known-bad IPs. Real-time alerting on attack patterns.
  - Why: Enterprise-grade protection against automated credential attacks. Required for high-value targets.

## Domain 4: Authorization Architecture
Focus: Permission models and implementation

### 4.1 Permission Model
**Question**: How granular are permissions?

- **L1** — Binary (admin/user): Two roles only. Admin has all access, users have limited access.
  - Why: Violates least privilege. Insufficient for any serious enterprise use.
- **L2** — RBAC with predefined roles: Multiple predefined roles (admin, editor, viewer, etc.).
  - Why: Standard enterprise pattern. Enables reasonable access segregation.
- **L3** — Fine-grained (resource-level): Permissions assignable at individual resource level.
  - Why: Supports complex access patterns. Enables true least privilege.
- **L4** — ABAC or policy-based: Attribute-based access control. Dynamic policies based on context.
  - Why: Most flexible. Supports complex, dynamic authorization scenarios.

### 4.2 Authz Implementation
**Question**: How is authz built?

- **L1** — Custom code with known patterns: Authorization logic built in application code following documented patterns (middleware checks, route guards). Not externalized but intentionally structured.
  - Why: Common starting point. Risk depends on code quality and review discipline. Acceptable if well-tested.
- **L2** — Established library (Casbin): Using proven authorization library with defined models.
  - Why: Reduces bugs. Easier to reason about. Community-tested.
- **L3** — Dedicated service (OpenFGA, Oso): Separate authorization service with defined schema.
  - Why: Centralized, auditable, scalable. Clear separation of concerns.
- **L4** — Externalized policy engine: Full policy externalization with real-time evaluation.
  - Why: Enterprise-grade. Supports complex multi-service authorization.

### 4.3 API Authorization
**Question**: How are APIs authorized?

- **L1** — Long-lived API keys: Static API keys that don't expire.
  - Why: High risk. Keys get leaked, no rotation, no granular permissions.
- **L2** — OAuth 2.0 / short-lived tokens: OAuth 2.0 implementation with token expiration.
  - Why: Industry standard. Enables token revocation and expiry.
- **L3** — Scoped tokens, granular perms: Tokens with specific scopes limiting access to certain resources/actions.
  - Why: True least privilege for API access. Limits blast radius.
- **L4** — Token binding (DPoP): Tokens bound to specific client. Prevents token theft/replay.
  - Why: Highest API security. Prevents token exfiltration attacks.

### 4.4 Permission Defaults
**Question**: What access do new users get?

- **L1** — Permissive defaults: New users get broad access by default.
  - Why: Security anti-pattern. Violates least privilege.
- **L2** — Minimal default role: New users get a minimal read-only or viewer role.
  - Why: Reasonable default. Requires explicit privilege escalation.
- **L3** — Deny-by-default: New users have no access until explicitly granted.
  - Why: Most secure. Ensures intentional access decisions.
- **L4** — + Admin-configurable defaults: Admins can configure what default access new users receive.
  - Why: Flexibility with security. Supports varying org needs.

### 4.5 Resource Authorization (BOLA Prevention)
**Question**: How does the app prevent unauthorized access to individual resources?

- **L1** — Sequential/guessable IDs: Resources use auto-increment IDs. Authorization checks rely on role alone, not resource ownership. Direct object references exposed in URLs.
  - Why: BOLA is OWASP API Top 10 #1. Guessable IDs + missing ownership checks = trivial data access across tenants.
- **L2** — UUIDs + ownership checks: Non-sequential resource identifiers (UUIDs). Ownership verified on every request -- not just role, but 'does this user/tenant own this resource?'
  - Why: Prevents enumeration attacks. Ownership checks are the single most impactful authorization control for multi-tenant SaaS.
- **L3** — Centralized resource authz: Resource authorization enforced through centralized middleware or policy layer. All endpoints validated automatically -- no per-endpoint manual checks. Automated testing for BOLA in CI/CD.
  - Why: Eliminates human error in authz checks. Scales with API surface area. Catches regressions automatically.
- **L4** — Field-level + contextual authz: Authorization at the field level within resources (not just resource access). Context-aware decisions (time, IP, device, risk score). Response filtering removes unauthorized fields automatically.
  - Why: Prevents partial data exposure -- not just who can access a resource, but which fields within it. Required for regulated data where some fields within a record have different sensitivity levels.

## Domain 5: Multi-Tenancy & Isolation
Focus: How customer data is separated

### 5.1 Data Isolation
**Question**: How is data separated?

- **L1** — Shared DB, tenant ID column: Single database with tenant_id column filtering.
  - Why: Cost effective but risky. Single bug can expose all tenants.
- **L2** — Schema or DB per tenant: Separate database schema or database per tenant.
  - Why: Stronger isolation. Limits blast radius of bugs.
- **L3** — Compute isolation: Separate compute resources (containers, pods) per tenant.
  - Why: Prevents noisy neighbor. Stronger security boundary.
- **L4** — Dedicated infrastructure option: Option for customers to have fully dedicated infrastructure.
  - Why: Ultimate isolation. Required for highest-security customers.

### 5.2 Cross-Tenant Protection
**Question**: What prevents cross-tenant access?

- **L1** — Application-layer checks: Tenant filtering done in application code.
  - Why: Relies on no bugs in app code. High risk.
- **L2** — DB-enforced row-level security: Database enforces tenant access via RLS policies.
  - Why: Defense in depth. Database prevents cross-tenant queries.
- **L3** — Network-level isolation: Network segmentation between tenant workloads.
  - Why: Even compromised app can't reach other tenants.
- **L4** — Cryptographic separation: Per-tenant encryption keys. Data unreadable without tenant key.
  - Why: Strongest protection. Compromised DB still protected.

### 5.3 Noisy Neighbor
**Question**: How are resources limited?

- **L1** — No resource limits: Tenants share resources without limits.
  - Why: One tenant can impact all others. Availability risk.
- **L2** — Rate limiting per tenant: API rate limits applied per tenant.
  - Why: Prevents API abuse. Basic fairness.
- **L3** — Resource quotas (CPU, mem): Hard limits on compute, memory, storage per tenant.
  - Why: Guaranteed resource availability. Prevents runaway costs.
- **L4** — Performance SLAs, dedicated: Guaranteed performance SLAs with option for dedicated resources.
  - Why: Enterprise requirement. Predictable performance.

## Domain 12: Integration Security
Focus: Third-party app governance, OAuth controls, API token lifecycle

### 12.1 Integration Inventory
**Question**: Can customers see what's connected?

- **L1** — Documentation only: List of possible integrations documented. No visibility into what's actually connected per tenant.
  - Why: Insufficient for security. Customers can't audit their own attack surface.
- **L2** — Connected apps visible in UI: Self-service UI showing all OAuth apps and API keys connected to the account with basic metadata.
  - Why: Enables customer audit of connected apps. Table stakes for enterprise security teams.
- **L3** — Activity + last-used timestamps: Integration inventory includes last-used date, created-by user, and activity summary. Highlights dormant integrations.
  - Why: Dormant integrations are prime attack targets. Enables hygiene reviews and offboarding.
- **L4** — Risk scoring + automated discovery: Automated discovery of shadow integrations. Risk scoring based on scopes, activity patterns, and vendor reputation.
  - Why: Proactive security. Catches unsanctioned integrations. Enables risk-based access decisions.

### 12.2 Token Lifecycle
**Question**: How are integration tokens managed?

- **L1** — Long-lived tokens, no expiry: API keys and OAuth tokens don't expire. Manual revocation only.
  - Why: High risk. Compromised tokens remain valid indefinitely. Common in breaches.
- **L2** — Expiring tokens (configurable): Tokens expire after configurable period (default 90 days). Refresh token rotation supported.
  - Why: Limits exposure window. Industry baseline per NIST and cloud provider guidance.
- **L3** — Customer-triggered rotation: Customers can rotate tokens on-demand via UI or API. Zero-downtime rotation with overlapping validity.
  - Why: Incident response capability. Enables immediate response to suspected compromise.
- **L4** — Auto-rotation + anomaly detection: Automatic rotation on schedule. Anomaly detection triggers immediate rotation. Customer notification on suspicious use.
  - Why: Proactive security. Detects and responds to token abuse automatically.

### 12.3 Scope Governance
**Question**: How are integration permissions controlled?

- **L1** — All-or-nothing access: Integrations get full access or no access. No granular scope control.
  - Why: Violates least privilege. Single compromised integration exposes everything.
- **L2** — Predefined scope levels: Integrations request scopes from predefined levels (read-only, read-write, admin). Scope visible at authorization.
  - Why: Basic least privilege. Users can make informed consent decisions.
- **L3** — Granular resource-level scopes: Fine-grained scopes per resource type or even individual resources. Scope reduction supported post-authorization.
  - Why: True least privilege. Limits blast radius of compromised integrations.
- **L4** — Admin-controlled scope policies: Admins define allowed scopes per app or app category. Automatic blocking of overprivileged requests. Scope audit and review workflows.
  - Why: Centralized governance. Prevents shadow IT from granting excessive access.

### 12.4 Actor Attribution
**Question**: Can you tell WHO made an API call?

- **L1** — Generic 'API' attribution: Logs show action was via API but not which key, token, or integration.
  - Why: Insufficient for investigation. Can't distinguish legitimate from malicious API use.
- **L2** — Key/token identification: Logs identify which API key or OAuth token was used. Token metadata (name, created date) available.
  - Why: Enables investigation. Can trace activity to specific integration.
- **L3** — App + acting user: Logs show both the OAuth app AND the user context (if applicable). Distinguishes service account vs user-delegated access.
  - Why: Critical for OAuth apps acting on behalf of users. Required for compliance in regulated industries.
- **L4** — Full attribution chain: Complete chain: App to Service Account to On-behalf-of User to Original authorizing admin. Correlation ID across systems.
  - Why: Forensic-grade attribution. Supports complex enterprise scenarios and legal requirements.

### 12.5 Integration Audit Events
**Question**: What integration activity is logged?

- **L1** — No integration-specific logs: Integration activity mixed with regular user activity. No way to filter or isolate.
  - Why: Blocks security monitoring of third-party access. Can't detect integration abuse.
- **L2** — Connect/disconnect events: OAuth authorization grants and revocations logged. API key creation and deletion logged.
  - Why: Basic lifecycle visibility. Enables review of who authorized what.
- **L3** — All integration activity logged: Separate log stream for all integration/API activity. Filterable by integration. Includes scope usage.
  - Why: Enables security monitoring of third-party behavior. Detects scope creep and abuse.
- **L4** — SIEM-ready + dashboards: Pre-built integration security dashboards. Native SIEM integration with integration-specific event types. Anomaly alerting.
  - Why: Enterprise-ready monitoring. Enables SOC visibility into third-party risk.

### 12.6 Revocation
**Question**: How fast can access be removed?

- **L1** — Support ticket required: Revoking integrations requires contacting support. No self-service option.
  - Why: Unacceptable incident response time. Leaves organization exposed during breach.
- **L2** — Self-service revoke in UI: Admins can revoke individual integrations via UI. Takes effect within minutes.
  - Why: Basic incident response capability. Enables immediate action on suspected compromise.
- **L3** — Bulk revoke + API: Bulk revocation of multiple integrations. API for automated revocation. Instant propagation.
  - Why: Scales to enterprise. Enables automated incident response playbooks.
- **L4** — Cascade + downstream notification: Revocation cascades to all related tokens. Downstream integrations notified. Audit trail of revocation chain.
  - Why: Handles complex integration graphs. Ensures complete access removal across connected systems.
