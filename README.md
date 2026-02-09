# traction-assess

Claude Code plugin for [TRACTION](https://www.adversis.io/traction) — evidence-based product security maturity assessment, run directly against your codebase.

## What is TRACTION?

TRACTION is a framework by [Adversis](https://adversis.io) for evaluating SaaS product security maturity across 12 domains and 67 controls with 4 maturity levels each.

## Install

```bash
claude plugin install traction-assess
```

Or install from a local path:

```bash
claude plugin install /path/to/traction-assess
```

## Usage

In any codebase:

```
/traction-assess
```

Claude will:
1. Ask about your product (name, B2B/B2C, AI/ML features)
2. Explore your codebase structure
3. Search for security-relevant patterns across all 12 domains
4. Score each of 67 controls (L0–L4) with file-level evidence citations
5. Calculate your tier (Certified → Growth → Enterprise → Advanced)
6. Print a terminal report with top recommendations
7. Save `traction-assessment.json` — importable into the TRACTION web app

## What You Get

**Terminal report** with:
- Tier achieved and coverage percentages
- Per-domain summary with average levels
- Top 5 recommendations ranked by tier impact

**JSON file** (`traction-assessment.json`) with:
- Per-control scores compatible with TRACTION web app import
- Evidence notes citing specific files for each score
- Import at [www.adversis.io/traction/assess](https://www.adversis.io/traction/assess)

## How It Works

This is **evidence-based assessment, not a questionnaire**. The skill searches your actual code — auth middleware, API routes, infrastructure configs, CI/CD pipelines, security policies — and cites specific files as evidence for each score.

Example finding:
> **3.1 SSO (L2)**: Found SAML integration at `src/auth/saml.ts` using passport-saml and OIDC at `src/auth/oidc.ts`. No SCIM provisioning found — would need SCIM for L3.

## Tiers

| Tier | Requirement | Target |
|------|-------------|--------|
| Certified | All controls at L1+ | SMB, early enterprise |
| Growth | All L1 + 80% at L2+ | Mid-market |
| Enterprise | All L2 + 80% at L3+ | Large enterprise |
| Advanced | All L3 + 50% at L4 | Regulated industries |

## Links

- [TRACTION Web App](https://www.adversis.io/traction/assess) — visual assessment tool and result viewer
- [Adversis](https://adversis.io) — product security for scaling teams
