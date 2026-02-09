# Platform Security Domains

## Domain 7: Secure Foundations
Focus: Build vs. buy for security primitives

### 7.1 Authentication
**Question**: How is auth implemented?

- **L1** — Custom implementation: Authentication built from scratch.
  - Why: High risk. Auth bugs are critical vulnerabilities.
- **L2** — Established framework: Using framework's built-in auth (Django, Rails, etc.).
  - Why: Leverages community-tested code. Reduces risk of authentication bypass, session fixation, and credential storage vulnerabilities common in custom implementations.
- **L3** — Dedicated auth service: Separate authentication service with defined interface.
  - Why: Centralized, auditable. Separation of concerns.
- **L4** — Auth-as-a-service (Auth0, Okta): Using dedicated identity platform.
  - Why: Expert-managed. Compliance-ready. Full feature set.

### 7.2 Authorization
**Question**: How is authz implemented?

- **L1** — Custom implementation: Authorization logic custom-built in application.
  - Why: High bug risk. Hard to audit and reason about.
- **L2** — Established library: Using proven library like Casbin, CASL, Pundit.
  - Why: Tested patterns. Easier to audit.
- **L3** — Dedicated authz service: Separate authorization service (OpenFGA, SpiceDB, etc.).
  - Why: Centralized decisions. Auditable. Scalable.
- **L4** — Externalized policy engine: Full policy externalization with management plane.
  - Why: Enterprise-grade. Policy as code. Full audit trail.

### 7.3 Cryptography
**Question**: How is crypto implemented?

- **L1** — Standard library only: Using language standard library crypto.
  - Why: May lack features. Varies by language quality.
- **L2** — Established lib (libsodium): Using high-level crypto library like libsodium.
  - Why: Misuse-resistant APIs. Expert-designed.
- **L3** — Cloud KMS integration: Leveraging cloud provider's key management service.
  - Why: Managed infrastructure. Hardware backing.
- **L4** — HSM-backed, FIPS validated: Hardware security modules with FIPS validation.
  - Why: Highest assurance. Government/regulated industry requirement.

### 7.4 Session Management
**Question**: How are sessions managed?

- **L1** — Framework default: Using framework's default session handling.
  - Why: May have secure defaults. Check configuration.
- **L2** — Redis with secure config: Centralized session store with proper security config.
  - Why: Enables scaling. Proper expiry. Secure storage.
- **L3** — Stateless JWT, proper validation: JWT-based sessions with proper validation and refresh.
  - Why: Scalable. No session store. Must implement correctly.
- **L4** — Token binding, device attestation: Sessions bound to device with attestation.
  - Why: Prevents session theft. Highest session security.

## Domain 8: Infrastructure Security
Focus: Production environment controls

### 8.1 Prod Data Access
**Question**: Who can access prod data?

- **L1** — Engineers have standing access: Development team has persistent access to production data.
  - Why: High risk. Insider threat. Compliance issue.
- **L2** — Access requires approval: Production access requires manager/security approval.
  - Why: Accountability. Audit trail. Reduced standing access.
- **L3** — No standing access (JIT): Just-in-time access only. No persistent credentials. All privileged sessions recorded with command-level logging for SSH and video replay for console access. Session anomaly detection for high-risk actions.
  - Why: Minimal exposure. Time-limited access.
- **L4** — Zero standing access, automated remediation: No persistent human access to production. All access is automated, time-bound, and auto-revoked. Break-glass procedures with dual-custody sealed credentials, SOC alerts on activation, and mandatory post-use review. Service account inventory with documented owners, automated rotation, and lifecycle governance.
  - Why: Eliminates insider threat surface. Access is the exception, not the rule. Break-glass ensures safety valve exists.

### 8.2 Environment Isolation
**Question**: How are environments separated?

- **L1** — Shared, logical separation: Same infrastructure with logical separation.
  - Why: Cost effective but risky. Blast radius is large.
- **L2** — Private subnets, no public DB: Network segmentation. Databases not publicly accessible.
  - Why: Defense in depth. Limits attack surface.
- **L3** — Zero-trust internal: No implicit trust between internal services.
  - Why: Assumes breach. Requires authentication everywhere.
- **L4** — mTLS everywhere: Mutual TLS between all services.
  - Why: Cryptographic service identity. Prevents lateral movement.

### 8.3 Secrets Management
**Question**: How are secrets handled?

- **L1** — Env vars, config files: Secrets in environment variables or config files.
  - Why: Basic. Risk of exposure in logs, errors.
- **L2** — Secrets manager: Dedicated secrets management (Vault, AWS Secrets Manager).
  - Why: Centralized. Audit trail. Access control.
- **L3** — Automated rotation: Secrets automatically rotated on schedule.
  - Why: Limits exposure window. Reduces manual work.
- **L4** — Customer-managed keys: Customers can provide keys for their tenant's secrets.
  - Why: Customer control. Required for some compliance.

### 8.4 Build & Artifact Security
**Question**: How is the build pipeline secured?

- **L1** — Basic CI/CD: CI/CD pipeline exists but without specific security controls. Build agents shared, secrets in environment.
  - Why: Functional but risky. Compromised pipeline = compromised product.
- **L2** — Hardened pipeline: Dedicated build agents, pinned dependencies, signed commits required. Secrets from vault, not environment.
  - Why: Reduces supply chain attack surface. Prevents casual tampering.
- **L3** — Artifact signing + provenance: Build artifacts cryptographically signed. Provenance metadata generated. Container images scanned and signed before deployment.
  - Why: Verifiable supply chain. Customers can validate artifact integrity. Required for SLSA Level 3.
- **L4** — Reproducible builds + SBOM: Deterministic builds that can be independently reproduced. Full SBOM attached to every release. Artifact attestation chain.
  - Why: Highest supply chain assurance. Enables independent verification. Required for government and critical infrastructure.

## Domain 9: Secure Development
Focus: How the product is built

### 9.1 Dependency Management
**Question**: How are deps managed?

- **L1** — Manual updates: Dependencies updated manually, ad-hoc.
  - Why: Likely to miss vulnerabilities. Technical debt.
- **L2** — Automated scanning: Automated vulnerability scanning (Dependabot, Snyk).
  - Why: Catches known vulnerabilities. Low effort.
- **L3** — SCA in CI/CD with blocking: Software composition analysis blocks builds with critical vulns.
  - Why: Prevents vulnerable code deployment. Enforced security.
- **L4** — SBOM, VEX, provenance: Full software bill of materials with vulnerability exchange and provenance.
  - Why: Supply chain transparency. Customer assurance. Executive order compliance.

### 9.2 Security Testing
**Question**: How is the product tested?

- **L1** — Ad-hoc manual testing: Security testing done occasionally, manually.
  - Why: Inconsistent coverage. Likely to miss issues.
- **L2** — Annual third-party pentest: External penetration test performed annually.
  - Why: Independent validation. Often required for compliance.
- **L3** — Continuous SAST/DAST: Automated security testing in CI/CD pipeline.
  - Why: Catches issues early. Continuous security validation.
- **L4** — Bug bounty + red team: Public bug bounty program plus internal/external red team.
  - Why: Continuous independent testing. Crowdsourced security.

### 9.3 Secure Design
**Question**: How is security built in?

- **L1** — Security ad-hoc: Security considered reactively, not systematically.
  - Why: Expensive fixes. Security as afterthought.
- **L2** — Security in design docs: Design documents include security considerations.
  - Why: Security as part of design process.
- **L3** — Threat modeling: Systematic threat modeling for new features.
  - Why: Proactive security. Identifies risks early.
- **L4** — Security champions program: Embedded security advocates in development teams.
  - Why: Scales security. Cultural integration.

### 9.4 Vulnerability Response
**Question**: How fast are vulnerabilities addressed?

- **L1** — No defined SLAs: Vulnerabilities addressed without formal timelines.
  - Why: Unpredictable response leaves customers exposed.
- **L2** — SLAs by severity: Critical: 14 days, High: 30 days, Medium: 90 days. CISA KEV prioritized.
  - Why: Structured approach. CISA KEV prioritization addresses most exploited vulns.
- **L3** — Aggressive patching (7 days critical): Critical: 7 days, High: 14 days. Immediate response for actively exploited. Customer notification.
  - Why: Evidence: immediate patching yields ~5x lower compromise odds vs 1 month delay.
- **L4** — 24-48 hour critical response: Critical/actively exploited: 24-48 hours. Proactive customer notification. Rollback capability.
  - Why: Best-in-class response. Minimizes exposure window for known threats.

### 9.5 Security Asset Attribution
**Question**: Can you map vulnerabilities to owners?

- **L1** — Informal service inventory: Basic list of production services (wiki/spreadsheet). Ownership is tribal knowledge.
  - Why: Enterprise buyers ask 'who handles my data?' A spreadsheet is enough at this stage.
- **L2** — Service catalog with owners: Maintained catalog (Backstage, Cortex, OpsLevel) mapping services to teams. Vulnerability findings auto-routed to owners. Coverage gaps visible.
  - Why: Eliminates 'who owns this?' delays during incidents. Enables SLA tracking per team.
- **L3** — Full attribution chain: Services to pipelines to repos to teams. All security findings (SAST, DAST, SCA, pentest, bounty) aggregated with ownership. Coverage metrics tracked.
  - Why: Complete visibility into security posture per team. Enables accountability and prioritization.
- **L4** — Real-time posture with risk scoring: Automated discovery, real-time per-service/per-team posture, risk scoring by data sensitivity + exposure, drift detection for untracked services.
  - Why: Proactive security. Catches shadow services and coverage gaps before they become incidents.

### 9.6 Security Program Management
**Question**: How is security work tracked and enforced?

- **L1** — Ad-hoc tracking: Security work in general issue trackers without dedicated labels or SLAs.
  - Why: If the answer is 'we use Jira,' that's L1. Achievable for a 20-person team.
- **L2** — Structured security backlog: Dedicated issue types/labels, severity SLAs, visible to leadership. Security work tracked separately from feature work. Regular review cadence.
  - Why: Makes security work visible. Prevents security debt from hiding behind feature work.
- **L3** — Progressive enforcement with metrics: Warn mode to trial with bellwether teams to org-wide rollout. CI/CD blocking for critical controls. Team-level scorecards. Feedback loops where teams flag false positives and suggest improvements.
  - Why: Avoids big-bang enforcement failures. Teams that participate in rollout become advocates.
- **L4** — Continuous assurance with automation: Automated control health monitoring. Preventative controls paired with historical issue classes. Cross-team benchmarking. Executive reporting in business-risk terms.
  - Why: Security program runs itself. Leadership sees risk in business terms, not CVE counts.

### 9.7 Input Validation & Output Encoding
**Question**: How are application inputs validated and outputs encoded?

- **L1** — Ad-hoc validation: Input validation exists but is inconsistent -- some endpoints validate, others don't. No enforced schema validation. Error messages may expose internal details.
  - Why: Injection is OWASP Top 10 #3. Inconsistent validation means attackers only need to find one unvalidated path.
- **L2** — Schema-based validation: All API inputs validated against schemas (OpenAPI, JSON Schema, protobuf). Allowlist-based (accept known-good) rather than denylist. Output encoding applied consistently. Error responses standardized without stack traces.
  - Why: Schema enforcement catches entire classes of attacks. Allowlist approach is fundamentally more secure than pattern matching for bad input.
- **L3** — Automated enforcement in CI/CD: Schema compliance enforced in CI/CD -- endpoints without validation schemas fail build. SAST rules for injection patterns. Field-level output filtering prevents excessive data exposure. Upload validation for file types and content.
  - Why: Shifts validation left. Prevents deployment of unvalidated endpoints. Every API consumer should be validated before reaching backend services.
- **L4** — Runtime protection + fuzzing: WAF/API gateway enforces schema compliance at runtime. Continuous fuzzing of all input parameters. GraphQL query complexity limits. Response size anomaly detection. Polyglot attack prevention.
  - Why: Defense in depth -- even if code-level validation has bugs, runtime enforcement catches exploitation. Continuous fuzzing finds edge cases static analysis misses.
