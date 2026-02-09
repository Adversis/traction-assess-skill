# Data & Transparency Domains

## Domain 1: Customer Data Controls
Focus: Export, deletion, residency, retention features

### 1.1 Data Export
**Question**: Can customers get their data out?

- **L1** — Export on request (manual): Customers can request data exports through support tickets. Manual process handled by internal teams, typically CSV or JSON format.
  - Why: Table stakes for any B2B SaaS. Customers need assurance they can retrieve their data. Required for GDPR data portability rights (Article 20).
- **L2** — Self-service export UI (JSON, CSV): Users can export their data directly from the application interface without contacting support. Multiple format options available.
  - Why: Reduces support burden and gives customers immediate control. Shows maturity in data ownership philosophy.
- **L3** — Programmatic export via API: Full API access to export all customer data programmatically. Supports automation, scheduled exports, and integration with customer systems.
  - Why: Enterprise customers need automated data pipelines. Enables backup strategies and multi-vendor architectures.
- **L4** — Real-time streaming / CDC: Change Data Capture or event streaming allows customers to receive data changes in real-time. Supports Kafka, webhooks, or similar.
  - Why: Critical for data warehouse integration, real-time analytics, and zero-ETL architectures. Top-tier enterprise requirement.

### 1.2 Data Deletion
**Question**: Can customers delete their data?

- **L1** — Deletion on request: Customers request deletion via support. Internal process to remove data from systems within a defined SLA.
  - Why: GDPR right to erasure (Article 17) baseline. Shows basic compliance posture.
- **L2** — Self-service deletion with confirmation: Users can delete their own data through the UI with appropriate confirmation steps and grace periods.
  - Why: Empowers customers while protecting against accidental deletion. Reduces compliance overhead.
- **L3** — Verified deletion with audit proof: Deletion is verified across all systems (including backups) with audit trail proving data removal.
  - Why: Enterprise customers need evidence for their own compliance. Supports audit requirements.
- **L4** — Cryptographic deletion (crypto-shredding): Data encrypted with per-tenant keys. Deletion achieved by destroying encryption keys, making data unrecoverable.
  - Why: Fastest, most verifiable deletion method. Handles data in backups automatically. Gold standard for sensitive industries.

### 1.3 Data Residency
**Question**: Can customers control where data lives?

- **L1** — Single region, documented: Data stored in one region with clear documentation of location. Customers know where their data resides.
  - Why: Transparency baseline. Many regulations require knowing data location (GDPR, data sovereignty laws).
- **L2** — Region selection at signup: Customers choose their data region during onboarding (e.g., US, EU, APAC). Data stays in selected region.
  - Why: Addresses basic data sovereignty. Required for many EU enterprise deals. Shows operational maturity.
- **L3** — Per-tenant region control: Admins can configure and potentially migrate data regions post-signup. Granular control over data placement.
  - Why: Supports M&A scenarios, changing regulations, and complex enterprise requirements.
- **L4** — Customer-managed infrastructure: Option for customers to run the product in their own cloud account or on-premises with full data control.
  - Why: Required for highest-security customers (government, financial services, healthcare). Ultimate data sovereignty.

### 1.4 Retention Controls
**Question**: Can customers control how long data is kept?

- **L1** — Vendor-defined only: Retention periods set by vendor policy. Documented but not configurable by customers.
  - Why: Baseline transparency. Customers need to know retention for their own compliance planning.
- **L2** — Configurable retention periods: Admins can set retention periods for their organization's data within vendor-defined bounds.
  - Why: Different industries have different requirements. Healthcare (HIPAA) vs financial (7 years) vs general (varies).
- **L3** — Granular retention by data type: Different retention policies for different data types (e.g., logs vs documents vs PII).
  - Why: Sophisticated compliance needs. Audit logs may need longer retention than operational data.
- **L4** — Customer-defined with enforcement: Customers set exact policies with automated enforcement, audit trails, and legal hold capabilities.
  - Why: Full compliance control. Supports complex regulatory environments and litigation requirements.

### 1.5 Consumer Privacy Controls `[B2C / Both]`
**Question**: What privacy controls exist for end users?

- **L1** — Basic privacy policy: Privacy policy published but no user controls beyond account deletion.
  - Why: Minimum legal requirement. Doesn't meet modern consumer expectations.
- **L2** — Cookie consent + preferences: Cookie consent banner with accept/reject. Basic marketing email preferences.
  - Why: Meets basic GDPR/ePrivacy requirements. Standard consumer expectation.
- **L3** — Privacy dashboard: Self-service privacy center where users can view data collected, adjust tracking, manage consent.
  - Why: Consumer trust differentiator. Reduces support burden for privacy requests.
- **L4** — Granular consent + data portability: Fine-grained consent controls per data type. Easy data download. Account deletion with verification.
  - Why: Best-in-class consumer privacy. Builds trust. Prepares for stricter future regulations.

### 1.6 Account Recovery `[B2C / Both]`
**Question**: How can users recover account access?

- **L1** — Email reset only: Password reset via email link. No alternatives if email is compromised.
  - Why: Basic but risky. Email compromise leads to full account takeover.
- **L2** — Email + phone backup: Can add phone number as backup recovery method.
  - Why: Second channel reduces lockout risk. Still vulnerable to SIM swap.
- **L3** — Recovery codes + trusted contacts: One-time recovery codes. Option to designate trusted contacts for recovery.
  - Why: Reduces single point of failure. Facebook's trusted contacts model.
- **L4** — Identity verification + fraud detection: Multi-factor identity verification. Fraud detection on recovery attempts. Cooling-off periods.
  - Why: Balances security and usability. Prevents account takeover attacks.

## Domain 2: Customer Observability
Focus: Audit logs and monitoring available to customers

### 2.1 Audit Logs
**Question**: Can customers see what happened?

- **L1** — No customer-facing logs: Logs exist internally but aren't exposed to customers.
  - Why: Insufficient for enterprise. Blocks SOC 2 CC7.2 (monitoring) and HIPAA audit requirements. Customers can't perform security reviews without log access.
- **L2** — Admin action logs in UI: Basic logs showing admin actions like user management, settings changes, plus authentication and input validation failures visible in dashboard.
  - Why: Enables basic security monitoring and compliance evidence.
- **L3** — Comprehensive logs (all actions): All security-relevant events logged: logins, data access, exports, API calls, permission changes, input validation failures, session events, file uploads.
  - Why: Full visibility for security teams. Required for SOC 2, ISO 27001, most compliance frameworks.
- **L4** — Immutable, tamper-evident logs: Logs cryptographically signed or stored in append-only systems. Evidence of tampering detectable.
  - Why: Forensic-grade logging. Required for regulated industries. Supports legal proceedings.

### 2.2 Log Export
**Question**: Can customers get logs into their systems?

- **L1** — No export: Logs viewable in UI only. No way to extract for external analysis.
  - Why: Blocks SIEM integration. Deal-breaker for security-mature customers.
- **L2** — Manual download (CSV, JSON): Users can download log files manually through the interface. Basic filtering by date range.
  - Why: Basic export capability. Supports periodic compliance reviews.
- **L3** — API access / webhooks: Programmatic access to logs via API with pagination and filtering. Real-time webhooks with retry/acknowledgment for streaming to external systems.
  - Why: Enables SIEM integration, automated alerting, and security automation.
- **L4** — Native SIEM integrations: Pre-built integrations with major SIEMs (Splunk, Sentinel, Chronicle, Datadog). Published schema documentation. Multiple output formats (CEF, OCSF, JSON).
  - Why: Frictionless security integration. Shows security maturity. Often required in RFPs.

### 2.3 Log Retention
**Question**: How long are logs available?

- **L1** — 7 days or less: Short retention window. Logs automatically purged after a week.
  - Why: Insufficient for most compliance. Limits incident investigation capability.
- **L2** — 90 days minimum: Logs retained for at least 90 days. Standard retention period.
  - Why: Meets most basic compliance requirements. Enables reasonable investigation window.
- **L3** — Customer-configurable (1 year): Customers can configure retention up to 1 year or more.
  - Why: Supports varying compliance needs. Healthcare, financial often need 1+ years.
- **L4** — Unlimited with BYOS: Bring Your Own Storage option. Customers store logs in their own systems indefinitely.
  - Why: Ultimate control. Supports legal hold, long-term compliance, and cost optimization.

### 2.4 Log Content
**Question**: What's in the logs?

- **L1** — Timestamp, action only: Basic logs with when and what. Minimal context.
  - Why: Insufficient for investigation. Can't answer 'who' or 'from where'.
- **L2** — + Actor, resource: Includes who performed action and what resource was affected.
  - Why: Enables accountability. Supports access reviews and incident attribution.
- **L3** — + IP, session, severity, correlation ID: Network context added. Includes IP, user agent, session ID, event severity, and correlation IDs for cross-request tracing.
  - Why: Enables threat detection. Supports forensic investigation and anomaly detection.
- **L4** — + Request/response payloads: Full request and response data logged with PII/secrets redaction. Never logs: passwords, tokens, encryption keys, PCI data, session IDs (use hashes).
  - Why: Complete audit trail. Supports detailed forensics and debugging. Handle with care for privacy.

### 2.5 Deception & Trip Wires
**Question**: Can customers detect unauthorized access?

- **L1** — Alerting on sensitive actions: Alerts when sensitive operations occur (bulk export, admin changes, unusual access patterns).
  - Why: Basic detection. Relies on known patterns.
- **L2** — Canary tokens available: Product offers canary tokens (fake credentials, honeypot files) that alert when accessed.
  - Why: Evidence-based: canary tokens have demonstrated effectiveness at detecting breaches. Low false-positive.
- **L3** — Customer-configurable trip wires: Customers can create their own honey tokens, trap resources, and detection rules.
  - Why: Enables customer-specific deception based on their threat model.
- **L4** — Integrated deception platform: Full deception infrastructure with fake data, automated alerting, and forensic capture.
  - Why: Most effective detection method. Catches attackers regardless of technique.

### 2.6 Threat Detection
**Question**: What automated threat detection exists?

- **L1** — Manual review only: Logs available but no automated detection. Security team must manually review.
  - Why: Doesn't scale. Misses subtle attack patterns.
- **L2** — Rule-based alerts: Predefined rules for common attack patterns (brute force, impossible travel, bulk access).
  - Why: Catches known patterns. Foundation for security monitoring.
- **L3** — Behavioral anomaly detection: ML-based detection of unusual user/API behavior. Baseline established per tenant.
  - Why: Catches unknown attacks. Effective against insider threats.
- **L4** — Customer-tunable detection + SOC integration: Customers can tune detection rules, set thresholds, and integrate with their SOC/SIEM.
  - Why: Enables security teams to customize detection for their environment.

## Domain 6: Cryptographic Design
Focus: Encryption implementation and key management

### 6.1 Data in Transit
**Question**: How is data protected in transit?

- **L1** — TLS 1.2+: All connections encrypted with TLS 1.2 or higher.
  - Why: Baseline requirement. TLS 1.0/1.1 are deprecated.
- **L2** — TLS 1.3, HSTS, cert pinning: TLS 1.3, HTTP Strict Transport Security, certificate pinning for mobile.
  - Why: Modern TLS. Prevents downgrade attacks and MITM.
- **L3** — mTLS available: Mutual TLS option for API connections.
  - Why: Two-way authentication. Stronger than API keys.
- **L4** — E2E encryption option: End-to-end encryption where vendor cannot access plaintext.
  - Why: Zero-knowledge architecture option. Maximum privacy.

### 6.2 Data at Rest
**Question**: How is stored data encrypted?

- **L1** — Platform-managed (cloud default): Using cloud provider's default encryption (AWS S3, RDS encryption).
  - Why: Basic protection. Vendor manages keys.
- **L2** — App-layer for sensitive fields: Application encrypts sensitive fields before storage.
  - Why: Defense in depth. Protects against DB access.
- **L3** — Per-tenant encryption keys: Each tenant's data encrypted with separate key.
  - Why: Tenant isolation. Enables crypto-shredding.
- **L4** — Customer-managed keys (BYOK): Customers provide and control their own encryption keys.
  - Why: Ultimate control. Required for some regulated industries.

### 6.3 Key Management
**Question**: How are keys managed?

- **L1** — Vendor-managed, no visibility: Vendor handles all key management. Customer has no visibility.
  - Why: Simple but requires full trust in vendor.
- **L2** — Key rotation supported: Regular key rotation with vendor handling process.
  - Why: Limits exposure window. Compliance requirement.
- **L3** — Customer-triggered rotation: Customers can trigger key rotation on demand.
  - Why: Incident response capability. Customer control.
- **L4** — Customer's KMS (BYOK): Integration with customer's key management system.
  - Why: Full customer control. Keys never leave customer's KMS.

### 6.4 Crypto Primitives
**Question**: What crypto approach?

- **L1** — Documented, no custom crypto: Cryptographic choices are documented. No custom cryptographic implementations -- using platform/language defaults at minimum.
  - Why: Transparency baseline. Enables security review. 'Don't roll your own crypto' is rule #1.
- **L2** — Established crypto libraries: Using well-known crypto libraries (libsodium, BouncyCastle, etc.) with documented algorithm selections and rationale.
  - Why: Community-audited implementations. Misuse-resistant APIs reduce risk of subtle bugs.
- **L3** — HSM/TPM where possible: Hardware security modules for key operations where applicable.
  - Why: Hardware-backed security. Keys protected from extraction.
- **L4** — FIPS 140-2/3 validated: Using FIPS-validated cryptographic modules.
  - Why: Government requirement. Highest assurance level.
