# TRACTION Assessment: Example SaaS

**Date**: 2025-01-15
**Tier Achieved**: Growth
**Assessment**: Evidence-based codebase analysis

## Coverage

| Level | Coverage | Controls |
|-------|----------|----------|
| L1    | 100%     | 56/56    |
| L2    | 80%      | 45/56    |
| L3    | 5%       | 3/56     |
| L4    | 0%       | 0/56     |

> **Note**: Domain 11 (AI/ML) excluded — product has no AI/ML features. 56 applicable controls out of 67 total.

## Domain Summary

| Domain | Controls | Avg Level | Key Findings |
|--------|----------|-----------|--------------|
| 1. Customer Data Controls | 6/6 | 1.8 | Self-service export (CSV/JSON), soft-delete with grace period. Region selection at signup, configurable retention. |
| 2. Customer Observability | 6/6 | 1.7 | Admin action audit logs, 90-day retention. CSV/JSON log export from admin UI. Basic alerting on sensitive actions. |
| 3. Authentication Features | 7/7 | 2.1 | SAML + OIDC SSO, TOTP MFA, magic links. Social sign-in with account linking. No SCIM, no hardware keys. |
| 4. Authorization Architecture | 5/5 | 2.0 | Casbin-based RBAC with middleware layer. OAuth 2.0 with scoped JWT. UUIDs + ownership checks. |
| 5. Multi-Tenancy & Isolation | 3/3 | 1.7 | PostgreSQL RLS policies, `SET app.current_tenant` in middleware. Per-tenant rate limiting. App-layer cross-tenant filtering. |
| 6. Cryptographic Design | 4/4 | 1.8 | TLS 1.3 with HSTS. AWS KMS for key management. No field-level encryption. |
| 7. Secure Foundations | 4/4 | 2.3 | Auth0 for identity, Casbin for authz, AWS KMS for crypto. Redis sessions with secure config. |
| 8. Infrastructure Security | 4/4 | 2.0 | VPC with private subnets, AWS Secrets Manager. Approval-based prod access, signed commits in CI. |
| 9. Secure Development | 7/7 | 1.7 | Dependabot + Snyk in CI, Semgrep SAST, Zod validation. Security in design docs. No formal threat modeling, no SBOM. |
| 10. Incident Response | 4/4 | 2.0 | security.txt published, status page, 7-day critical SLA. Published post-mortems with customer impact. |
| 12. Integration Security | 6/6 | 1.5 | Connected apps UI, OAuth with 90-day expiry. No bulk revoke, limited actor attribution. |

## Top 5 Recommendations

1. **Data at Rest Encryption (6.2): L1 → L2** — Add application-level encryption for sensitive fields (PII, secrets) using envelope encryption with AWS KMS. Currently relying on RDS platform-default encryption only. Impact: Defense-in-depth for sensitive data; common enterprise security questionnaire requirement.

2. **Key Management (6.3): L1 → L2** — Implement key rotation schedule with AWS KMS. Currently using KMS but with no rotation configured. Impact: Limits key exposure window; required for SOC 2 CC6.1 and PCI DSS 3.6.

3. **Integration Inventory (12.1): L1 → L2** — Add connected-apps visibility to the admin UI showing OAuth apps, API keys, and last-used timestamps. Currently documentation-only. Impact: Enables customer audit of their attack surface; table stakes for enterprise security teams.

4. **Cross-Tenant Protection (5.2): L1 → L2** — Add automated tests for tenant leakage and enforce tenant context via middleware across all services. Currently app-layer filtering only without RLS-level enforcement of cross-tenant checks. Impact: Prevents the most common multi-tenancy vulnerability — a missed WHERE clause.

5. **Security Program Mgmt (9.6): L1 → L2** — Create a structured security backlog with dedicated issue types, severity SLAs, and leadership visibility. Currently using generic Jira labels. Impact: Makes security work visible and trackable; foundation for scaling security as the team grows.

> **Next tier target**: Enterprise requires all controls at L2+ and 80% at L3+. Current gaps: 11 controls at L1 need to reach L2, then begin L3 investments in strongest domains (Auth, Foundations, Crypto).

## JSON Output

Assessment saved to `traction-assessment.json` — import at https://www.adversis.io/traction/assess
