# Code Signals Reference

What to search for in a codebase when assessing each TRACTION domain. Use these patterns with the Grep and Glob tools to find evidence systematically.

> **Scoring rule**: Score the highest level where ALL criteria are met, not the highest where ANY criterion is met. If you find partial evidence, score at the level below and note what's missing.

---

## Domain 1: Customer Data Controls

### Grep Patterns
```
export.*csv|export.*json|data.?export|bulk.?export|download.?data
delete.*account|account.*delet|purge.*user|data.?erasure|right.?to.?be.?forgotten|gdpr.*delet|soft.?delete|hard.?delete
data.?residen|region.?select|geo.?partition|data.?location|data.?sovereign
retention.?polic|retention.?period|ttl|time.?to.?live|expire.*after|auto.?purg|data.?lifecycle
cookie.?consent|privacy.?prefer|consent.?manage|opt.?out|do.?not.?track|gdpr.?consent|ccpa
account.?recover|password.?reset|reset.?token|recovery.?code|backup.?code
crypto.?shred|key.?delet.*encrypt|per.?tenant.?key
cdc|change.?data.?capture|debezium|kafka.?connect|streaming.?export
```

### Glob Patterns
```
**/export/**
**/data-export/**
**/gdpr/**
**/privacy/**
**/retention/**
**/account/delete*
**/account/recover*
**/consent/**
**/migrations/*retention*
**/migrations/*purge*
```

### Dependency Signals
- **Export**: `csv-writer`, `json2csv`, `xlsx`, `archiver` (JS); `pandas`, `csv` (Python); `encoding/csv` (Go)
- **Deletion**: `soft-delete` plugins, `paranoid` (Sequelize), `acts_as_paranoid` (Ruby)
- **Privacy**: `@osano/consent-manager`, `react-cookie-consent`, `onetrust`, `cookiebot`
- **CDC/Streaming**: `@debezium`, `kafkajs`, `confluent-kafka`, `aiokafka`

### Infrastructure Signals
- Multi-region database deployments (Terraform `aws_rds_cluster` with multiple `availability_zones` or separate region modules)
- S3 lifecycle policies, GCS lifecycle rules, Azure Blob lifecycle management for retention
- K8s CronJob for data retention enforcement, cleanup batch jobs
- KMS key per tenant patterns in Terraform/Pulumi
- Data replication configurations across regions

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 1.1 Data Export | Support ticket or admin script for export | UI with export button, format selection (CSV/JSON) | `/api/v*/export` endpoint, bulk API | Kafka/CDC config, webhook streaming, real-time sync |
| 1.2 Data Deletion | Admin script or DB query for deletion | Self-service delete in UI, soft-delete with grace period | Deletion verification across systems, audit trail of deletions | Per-tenant encryption keys, key deletion workflow (crypto-shredding) |
| 1.3 Data Residency | Single region documented in config | Region selection at signup (`region` field on tenant/org model) | Per-tenant region config, migration tooling between regions | Helm charts / Terraform for customer-managed infra, BYOC support |
| 1.4 Retention | Documented policy only (no code) | Admin-configurable TTL/retention settings in app | Per-data-type retention policies, granular config | Legal hold model, automated enforcement jobs, audit of policy changes |
| 1.5 Consumer Privacy `[B2C]` | Privacy policy page exists | Cookie consent banner, marketing prefs | Privacy dashboard component, consent management UI | Granular per-type consent, data download, verified account deletion |
| 1.6 Account Recovery `[B2C]` | Email-based password reset only | Phone/SMS backup recovery | Recovery codes generation, trusted contacts model | Hardware key recovery, admin-assisted recovery with identity verification |

---

## Domain 2: Customer Observability

### Grep Patterns
```
audit.?log|audit.?trail|audit.?event|activity.?log|event.?log
log.?export|siem.?integrat|splunk|datadog|elastic|log.?forward|log.?ship
log.?retention|log.?ttl|log.?archiv|log.?rotat
actor_id|user_agent|ip_address|source_ip|request_id|correlation_id|trace_id
honeypot|honey.?token|canary.?token|trip.?wire|decoy|deception
threat.?detect|anomaly.?detect|alert.?rule|security.?alert|suspicious.?activ
who.?what.?when|event_type|action_type|resource_type
```

### Glob Patterns
```
**/audit/**
**/audit-log*
**/activity-log*
**/events/audit*
**/logging/**
**/siem/**
**/honeypot*
**/canary*
**/threat*
```

### Dependency Signals
- **Logging**: `winston`, `pino`, `bunyan` (JS); `structlog`, `python-json-logger` (Python); `zap`, `logrus` (Go); `serilog` (C#)
- **SIEM/Export**: `@splunk/cloud-sdk`, `datadog-api-client`, `elasticsearch`, `@elastic/elasticsearch`
- **Tracing**: `@opentelemetry/*`, `opentelemetry-*`, `dd-trace`, `jaeger-client`
- **Deception**: `canarytokens`, custom honeypot implementations

### Infrastructure Signals
- CloudWatch Logs, CloudTrail configuration in Terraform
- Log forwarding/shipping config (Fluentd, Fluent Bit, Vector, Logstash)
- S3 bucket policies for log archival with lifecycle rules
- SIEM webhook or API integration configuration

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 2.1 Audit Logs | Basic event logging (login, CRUD) | Structured audit events with who/what/when/where | Customer-accessible audit log UI, filterable | Real-time audit stream API, immutable log storage |
| 2.2 Log Export | Manual export on request | CSV/JSON download from UI | API endpoint for log export, SIEM integration (Splunk, Datadog) | Real-time streaming to customer SIEM, custom webhook destinations |
| 2.3 Log Retention | Fixed retention documented | Configurable retention per org | Per-log-type retention, long-term archival | Customer-defined policies, legal hold on logs, immutable archival |
| 2.4 Log Content | Basic action + timestamp | Actor ID, IP, user agent, resource | Request/response metadata, correlation IDs | Full context chain, before/after values, causation tracking |
| 2.5 Deception | None | Canary tokens in sensitive config | Honeypot endpoints/resources, trip wire alerts | Customer-configurable decoys, automated response on trigger |
| 2.6 Threat Detection | None | Basic alerting (brute force, impossible travel) | Behavioral anomaly detection, rule engine | Customer-tunable detection rules, ML-based anomaly detection |

---

## Domain 3: Authentication Features

### Grep Patterns
```
saml|oidc|openid.?connect|sso|single.?sign.?on|identity.?provider|idp
mfa|multi.?factor|two.?factor|2fa|totp|webauthn|fido|authenticator
session.?timeout|session.?expir|max.?age|idle.?timeout|session.?revok|concurrent.?session
password.?polic|password.?strength|zxcvbn|password.?hash|bcrypt|argon2|scrypt|pbkdf2|have.?i.?been.?pwned|hibp|breached.?password
oauth.*google|oauth.*github|oauth.*facebook|social.?login|social.?auth|passport.?strategy
magic.?link|passwordless|webauthn|passkey|fido2
rate.?limit|throttl|brute.?force|login.?attempt|failed.?attempt|account.?lock
```

### Glob Patterns
```
**/auth/**
**/authentication/**
**/sso/**
**/saml/**
**/oidc/**
**/mfa/**
**/session/**
**/passport/**
**/login/**
**/identity/**
```

### Dependency Signals
- **SSO**: `passport-saml`, `node-saml`, `@auth0/nextjs-auth0`, `python-saml`, `onelogin`, `omniauth-saml` (Ruby); `spring-security-saml2` (Java)
- **MFA/TOTP**: `speakeasy`, `otplib`, `pyotp`, `pquerna/otp` (Go), `devise-two-factor` (Ruby)
- **WebAuthn**: `@simplewebauthn/*`, `py_webauthn`, `duo-labs/webauthn` (Go), `webauthn4j` (Java)
- **Password**: `bcryptjs`, `argon2`, `zxcvbn`, `haveibeenpwned`
- **Rate limiting**: `express-rate-limit`, `rate-limiter-flexible`, `django-ratelimit`, `throttle` (Ruby)
- **Auth platforms**: `@auth0/*`, `@clerk/*`, `firebase-admin`, `supertokens-*`, `@workos-inc/*`, `stytch`

### Infrastructure Signals
- Auth0/Okta/WorkOS tenant configuration
- Redis/Memcached for session storage
- Rate limiting middleware or API gateway config (Kong, nginx rate_limit)
- Certificate configurations for SAML

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 3.1 SSO | Google/GitHub OAuth only | SAML 2.0 or OIDC with one IdP | Multi-IdP support, SCIM provisioning, JIT provisioning | Customer-managed IdP config, directory sync, SSO enforcement |
| 3.2 MFA | Email OTP or SMS codes | TOTP authenticator app support | WebAuthn/FIDO2, hardware key support | Org-level MFA enforcement policy, adaptive MFA, phishing-resistant only |
| 3.3 Session Mgmt | Fixed session timeout | Configurable timeout, idle expiry | Concurrent session limits, session revocation UI | Per-policy session rules, device binding, continuous auth signals |
| 3.4 Password | Basic length/complexity | bcrypt/argon2 hashing, breached password check | Configurable password policy per org | Customer-managed password policy, credential rotation enforcement |
| 3.5 Social Sign-In `[B2C]` | One social provider | Multiple social providers | Configurable per org (enable/disable) | Account linking, social-to-enterprise migration |
| 3.6 Consumer Passwordless `[B2C]` | Email magic link | SMS + email options | WebAuthn/passkey support | Multi-device passkey sync, platform authenticator |
| 3.7 Auth Rate Limiting | Global rate limit on login | Per-account lockout after N failures | Progressive delays, CAPTCHA integration | IP reputation, device fingerprinting, adaptive throttling |

---

## Domain 4: Authorization Architecture

### Grep Patterns
```
rbac|role.?based|permission|has.?role|check.?role|can\(|authorize|has.?permission
# L2 libraries (in-process):
casbin|casl|pundit|accesscontrol|django.?guardian
# L3+ services/engines (external process):
abac|attribute.?based|policy.?engine|opa|open.?policy|cerbos|oso|authzed|spicedb|openfga|cedar
api.?key|bearer|api.?token|scope|oauth.?scope|jwt.?claim|token.?scope
default.?role|default.?permission|least.?privilege|deny.?by.?default|allow.?list
resource.?owner|object.?level|bola|idor|ownership.?check|belongs.?to|tenant_id.*where
middleware.*auth|@authorize|@permission|policy.?check|guard|decorator.*permission
```

### Glob Patterns
```
**/permissions/**
**/authorization/**
**/authz/**
**/policies/**
**/rbac/**
**/roles/**
**/guards/**
**/middleware/auth*
**/casl/**
**/casbin/**
```

### Dependency Signals
- **RBAC**: `casl`, `casbin`, `accesscontrol`, `django-guardian`, `pundit` (Ruby), `spring-security` (Java)
- **Policy engines**: `@open-policy-agent/*`, `cerbos`, `oso`, `cedar-policy`
- **Zanzibar-style**: `@authzed/authzed-node`, `spicedb`, `openfga`, `permify`
- **API auth**: `passport-http-bearer`, `django-oauth-toolkit`, `doorkeeper` (Ruby)

### Infrastructure Signals
- OPA sidecar or Cerbos service in Docker/K8s
- SpiceDB/AuthZed deployment configs
- API gateway authorization policies (Kong, Envoy)
- IAM policy definitions in cloud provider configs

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 4.1 Permission Model | Basic roles (admin/member/viewer) | Configurable roles with permissions | Custom role builder, fine-grained permissions | Zanzibar-style relationship-based access, external policy engine |
| 4.2 Authz Implementation | Inline role checks in handlers | Middleware/decorator authorization layer, established library (Casbin, CASL) | Dedicated authz service (OPA, Cerbos, OpenFGA, SpiceDB) | Externalized authz service (AuthZed, Permit.io), policy-as-code |
| 4.3 API Authorization | API key per account | Scoped API tokens with permissions | OAuth2 with granular scopes, short-lived tokens | Customer-managed OAuth apps, token introspection, DPoP |
| 4.4 Permission Defaults | Admin-level defaults (too permissive) | Reasonable defaults, configurable | Least-privilege defaults, require explicit grants | Customer-defined default policies, deny-by-default provable |
| 4.5 Resource Authz (BOLA) | No explicit ownership checks | `WHERE tenant_id = ?` on queries | Resource-level ownership middleware, object-level checks | Automated BOLA testing, resource ACLs, delegation support |

> **Classification note — authz libraries vs services**: Casbin, CASL, Pundit, and accesscontrol are **L2 libraries** (run in-process, linked as a dependency). OPA, Cerbos, OpenFGA, SpiceDB, Cedar, and AuthZed are **L3+ services** (run as a separate process or external service). Finding Casbin in a codebase indicates L2 authz implementation; finding OPA/Cerbos indicates L3+. Do not conflate them.

---

## Domain 5: Multi-Tenancy & Isolation

### Grep Patterns
```
tenant.?id|org.?id|organization_id|account_id|workspace_id
tenant.?context|current.?tenant|set.?tenant|tenant.?middleware|tenant.?scope
cross.?tenant|tenant.?leak|data.?isolation|row.?level.?security|rls
noisy.?neighbor|rate.?limit.*tenant|quota|resource.?limit|fair.?share|throttl.*org
schema.?per.?tenant|database.?per.?tenant|namespace.*tenant
```

### Glob Patterns
```
**/tenant/**
**/tenancy/**
**/multi-tenant*
**/organization/**
**/workspace/**
**/middleware/*tenant*
```

### Dependency Signals
- **Multi-tenancy**: `django-tenants`, `apartment` (Ruby), `multitenancy` plugins, `@prisma/client` (with tenant filtering)
- **RLS**: PostgreSQL row-level security policies, `pgPolicy`, Citus data for distributed tenancy
- **Rate limiting**: `bottleneck`, `p-limit`, `redis` (for distributed quotas)

### Infrastructure Signals
- RLS policies in database migrations (`CREATE POLICY`)
- Schema-per-tenant or database-per-tenant patterns
- Network isolation (VPC per tenant, namespace per tenant in K8s)
- GCP project-per-tenant, Azure resource group per tenant for isolation
- K8s namespace isolation with NetworkPolicy per tenant
- Resource quotas in Kubernetes (`ResourceQuota`)
- Separate encryption keys per tenant in KMS

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 5.1 Data Isolation | `tenant_id` column on tables, basic WHERE clause filtering | Row-level security (RLS) in database | Schema-per-tenant or database-per-tenant | Customer-managed infrastructure, full physical isolation |
| 5.2 Cross-Tenant | Basic tenant scoping on queries | Automated tests for tenant leakage, middleware enforcement | Tenant context propagation across all services, integration tests | Formal isolation verification, pen test evidence, zero-trust between tenants |
| 5.3 Noisy Neighbor | No resource limits per tenant | Basic rate limiting per org/tenant | Per-tenant resource quotas (CPU, storage, API calls) | Customer-configurable limits, dedicated capacity option, SLA guarantees |

---

## Domain 6: Cryptographic Design

### Grep Patterns
```
tls|https|ssl|certificate|hsts|strict.?transport
encrypt.*at.?rest|aes|kms|envelope.?encrypt|field.?level.?encrypt|column.?encrypt
key.?management|key.?rotation|kms|vault|key.?store|master.?key|data.?key|cmek|customer.?managed.?key
crypto|cipher|hmac|sha256|sha512|ed25519|rsa|ecdsa|nacl|libsodium|subtle.?crypto
```

### Glob Patterns
```
**/crypto/**
**/encryption/**
**/certs/**
**/tls/**
**/kms/**
**/vault/**
**/keys/**
```

### Dependency Signals
- **TLS**: `helmet` (JS), `certbot`, `mkcert`
- **Encryption**: `@aws-sdk/client-kms`, `@google-cloud/kms`, `google-cloud-kms`, `@azure/keyvault-keys`, `azure-keyvault`, `cryptography` (Python), `nacl`, `libsodium-wrappers`
- **Vault**: `node-vault`, `hvac` (Python), `hashicorp/vault`
- **Field-level**: `@aws-crypto/client-node`, `mongoose-field-encryption`, `django-encrypted-model-fields`, `attr_encrypted` (Ruby)

### Infrastructure Signals
- TLS certificates and HSTS configuration in nginx/Caddy/load balancer
- KMS key definitions in Terraform (`aws_kms_key`, `google_kms_crypto_key`, `azurerm_key_vault_key`)
- Vault deployment and configuration
- Database encryption settings (RDS `storage_encrypted`, GCP `disk_encryption_key`, Azure `transparent_data_encryption`)
- Certificate management (cert-manager in K8s, ACM in AWS, Azure Key Vault certificates)

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 6.1 Data in Transit | TLS 1.2+ enforced, HSTS header | TLS 1.3, certificate pinning consideration | mTLS between services, internal TLS everywhere | Customer-verifiable TLS config, certificate transparency logging |
| 6.2 Data at Rest | Cloud provider default encryption | Application-level encryption on sensitive fields | Envelope encryption with dedicated KMS | CMEK (customer-managed encryption keys), BYOK |
| 6.3 Key Management | Cloud provider managed keys | Dedicated KMS, key rotation schedule | Per-tenant keys, automated rotation | Customer-managed keys in customer's KMS, key escrow options |
| 6.4 Crypto Primitives | Standard library crypto (no homebrew) | Vetted libraries (libsodium, AWS crypto SDK) | Documented crypto design, no deprecated algorithms | External crypto audit evidence, FIPS 140-2 modules |

---

## Domain 7: Secure Foundations

### Grep Patterns
```
auth0|okta|clerk|workos|supertokens|firebase.?auth|cognito|stytch|fusionauth
casbin|opa|cerbos|authzed|spicedb|openfga|cedar|zanzibar
bcrypt|argon2|scrypt|libsodium|nacl|aws.?kms|google.?kms|azure.?keyvault|hashicorp.?vault
express.?session|next.?auth|iron.?session|lucia|passport|devise|spring.?security.?session
custom.?auth|hand.?roll|home.?grown|diy.?auth|build.?our.?own
```

### Glob Patterns
```
**/auth/providers/**
**/lib/crypto*
**/utils/crypto*
**/lib/session*
**/utils/auth*
```

### Dependency Signals
Look for the presence of established auth/authz/crypto libraries vs custom implementations. Key signals:
- **Buy (good)**: `@auth0/*`, `@clerk/*`, `firebase-admin`, `passport`, `devise`, `spring-security`, `casbin`, `@aws-crypto/*`
- **Build (risky)**: Custom JWT signing, custom password hashing, hand-rolled session management, custom RBAC without established library

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 7.1 Auth Build vs Buy | Uses established auth library or service | Managed auth service (Auth0, Clerk, WorkOS) | Enterprise auth platform with SSO/SCIM/MFA built-in | Auth platform with compliance certifications, SOC 2 coverage |
| 7.2 Authz Build vs Buy | Uses established RBAC library | Dedicated authz library (Casbin, CASL, Pundit) | External policy engine (OPA, Cerbos, OpenFGA, Cedar) | Managed authz service (AuthZed, Permit.io), relationship-based |
| 7.3 Crypto Build vs Buy | Standard library crypto, no custom algorithms | Established crypto libraries (libsodium, AWS KMS SDK) | Managed KMS with envelope encryption | HSM-backed, FIPS-validated crypto provider |
| 7.4 Session Build vs Buy | Framework default sessions | Established session library with secure defaults | Managed session service, secure cookie config | Token-based with rotation, binding, and revocation capabilities |

---

## Domain 8: Infrastructure Security

### Grep Patterns
```
bastion|jump.?box|break.?glass|prod.?access|privileged.?access|session.?record
staging|production|environment.*isol|env.?separat|namespace.*prod|vpc.*prod
secret.?manag|vault|ssm|parameter.?store|doppler|1password|secrets.?provider
GITHUB_TOKEN|CI_TOKEN|DOCKER_.*TOKEN|cosign|sigstore|slsa|provenance|sbom|attestation
\.env|process\.env|os\.environ|os\.getenv|config\.secret
# Cloud provider coverage (AWS, GCP, Azure):
aws|amazon|gcp|google.?cloud|gcloud|azure|az.?cli
# CI/CD providers:
github.?actions|cloud.?build|azure.?devops|gitlab.?ci|circleci|jenkins
```

### Glob Patterns
```
**/terraform/**
**/pulumi/**
**/infra/**
**/infrastructure/**
**/k8s/**
**/kubernetes/**
**/helm/**
**/.github/workflows/*
**/Dockerfile*
**/docker-compose*
**/.env.example
**/cloudbuild*
**/Jenkinsfile
**/.gitlab-ci.yml
**/azure-pipelines.yml
**/circleci/**
```

### Dependency Signals
- **Secrets**: `@aws-sdk/client-secrets-manager`, `@google-cloud/secret-manager`, `google-cloud-secret-manager` (Python), `@azure/keyvault-secrets`, `azure-keyvault-secrets` (Python), `python-dotenv`, `doppler-sdk`
- **IaC**: `@cdktf/*`, `@pulumi/*`, `terraform` (check for provider configs), `@google-cloud/deploy`, `@azure/arm-*`
- **CI security**: `cosign`, `sigstore`, `syft` (SBOM), `grype` (vulnerability scanning)
- **Cloud CLIs/SDKs**: `@aws-sdk/*`, `@google-cloud/*`, `google-cloud-*` (Python), `@azure/*`, `azure-*` (Python), `boto3` (AWS Python)

### Infrastructure Signals
- **AWS**: VPC configurations, security groups, network ACLs, IAM roles with least privilege (no `*` permissions), CloudTrail, AWS SSO/Identity Center
- **GCP**: VPC networks, firewall rules, IAM bindings, Cloud Audit Logs, Cloud Build configs, GKE cluster configs, Workload Identity
- **Azure**: VNets, NSGs, Azure RBAC role assignments, Azure Monitor, Azure DevOps pipelines, AKS configs, Managed Identity
- **K8s**: RBAC (`Role`, `ClusterRole`), NetworkPolicy for pod-level isolation, `ResourceQuota`, `PodSecurityPolicy`/`PodSecurityStandard`
- Separate AWS accounts, GCP projects, or Azure subscriptions for staging/production
- Secret references (not inline secrets) in Terraform/Pulumi
- Container image signing and verification in CI pipelines (GitHub Actions, GCP Cloud Build, Azure DevOps, GitLab CI)
- SBOM generation in build pipeline

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 8.1 Prod Data Access | Named accounts for prod access, no shared credentials | MFA required for prod, access logging | Just-in-time access, session recording, break-glass procedures | Zero standing privileges, customer-auditable access logs |
| 8.2 Env Isolation | Separate configs for staging/prod | Separate infrastructure (VPCs, accounts) | Network isolation, no prod data in lower envs | Air-gapped environments, separate CI/CD per environment |
| 8.3 Secrets Mgmt | No hardcoded secrets (env vars at minimum) | Secrets manager (Vault, AWS SM, Doppler) | Automatic rotation, dynamic secrets, audit trail | Customer-managed secrets, zero-knowledge architecture |
| 8.4 Build & Artifact | Pinned dependencies, lockfile | Signed commits, protected main branch | Signed artifacts, SBOM generation, reproducible builds | SLSA Level 3+, provenance attestation, hermetic builds |

---

## Domain 9: Secure Development

### Grep Patterns
```
dependabot|renovate|snyk|npm.?audit|safety.?check|govulncheck|cargo.?audit
sast|semgrep|codeql|sonarqube|bandit|brakeman|gosec|security.?scan
threat.?model|security.?review|secure.?design|security.?requirements
cve|security.?advisory|vulnerability|patch|security.?fix|security.?update
security.?champion|security.?lead|codeowner.*security|security.?team
security\.txt|\.well-known/security|bug.?bounty|responsible.?disclosure
sanitiz|escape|xss|injection|parameteriz|prepared.?statement|html.?encode|dompurify|bleach
input.?validat|schema.?valid|zod|joi|yup|cerberus|pydantic|validate
```

### Glob Patterns
```
**/.github/dependabot.yml
**/.github/workflows/*security*
**/.github/workflows/*scan*
**/.github/SECURITY.md
**/SECURITY.md
**/.semgrep*
**/.snyk
**/sonar-project.properties
**/security.txt
**/.well-known/security.txt
**/CODEOWNERS
**/threat-model*
```

### Dependency Signals
- **Dependency scanning**: `dependabot` config, `renovate` config, `snyk`, `npm-audit-resolver`
- **SAST**: `semgrep`, `@semgrep/*`, `codeql`, `bandit` (Python), `brakeman` (Ruby), `gosec` (Go)
- **Validation**: `zod`, `joi`, `yup`, `class-validator` (TS), `pydantic` (Python), `govalidator` (Go), `dry-validation` (Ruby)
- **Sanitization**: `dompurify`, `sanitize-html`, `bleach` (Python), `bluemonday` (Go)

### Infrastructure Signals
- CI pipeline with security scanning steps
- Branch protection rules requiring security checks
- CODEOWNERS file with security team reviewers
- Vulnerability disclosure policy (security.txt, SECURITY.md)

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 9.1 Dependency Mgmt | Lockfile committed, manual updates | Dependabot/Renovate configured, automated PRs | SCA in CI pipeline (Snyk, npm audit), blocking on critical | License scanning, supply chain security (Sigstore verification) |
| 9.2 Security Testing | Linter with security rules | SAST in CI (Semgrep, CodeQL) | DAST/IAST integration, regular pen testing | Bug bounty program, continuous security testing, red team |
| 9.3 Secure Design | Ad hoc security reviews | Security review checklist for PRs | Formal threat modeling (STRIDE/PASTA), security design docs | Threat modeling integrated in SDLC, automated threat model updates |
| 9.4 Vulnerability Response | Respond to reported vulnerabilities | Defined SLA for patch response by severity | Public security advisories, CVE assignment | Coordinated disclosure program, pre-notification for critical |
| 9.5 Security Asset Attribution | Basic inventory of services | CODEOWNERS with security ownership | Security champions per team, asset classification | Automated asset discovery, security posture dashboard |
| 9.6 Security Program Mgmt | Ad hoc security efforts | Documented security policies | Security team or function, regular assessments | ISO 27001 / SOC 2 certified, continuous compliance monitoring |
| 9.7 Input Validation | Basic framework escaping | Schema validation on inputs (Zod, Joi, Pydantic) | Centralized validation middleware, output encoding | Content Security Policy, automated injection testing, WAF rules |

---

## Domain 10: Incident Response

### Grep Patterns
```
security\.txt|\.well-known/security|vulnerability.?disclosure|bug.?bounty|hackerone|bugcrowd
incident.?notif|breach.?notif|security.?notice|status.?page|incident.?communication
patch|hotfix|security.?release|emergency.?deploy|fast.?track
post.?mortem|incident.?review|retro|root.?cause|lessons.?learned|blameless
incident.?response|incident.?plan|runbook|playbook|on.?call|pagerduty|opsgenie
```

### Glob Patterns
```
**/security.txt
**/.well-known/security.txt
**/SECURITY.md
**/incident*
**/runbook*
**/playbook*
**/postmortem*
**/post-mortem*
**/status-page*
```

### Dependency Signals
- **Incident management**: `pagerduty`, `opsgenie`, `@statuspage/*`
- **Status page**: `statuspage`, `cachet`, `upptime`, `instatus`

### Infrastructure Signals
- Status page deployment (Statuspage.io, Atlassian Statuspage, self-hosted)
- PagerDuty/OpsGenie/incident.io integration configs
- Runbook automation (Rundeck, AWS SSM Run Command)
- Automated rollback configurations in CI/CD

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 10.1 Vulnerability Disclosure | security.txt or SECURITY.md with contact info | Bug bounty program (HackerOne, Bugcrowd) | Managed VDP with defined scope and SLAs | Public bounty with competitive payouts, safe harbor policy |
| 10.2 Incident Notification | Email notification process documented | Status page for real-time updates | Customer-specific notification (in-app, webhook) | Pre-notification for critical issues, contractual SLAs |
| 10.3 Patch Response | Patches applied within weeks | Defined SLA by severity (critical < 24h) | Automated patching pipeline, fast-track deployment | Zero-day response capability, customer-auditable patch timeline |
| 10.4 Post-Incident | Internal incident review | Published post-mortems for major incidents | Structured blameless retro process, action items tracked | Customer-visible post-incident reports, continuous improvement metrics |

---

## Domain 11: AI/ML Controls (optional)

### Grep Patterns
```
training.?data|dataset|data.?label|data.?annotation|model.?train|fine.?tun
temperature|top.?p|max.?tokens|model.?config|system.?prompt|model.?param
ai.?log|llm.?log|model.?observ|prompt.?log|completion.?log|langsmith|langfuse|helicone
content.?filter|output.?filter|guardrail|toxicity|moderation|safety.?check|content.?polic
human.?review|human.?in.?the.?loop|hitl|approval.?workflow|human.?override|escalat.*human
prompt.?inject|jailbreak|input.?sanitiz.*prompt|llm.?guard|rebuff|prompt.?armor
ai.?disclos|model.?card|ai.?transparen|data.?processing.?disclosure
opt.?out.*ai|ai.*opt.?out|training.*consent|ai.*consent|data.*ai.*polic
model.?hash|model.?sign|model.?version|model.?registry|model.?provenance|hugging.?face.*token
tool.?call|function.?call|agent.*tool|tool.?use|mcp|model.?context.?protocol|tool.?permiss
context.?window|memory.*persist|conversation.?history|context.?isolat|memory.*tenant
```

### Glob Patterns
```
**/ai/**
**/ml/**
**/models/**
**/llm/**
**/prompts/**
**/guardrails/**
**/agents/**
**/langchain*
**/training/**
**/model-cards/**
```

### Dependency Signals
- **LLM frameworks**: `langchain`, `llama-index`, `semantic-kernel`, `autogen`, `crewai`, `openai`, `anthropic`, `@google/generative-ai`
- **Guardrails**: `guardrails-ai`, `nemo-guardrails`, `rebuff`, `llm-guard`, `openai-moderation`
- **Observability**: `langsmith`, `langfuse`, `helicone`, `promptlayer`, `weights-and-biases`
- **Vector stores**: `pinecone`, `chromadb`, `weaviate`, `qdrant`, `pgvector`
- **Agent frameworks**: `@modelcontextprotocol/*`, `autogen`, `crewai`, `langchain-agents`

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 11.1 Training Data | Data source documented | Data provenance tracking, PII filtering | Consent-based training data, opt-out mechanism | Customer-controlled training data, no cross-tenant training |
| 11.2 Model Config | Hardcoded model parameters | Configurable temperature/model per use case | Per-tenant model configuration, model selection | Customer-managed model deployment, private model hosting |
| 11.3 AI Observability | Basic input/output logging | Structured prompt/completion logging (Langfuse, Langsmith) | Per-tenant AI audit trail, cost tracking | Customer-accessible AI usage dashboard, exportable AI logs |
| 11.4 Output Safety | Basic content filtering | Guardrails library integration, moderation API | Multi-layer safety (input + output filters, context grounding) | Customer-configurable safety policies, domain-specific filters |
| 11.5 Human Oversight | Manual review available | Approval workflows for high-risk actions | Configurable escalation rules, human-in-the-loop options | Customer-defined oversight policies, kill switch |
| 11.6 Input Security | Basic input length limits | Prompt injection detection (LLM Guard, Rebuff) | System prompt isolation, input/output separation | Adversarial testing, automated red-teaming, certified resilience |
| 11.7 AI Transparency | AI usage mentioned in ToS | Model card or AI disclosure page | Per-feature AI disclosure, data processing transparency | Customer-auditable AI pipeline, explainability APIs |
| 11.8 AI Data Rights | Documented AI data policy | Opt-out from AI training | Granular consent per AI feature | Customer-controlled data boundaries, contractual AI data terms |
| 11.9 Model Supply Chain | Using known model providers | Model versioning, pinned model versions | Model integrity verification, SBOM for models | Customer-managed model selection, signed model artifacts |
| 11.10 Agent Auth & Tool Control | Tools hardcoded, no scope limits | Tool allowlist per agent, basic permission checks | Per-tenant tool authorization, scope restrictions | Customer-managed tool policies, audit trail per tool invocation |
| 11.11 Memory & Context | No persistent memory, or undocumented | Conversation history with TTL | Per-tenant context isolation, memory boundaries | Customer-managed memory policies, verifiable context isolation |

---

## Domain 12: Integration Security

### Grep Patterns
```
integrat.*inventory|app.?directory|marketplace|connected.?app|installed.?app|third.?party.?app
token.?lifecycle|token.?expir|token.?rotat|refresh.?token|token.?revok|api.?key.?rotat
oauth.?scope|scope.?request|permission.?request|scope.?limit|minimal.?scope|scope.?review
actor.*integrat|integration.*actor|service.?account|api.?key.*actor|bot.?user|app.?user
integration.*event|webhook.*log|api.*audit|app.*install.*log|integration.*audit
revok.*token|revok.*access|disconnect.*app|uninstall.*integrat|remove.*access|deauthoriz
```

### Glob Patterns
```
**/integrations/**
**/marketplace/**
**/apps/**
**/oauth/**
**/webhooks/**
**/api-keys/**
**/connected-apps/**
**/third-party/**
```

### Dependency Signals
- **OAuth**: `passport-oauth2`, `django-oauth-toolkit`, `doorkeeper` (Ruby), `spring-security-oauth2` (Java), `golang.org/x/oauth2` (Go)
- **Webhook**: `svix`, `standardwebhooks`, `webhook-relay`
- **API management**: `kong`, `gravitee`, `tyk`

### Infrastructure Signals
- OAuth2 server configuration
- Webhook delivery infrastructure with retry logic
- API gateway with per-integration rate limiting
- Token storage with encryption at rest

### Level Evidence Map

| Control | L1 | L2 | L3 | L4 |
|---------|----|----|----|----|
| 12.1 Integration Inventory | List of known integrations documented | Admin UI showing installed/connected apps | Integration health dashboard, usage metrics | Customer-managed app catalog, approval workflows |
| 12.2 Token Lifecycle | Long-lived tokens, manual revocation | Token expiration, refresh token rotation | Automatic rotation, short-lived tokens (< 1h) | Customer-managed token policies, zero-trust token architecture |
| 12.3 Scope Governance | Single broad scope per integration | Per-integration scope selection | Admin-controlled scope limits, scope review UI | Customer-defined scope policies, just-in-time scope elevation |
| 12.4 Actor Attribution | Actions attributed to "API" generically | Integration name in audit logs | Per-token actor identity, unique service accounts per integration | Customer-visible integration actor trail, delegated auth chain |
| 12.5 Integration Audit Events | Basic install/uninstall logging | Scope changes and token events logged | Per-integration activity dashboard, anomaly detection | Real-time integration audit stream, customer-exportable |
| 12.6 Revocation | Manual token deletion | One-click disconnect in UI, token cascade revocation | Automated revocation on anomaly, webhook delivery of revocation | Customer-managed revocation policies, cross-system propagation |

---

## Cross-Cutting Search Strategy

When beginning an assessment, run these searches first to orient yourself:

### Step 1: Identify the stack
```
Glob: **/package.json, **/requirements.txt, **/go.mod, **/Gemfile, **/Cargo.toml, **/pom.xml, **/build.gradle, **/*.csproj
```

### Step 2: Find auth infrastructure
```
Grep: auth0|clerk|okta|workos|cognito|firebase.?auth|supertokens|passport|devise|spring.security
Glob: **/auth/**, **/authentication/**, **/login/**
```

### Step 3: Find data layer
```
Grep: prisma|sequelize|typeorm|knex|mongoose|sqlalchemy|gorm|activerecord|entity.?framework
Glob: **/models/**, **/entities/**, **/schema/**, **/migrations/**
```

### Step 4: Find infrastructure
```
Glob: **/terraform/**, **/pulumi/**, **/infra/**, **/.github/workflows/**, **/Dockerfile*, **/k8s/**, **/helm/**
```

### Step 5: Find security configuration
```
Glob: **/SECURITY.md, **/security.txt, **/.well-known/security.txt, **/.semgrep*, **/.snyk, **/CODEOWNERS
Grep: security.?polic|security.?header|helmet|csp|content.security.policy
```

### Step 6: Check for AI/ML (determines if Domain 11 applies)
```
Grep: openai|anthropic|langchain|llama.?index|hugging.?face|transformers|tensorflow|pytorch
Glob: **/ai/**, **/ml/**, **/llm/**, **/prompts/**, **/agents/**
```

## Pattern Matching Tips

- **Multi-language awareness**: The same concept has different names. Authorization middleware might be `@authorize` (C#), `@login_required` (Python), `before_action :authenticate` (Ruby), `authMiddleware` (JS/Go).
- **Framework conventions**: Rails uses `app/policies/` (Pundit), Django uses `permissions.py`, Spring uses `@PreAuthorize`, Express uses middleware functions.
- **False positives**: `export` matches both data export and JS module exports. Use context (file location, surrounding code) to disambiguate. Prefer searching in route/controller/service files for data export evidence.
- **Absence is evidence**: If you search for `tenant_id` and find nothing in a multi-user app, that IS a finding (lack of tenant isolation). Document what you did NOT find as clearly as what you did find.
- **Config over code**: Many security controls live in configuration (Terraform, K8s manifests, CI configs, `.env.example`) rather than application code. Always check infrastructure files.
- **Test files matter**: Security tests (`**/test/**/auth*`, `**/spec/**/security*`) are strong positive signals. A test for tenant isolation is evidence that tenant isolation was intentionally designed.

### Common False Positives

| Pattern | False Positive | How to Disambiguate |
|---------|---------------|---------------------|
| `export` | JS/TS module exports (`export default`, `export const`) | Check file path: `src/api/export*` = data export. Check for CSV/JSON generation. |
| `session` | Express session middleware vs. browser sessionStorage | Check dependency context: `express-session` = server session. `sessionStorage` = client-side. |
| `encrypt` | bcrypt password hashing vs. data-at-rest encryption | bcrypt = password hashing (Domain 3.4). `aes`/`kms`/`envelope` = encryption (Domain 6). |
| `audit` | npm audit (dependency scanning) vs. audit logging | `npm audit` in CI = Domain 9.1. `auditLog`/`audit_event` in app code = Domain 2.1. |
| `token` | JWT auth tokens vs. API tokens vs. CSRF tokens | Check context: auth middleware = Domain 3/4. API key management = Domain 12. CSRF = neither. |
| `policy` | IAM policies vs. app authorization policies vs. content policies | Terraform IAM = Domain 8. Casbin/OPA model = Domain 4. Content moderation = Domain 11.4. |
| `secret` | App secrets (API keys) vs. K8s Secrets vs. client_secret | Infrastructure secrets mgmt = Domain 8.3. OAuth client_secret = Domain 4.3. |
