---
name: traction-assess
description: This skill should be used when the user asks to "assess my security maturity", "run a TRACTION assessment", "evaluate product security", "check enterprise readiness", "security maturity audit", or mentions TRACTION framework assessment. Systematically explores the codebase against 12 security domains and 67 controls to produce a scored maturity assessment with tier determination.
---

# TRACTION Security Maturity Assessment

TRACTION (Trust · Readiness · Access · Controls · Transparency · Infrastructure · Ops · Notification) is a framework by [Adversis](https://adversis.io) for evaluating SaaS product security maturity. This skill systematically explores the codebase to produce an evidence-based maturity assessment across 12 security domains with 67 controls, scoring each control L0–L4 with specific file-level citations. The output is a terminal report plus a JSON file importable into the [TRACTION web app](https://www.adversis.io/traction/assess).

## Assessment Workflow

Execute these 5 phases in order. Do not skip phases.

### Phase 1: Gather Context

Use AskUserQuestion to collect three pieces of information before starting:

1. **Product name** — What is this product called?
2. **Business model** — B2B, B2C, or both? (Determines which controls are relevant — some controls are B2B-only or B2C-only)
3. **AI/ML features** — Does this product include AI/ML features? (Determines whether Domain 11 is assessed)

### Phase 2: Codebase Reconnaissance

Launch parallel Explore agents to map the codebase structure. Identify:

- **Tech stack**: Language(s), framework(s), runtime
- **Auth layer**: Where authentication and authorization code lives
- **API layer**: Route definitions, middleware, controllers
- **Data layer**: Database models, migrations, ORM configuration
- **Infrastructure**: Terraform, Pulumi, Docker, Kubernetes, CI/CD configs
- **Security-specific**: Middleware, guards, policies, encryption, logging
- **Documentation**: Security policies, incident plans, design docs
- **Dependencies**: package.json, requirements.txt, go.mod, Gemfile, Cargo.toml, *.csproj

Use Glob patterns like:
- `**/auth/**`, `**/middleware/**`, `**/guards/**`, `**/policies/**`
- `**/config/**`, `**/infra/**`, `**/terraform/**`, `**/pulumi/**`
- `**/.github/**`, `**/Dockerfile*`, `**/docker-compose*`, `**/k8s/**`
- `**/.well-known/**`, `**/security.txt`
- `**/docs/**`, `**/security/**`

Record findings in this format before proceeding to Phase 3:

| Layer | Technology | Key Paths |
|-------|-----------|-----------|
| Language/Framework | e.g. TypeScript, Next.js | |
| Auth | e.g. Auth0, passport | src/auth/ |
| API | e.g. Express REST | src/api/, src/routes/ |
| Data | e.g. Prisma, PostgreSQL | src/models/, prisma/ |
| Infrastructure | e.g. Terraform, AWS | terraform/, .github/workflows/ |
| Security | e.g. helmet, Semgrep | src/middleware/, .semgrep.yml |
| Dependencies | package.json | package.json |

**Example (filled out):**

| Layer | Technology | Key Paths |
|-------|-----------|-----------|
| Language/Framework | TypeScript, Next.js 14 | `tsconfig.json`, `next.config.js` |
| Auth | Auth0 (passport-saml, openid-client) | `src/auth/`, `src/middleware/auth.ts` |
| API | Express REST | `src/api/`, `src/routes/` |
| Data | Prisma, PostgreSQL | `src/models/`, `prisma/schema.prisma`, `prisma/migrations/` |
| Infrastructure | Terraform (AWS), GitHub Actions | `terraform/`, `.github/workflows/` |
| Security | helmet, Semgrep, Zod | `src/middleware/security.ts`, `.semgrep.yml`, `src/middleware/validation.ts` |
| Dependencies | package.json, package-lock.json | `package.json` |

**Exit criteria**: Recon is complete when every row has an entry or "Not found". Missing layers are signal — a product with no infrastructure code likely has all infra managed externally (score accordingly in Domain 8). A product with no `auth/` directory may use a third-party auth service configured outside the repo (note in Domain 3/7 evidence). Proceed to Phase 3 only when the table is filled.

### Phase 3: Domain-by-Domain Assessment

For each theme group, read the corresponding domain reference file and `code-signals.md`, then systematically search the codebase.

**Assessment order** (by theme):
1. **Identity & Access** — Read `references/domains-identity-access.md`. Assess Domains 3, 4, 5, 12.
2. **Data & Transparency** — Read `references/domains-data-transparency.md`. Assess Domains 1, 2, 6.
3. **Platform Security** — Read `references/domains-platform-security.md`. Assess Domains 7, 8, 9.
4. **Response & Compliance** — Read `references/domains-response-compliance.md`. Assess Domains 10, 11 (if AI/ML applies).

For each domain:
1. Read the domain reference to understand what each level requires
2. Consult `references/code-signals.md` for grep patterns, glob patterns, and dependency signals
3. Execute searches using Grep and Glob tools — search for the patterns listed in code-signals.md
4. Read relevant files found to understand implementation depth
5. Record findings: what was found, where, and what level it indicates

Parallelize across domains within each theme group where possible. Use parallel Grep and Glob calls to search for multiple domain patterns concurrently within each theme group, or use the Agent tool (subagent_type: "general-purpose") to assess independent domains in parallel.

### Phase 4: Score and Calculate

For each control, assign a level (0–4 or -1 for N/A):

- **0** — No relevant code or configuration found
- **1** — L1 criteria fully met
- **2** — L1 + L2 criteria fully met
- **3** — L1 + L2 + L3 criteria fully met
- **4** — All levels fully met
- **-1 (N/A)** — Control does not apply (e.g., B2C-only control for B2B product, or no AI features for Domain 11)

**Calculate tier** using these thresholds (excluding N/A controls):

| Tier | Requirement |
|------|-------------|
| **Certified** | All controls at L1+ (100% L1 coverage) |
| **Growth** | All L1 + 80% of controls at L2+ |
| **Enterprise** | All L2 + 80% of controls at L3+ |
| **Advanced** | All L3 + 50% of controls at L4 |

A product achieves the highest tier where ALL requirements are met. If not all controls are at L1, the tier is "Not Yet Certified."

#### Tier Calculation Details

Exclude N/A (-1) controls from both numerator and denominator:

```
applicable = controls where level >= 0
atL1 = applicable controls where level >= 1
atL2 = applicable controls where level >= 2
atL3 = applicable controls where level >= 3
atL4 = applicable controls where level >= 4

Not Yet Certified: atL1 < len(applicable)
Certified:         atL1 == len(applicable)
Growth:            Certified AND atL2 / len(applicable) >= 0.80
Enterprise:        atL2 == len(applicable) AND atL3 / len(applicable) >= 0.80
Advanced:          atL3 == len(applicable) AND atL4 / len(applicable) >= 0.50
```

Coverage percentages for the report:

```
l1 = atL1 / len(applicable) * 100
l2 = atL2 / len(applicable) * 100
l3 = atL3 / len(applicable) * 100
l4 = atL4 / len(applicable) * 100
```

When `aiMlIncluded` is false, all Domain 11 controls are marked N/A (-1) and excluded from tier calculation. The denominator becomes ~56 controls instead of 67.

#### Worked Example

56 applicable controls (AI/ML excluded).
55 at L1+, 45 at L2+, 3 at L3+, 0 at L4.

```
L1 coverage: 55/56 = 98% → Not all L1, so tier = "Not Yet Certified"
(If all 56 were L1+: Certified. If also 45/56 = 80% L2+: Growth.)
```

Another example — 56 applicable, 56 at L1+, 45 at L2+, 3 at L3+, 0 at L4:

```
atL1 == 56 == len(applicable)          → Certified ✓
atL2 / len(applicable) = 45/56 = 80%  → ≥ 80%, Growth ✓
atL2 == 45 ≠ 56                        → Not all L2, Enterprise ✗
Tier = Growth
Coverage: L1=100%, L2=80%, L3=5%, L4=0%
```

**Generate gap list**: For each control below the next tier's threshold, calculate the impact of leveling it up. Rank by tier impact — controls that would move the needle most toward the next tier come first.

### Phase 5: Output Results

Produce two outputs:

#### A. Terminal Report

Print a structured markdown report to the terminal:

```
# TRACTION Assessment: [Product Name]
**Date**: [YYYY-MM-DD]
**Tier Achieved**: [tier name]
**Assessment**: Evidence-based codebase analysis

## Coverage
| Level | Coverage |
|-------|----------|
| L1    | XX%      |
| L2    | XX%      |
| L3    | XX%      |
| L4    | XX%      |

## Domain Summary
| Domain | Controls | Avg Level | Key Findings |
|--------|----------|-----------|--------------|
| 1. Customer Data Controls | X/6 | X.X | [1-line summary] |
| 2. Customer Observability | X/6 | X.X | [1-line summary] |
| ... | ... | ... | ... |

## Top 5 Recommendations
1. **[Control Name] (X.X): L[current] → L[next]** — [specific action to take]. Impact: [why this matters for tier progression].
2. ...

## JSON Output
Assessment saved to `traction-assessment.json` — import at https://www.adversis.io/traction/assess
```

#### B. JSON File

Write a JSON file to the project root as `traction-assessment.json`. Format:

```json
{
  "productName": "...",
  "assessedAt": "ISO-8601 timestamp",
  "assessedBy": "traction-assess (Claude Code plugin)",
  "tier": "Growth",
  "coverage": { "l1": 100, "l2": 85, "l3": 40, "l4": 10 },
  "aiMlIncluded": true,
  "assessments": [
    { "control": "1.1", "level": 2 },
    ...
  ],
  "notes": {
    "1.1": "Evidence citation with file paths and what was found",
    ...
  }
}
```

The `assessments` array and top-level fields are compatible with the TRACTION web app import. The `notes` object provides per-control evidence for verification.

## Evidence Rules

These rules ensure honest, useful assessments:

1. **Score the highest level where ALL criteria are met**, not the highest where ANY criterion is met. A control at L3 means L1, L2, AND L3 are all satisfied.

2. **Cite specific file paths** and describe what was found. Good: "Found SAML integration at `src/auth/saml.ts` using passport-saml." Bad: "SSO appears to be implemented."

3. **Incomplete or TODO'd code scores one level below.** If you find `// TODO: implement SCIM` in an SSO file, the SSO implementation is real but SCIM is not — score based on what's actually implemented.

4. **Mark N/A (-1) only when the control genuinely does not apply.** Examples: B2C-only controls (1.5, 1.6, 3.5, 3.6) for a pure B2B product. Domain 11 when the product has no AI/ML features. An internal tool with no customer-facing data export need.

5. **Mark 0 when no relevant code is found.** This is valuable information. A score of 0 on audit logging means "no audit logging was found in the codebase" — that's a clear gap, not an error.

6. **When uncertain between two levels, score conservatively** (lower) and explain what additional evidence would raise the score.

7. **Configuration counts.** Terraform, Helm charts, nginx configs, CI/CD workflows, and documentation are all valid evidence. Security is not only in application code.

8. **Absence of evidence is not evidence of absence**, but score what you can verify. Note when a capability might exist outside the codebase (e.g., WAF configured in cloud console, third-party SaaS tools).

## Control Applicability

Some controls are specific to a business model. Mark as N/A (-1) when the control doesn't apply.

| Control | Applies To | Rationale |
|---------|-----------|-----------|
| 1.5 Consumer Privacy | B2C / Both | Cookie consent, consumer privacy dashboard |
| 1.6 Account Recovery | B2C / Both | Consumer account recovery flows |
| 3.5 Social Sign-In | B2C / Both | Social login for consumers |
| 3.6 Consumer Passwordless | B2C / Both | Magic links, passkeys for consumers |
| 11.* (all) | AI/ML products only | Skip entire domain if no AI/ML features |

All other controls apply to both B2B and B2C products.

## Scenario Guidance

Common edge cases and how to handle them:

| Scenario | Guidance |
|----------|----------|
| Single-tenant / on-prem | Domain 5 (Multi-tenancy) → all N/A. Domain 1.3 (Residency) may be N/A if customer hosts. |
| API-only (no UI) | Controls referencing "UI" (1.1 L2 "Self-service export UI", 2.1 L3 "Customer-accessible audit log UI") — score based on API equivalents. L2 "UI export" = L2 "API export". |
| Internal tool | Controls 3.5, 3.6 (consumer auth), 1.5 (consumer privacy) → N/A. Tier thresholds still apply to remaining controls. |
| Monorepo | Scope the assessment to one product/service boundary. State scope in `productName`. |
| Frontend-only repo | Infrastructure controls (Domain 8) likely not assessable from this repo. Note as "Evidence not available in this codebase — may exist in separate infra repo." Score 0, not N/A. |
| API consumer only (no training) | 11.1 L3 "isolated training pipelines" likely N/A for self-hosted training. Focus on provider DPAs, data handling policies. Score based on API consumer criteria in the reference. |
| RAG-based product | 11.1 scores based on vector store data governance. 11.11 memory controls apply to retrieval context. Check for embedding pipeline security. |
| Multi-model / multi-provider | 11.9 supply chain controls are critical. Check for provider fallback strategies, version pinning across providers. |
| Agentic AI with tool use | 11.10 and 11.11 are critical differentiators. Check for tool authorization patterns, IBAC, delegation trust chains. |

## Handling Ambiguous Findings

When search patterns return ambiguous results:

1. **Check file path context** — `src/api/export.ts` is likely data export; `src/utils/index.ts` with `export default` is not.
2. **Read surrounding code** — Look at the function/class to determine intent.
3. **Exclude test fixtures and node_modules** — Focus on `src/`, `app/`, `lib/`, and infrastructure files.
4. **Note uncertainty** — When a finding could go either way, note it: "Found `export` in `src/data/handler.ts` — appears to be data export based on CSV generation logic, but could be module export. Scored as L2 conservatively."

## AI Bridge Domain Guidance

When assessing Domain 11, check for consistency with foundational domains. AI controls build on existing security architecture — a high AI score with weak foundations is suspicious.

| AI Control | Depends On | Consistency Check |
|------------|-----------|-------------------|
| 11.10 Agent Authorization | Domain 4 (Authz) | Agent authorization can't meaningfully exceed the underlying authorization architecture. If Domain 4 is L1, 11.10 at L3 is suspicious. |
| 11.1 AI Data Governance | Domain 1 (Data Controls) | AI data governance builds on data export, deletion, and residency controls. Check that data rights apply to AI-processed data too. |
| 11.3 AI Observability | Domain 2 (Observability) | AI observability extends audit logging infrastructure. If audit logs (2.1) are L1, AI observability at L3 is unlikely. |
| 11.* (all AI) | Domain 3 (Auth) | AI features need the same SSO, MFA, and session security as the rest of the product. AI endpoints without auth are a critical gap. |

When you find a disparity (e.g., Domain 4 at L1 but 11.10 at L3), note it in the evidence and verify the AI control score isn't inflated.

## Tier Reference

| Tier | Requirement | Target | Stage |
|------|-------------|--------|-------|
| Certified | All L1 | SMB, early enterprise | Pre-Series A |
| Growth | All L1 + 80% L2 | Mid-market | Series A/B |
| Enterprise | All L2 + 80% L3 | Large enterprise | Series B/C |
| Advanced | All L3 + 50% L4 | Regulated industries | Series C+ |

## Level Philosophy

- **L1**: Table stakes. Minimum viable security. "Won't get laughed out of the room."
- **L2**: Growth stage. Self-service, configurable. "Ready for serious enterprise pilots."
- **L3**: Enterprise ready. Full control, integration-friendly. "Can close F500 deals."
- **L4**: Best in class. Customer-managed, zero-trust. "Regulated industry ready."

## Domain Reference Files

Consult these files during Phase 3 for full control definitions and level criteria:

- `references/domains-identity-access.md` — Domains 3 (Auth), 4 (Authz), 5 (Multi-tenancy), 12 (Integrations)
- `references/domains-data-transparency.md` — Domains 1 (Data Controls), 2 (Observability), 6 (Crypto)
- `references/domains-platform-security.md` — Domains 7 (Foundations), 8 (Infrastructure), 9 (Secure Dev)
- `references/domains-response-compliance.md` — Domains 10 (Incident Response), 11 (AI/ML)
- `references/code-signals.md` — Grep patterns, glob patterns, and level evidence maps for all 12 domains

## Example Output

See `examples/sample-output.json` for a complete example assessment JSON output (AI excluded) and `examples/sample-output-ai.json` for an example with Domain 11 AI controls assessed. See `examples/sample-terminal-report.md` for the corresponding terminal report format.
