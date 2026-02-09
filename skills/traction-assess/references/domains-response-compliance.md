# Response & Compliance Domains

## Domain 10: Incident Response
Focus: What happens when there's a security issue

### 10.1 Vulnerability Disclosure
**Question**: How can researchers report?

- **L1** — Email (security@): Security email address published for reports.
  - Why: Basic channel. Better than nothing.
- **L2** — security.txt published: security.txt file with contact, policy, PGP key.
  - Why: Standard format. Easy for researchers to find.
- **L3** — Formal VDP with safe harbor: Vulnerability disclosure policy with legal safe harbor.
  - Why: Encourages responsible disclosure. Legal protection for researchers.
- **L4** — Bug bounty with rewards: Paid bug bounty program with defined scope and rewards.
  - Why: Incentivizes research. Continuous independent testing.

### 10.2 Incident Notification
**Question**: How are customers notified?

- **L1** — Best effort: Customers notified when possible, no SLA.
  - Why: Insufficient for compliance. Creates customer distrust.
- **L2** — 72-hour SLA: Customers notified within 72 hours of confirmed incident.
  - Why: Meets GDPR breach notification requirement.
- **L3** — 24-hour SLA + status page: 24-hour notification plus public status page for incidents.
  - Why: Rapid transparency. Builds trust.
- **L4** — Real-time, dedicated channel: Real-time notification via dedicated channel (Slack, email) with named contact.
  - Why: Enterprise expectation. Enables customer incident response.

### 10.3 Patch Response
**Question**: How fast are vulns fixed?

- **L1** — No defined SLA: Vulnerabilities fixed without defined timeline.
  - Why: Unpredictable. May leave customers exposed.
- **L2** — Critical: 30 days: Critical vulnerabilities fixed within 30 days.
  - Why: Basic commitment. May be too slow for critical issues.
- **L3** — Critical: 7 days: Critical vulnerabilities fixed within 7 days.
  - Why: Reasonable timeline for critical issues.
- **L4** — Critical: 24 hours: Critical vulnerabilities fixed within 24 hours.
  - Why: Best-in-class response. Shows security commitment.

### 10.4 Post-Incident
**Question**: What's shared after?

- **L1** — Internal review only: Post-incident review done internally only.
  - Why: Misses opportunity to build trust.
- **L2** — Summary on request: Structured incident report shared with affected customers within 72 hours. Includes timeline, scope, and remediation steps.
  - Why: Reactive transparency. Better than nothing.
- **L3** — RCA to affected customers: Detailed root cause analysis proactively shared with all affected customers. Includes technical details, timeline, customer impact assessment, and specific preventive measures.
  - Why: Demonstrates transparency and technical rigor. Enables customer security teams to assess their own exposure and take informed action.
- **L4** — Public post-mortem: Detailed public post-mortem for significant incidents.
  - Why: Industry leadership. Builds trust. Helps community.

## Domain 11: AI/ML Controls (Optional)
Focus: Security, safety, and governance for AI-powered features

> **Note**: This domain is optional. When toggled off, it is excluded from tier calculations, progress metrics, and the assessment UI. Enable it only if the product includes AI/ML-powered features.

### 11.1 Training Data Governance
**Question**: Can customers control how their data is used by AI?

- **L1** — Usage documented: Clear documentation of whether and how customer data is used for training, inference, and improvement. Data retention periods and processing locations stated.
  - Why: AIUC-1 A001/A002 mandatory. Enterprise buyers won't evaluate without data usage transparency. Baseline trust requirement.
- **L2** — Opt-out with separation: Customers can opt out of data usage for training. Customer data separated from training pipelines. Cross-customer data exposure prevented.
  - Why: AIUC-1 A005 mandatory. GDPR Art. 21 right to object. Mid-market and regulated industry expectation.
- **L3** — Explicit opt-in, isolated: Customer data only used with explicit opt-in consent with timestamp. Isolated training pipelines per customer. Data lineage tracked from source to model.
  - Why: GDPR Art. 6/7 lawful basis. Enterprise procurement requirement for F500 and regulated industries.
- **L4** — Customer-controlled, auditable: Customers control exactly what data feeds AI systems. Isolated fine-tuning with customer-specific models. Full data lineage audit trail. Customer can independently verify data handling.
  - Why: Regulated industry requirement. Full data sovereignty for AI workloads. Government and financial sector mandate.

### 11.2 Model Configuration
**Question**: What model control exists?

- **L1** — Shared, no config: Single shared model, no customer configuration.
  - Why: May not meet specific customer needs.
- **L2** — Model selection options: Customers can choose between different model options.
  - Why: Flexibility for different use cases.
- **L3** — Fine-tuning available: Customers can fine-tune models on their data.
  - Why: Custom performance. Domain-specific optimization.
- **L4** — Customer-managed deployment: Customers can deploy models in their own infrastructure.
  - Why: Full control. Data sovereignty for AI.

### 11.3 AI Observability
**Question**: Can customers see and audit AI activity?

- **L1** — Basic interaction logs: AI interactions logged with prompt (or hash), response summary, timestamp, model version, and errors. Viewable in application interface.
  - Why: AIUC-1 E015 mandatory. Without logging, incidents are undetectable and unauditable. Table stakes for any AI product.
- **L2** — Structured, queryable: Structured JSON logs including latency, token usage, error rates, model version. Searchable and filterable by time, user, session. Model and prompt configuration changes logged with who/when/what.
  - Why: Enables integration with customer monitoring and compliance systems. SOC 2 CC7.2 requires queryable audit logs.
- **L3** — Exportable, SIEM-ready: Programmatic log export via API. Full model and configuration change audit trail. Structured format compatible with SIEM platforms. Minimum 90-day immutable retention.
  - Why: Enterprise security teams require SIEM integration. Change audit trail enables SOC 2 and ISO 27001 evidence collection.
- **L4** — Customer-controlled observability: Customers control what AI activity is logged, where logs are stored, and retention periods. Real-time streaming support. Customer-managed encryption keys for log storage.
  - Why: Full audit sovereignty. Required for regulated industries with strict data residency and retention requirements.

### 11.4 Output Safety
**Question**: What output controls protect users?

- **L1** — Basic safety filters: PII detection in outputs. Harmful content blocking (toxicity, violence, self-harm). Vendor-provided safety filters for common risk categories.
  - Why: AIUC-1 C003/C006 mandatory. Unfiltered outputs create liability, brand risk, and potential user harm. Minimum viable safety.
- **L2** — Multi-category protection: Hallucination mitigation with grounding and citations. Out-of-scope response prevention. Output vulnerability scanning (injection, XSS). Defined risk taxonomy covering harmful, off-topic, and fabricated outputs.
  - Why: AIUC-1 C001/C004/D001 mandatory. Hallucinations and scope violations are the top enterprise concerns after data privacy.
- **L3** — Customer-configurable policies: Customers configure safety rules per risk category. Custom risk thresholds. High-risk output flagging for human review. Published guardrail documentation with test results.
  - Why: AIUC-1 C005 mandatory. Different industries have different safety thresholds. Healthcare vs. entertainment require fundamentally different filtering.
- **L4** — Customer policy engine: Customers implement their own policy engine for outputs. Real-time monitoring across all risk categories. Automated escalation. Customer-controlled false positive tuning.
  - Why: Full safety control. Enterprise requirement for regulated industries where generic filters are insufficient.

### 11.5 Human Oversight
**Question**: Can humans review AI decisions?

- **L1** — Fully automated: AI operates without human review capability.
  - Why: Risk for high-stakes decisions.
- **L2** — Human review option: Option to have humans review AI outputs.
  - Why: Enables oversight for sensitive decisions.
- **L3** — Configurable boundaries: Customers can set rules for when human review is required.
  - Why: Risk-based automation. Efficiency with safety.
- **L4** — AI vs human audit trail: Clear audit trail of AI vs human decisions with decision attribution.
  - Why: AIUC-1 E004 mandatory. Accountability and regulatory requirement. EU AI Act and GDPR Art. 22 require knowing who or what made a decision.

### 11.6 Input Security
**Question**: How are AI inputs validated and protected?

- **L1** — Basic input validation: Input length limits, format validation, and known malicious pattern blocking applied before model processing. No raw user input reaches the model unvalidated.
  - Why: Prompt injection is OWASP LLM Top 10 #1. Without input validation, every other AI control can be bypassed. Non-negotiable baseline.
- **L2** — Injection detection, PII stripping: Automated prompt injection detection with real-time filtering. PII detected and masked/stripped from inputs before model processing. System prompts isolated from user input and protected from extraction.
  - Why: AIUC-1 B001/B002 mandatory. PII in prompts creates data leakage risk. System prompt isolation prevents instruction extraction attacks.
- **L3** — Adversarial testing program: Regular adversarial testing against OWASP LLM Top 10 patterns. Customer-configurable input policies. Jailbreak and prompt extraction testing with documented results and remediation.
  - Why: AIUC-1 B001 requires third-party adversarial testing. Demonstrates security maturity to enterprise buyers during procurement.
- **L4** — Adaptive defense: Threat intelligence-fed injection detection. Continuous red-teaming program. Customer-managed input security policies. Real-time adversarial input monitoring with automated response.
  - Why: AI threats evolve rapidly. Static defenses become obsolete. Adaptive defense is the only sustainable approach for high-value targets.

### 11.7 AI Transparency
**Question**: Are users informed about AI capabilities and limitations?

- **L1** — AI disclosure: Clear notice to users they are interacting with AI, visible before first interaction. AI-generated content clearly labeled.
  - Why: AIUC-1 E016 mandatory. EU AI Act Art. 52 requires AI disclosure. Users must know when they are interacting with AI, not a human.
- **L2** — Capabilities documented: Published documentation covering purpose, capabilities, known limitations, data sources, and known biases. Acceptable use policy defined and communicated.
  - Why: AIUC-1 E010/E017 mandatory. Users and buyers need to understand what the AI can and cannot do to use it safely and evaluate it for procurement.
- **L3** — Risk docs published: Published risk taxonomy with guardrail documentation and test results. Customer-facing security overview for procurement. Hallucination rates, scope boundaries, and failure modes documented.
  - Why: Enterprise procurement requires risk documentation. Proactive transparency reduces post-sale friction and builds trust.
- **L4** — Full system transparency: Interpretability reports for major AI decisions. Transparency reports shared with regulators and customers. Decision explanations available on request. Independent audit support.
  - Why: GDPR Art. 22 right to explanation for automated decisions. Regulated industries require decision auditability for compliance.

### 11.8 AI Data Rights
**Question**: Can users exercise data rights over AI interactions?

- **L1** — Interaction history visible: Users can view their AI interaction history. Consent captured before first AI interaction with timestamp. Clear terms of service covering AI usage.
  - Why: GDPR Art. 15 right of access. Users must know what data the AI has about them. Baseline requirement for any AI processing personal data.
- **L2** — Export and deletion: Users can export AI interaction history in structured format (JSON/CSV). Deletion requests processed within 30 days covering app DB, model context, and logs. Confirmation provided to user.
  - Why: GDPR Art. 17/20 mandatory. AI-specific deletion must include model context and retrieval stores, not just application database records.
- **L3** — Automated decision controls: Users can opt out of automated AI decisions. Data corrections propagate to all downstream systems including AI context. DPIA completed for AI processing activities.
  - Why: GDPR Art. 22 mandatory for automated decision-making with legal or significant effects. Opt-out is a legal requirement, not a feature.
- **L4** — Granular data sovereignty: Per-interaction retention controls. Purpose limitation enforcement for AI processing. Regional data restrictions. ROPA covering all AI processing activities. Customer-controlled data lifecycle.
  - Why: Full GDPR compliance including Art. 30 ROPA. Required for EU market and regulated industries with strict data governance requirements.

### 11.9 Model Supply Chain
**Question**: How are AI providers and models managed?

- **L1** — Providers documented: AI providers, specific models, and versions documented with rationale for selection. Known limitations per provider documentation acknowledged and communicated.
  - Why: AIUC-1 E006 mandatory. Cannot secure what you do not document. Enterprise buyers need to know whose AI they are trusting with their data.
- **L2** — Vendor due diligence: Provider data handling and retention confirmed in writing. DPAs signed with all AI processors. Data processing locations documented. No training on customer data confirmed.
  - Why: AIUC-1 E006/E011 mandatory. DPAs required by GDPR for all data processors. Data residency requirements increasingly common in enterprise procurement.
- **L3** — Change management: Model updates, prompt changes, and data source changes require review and approval before deployment. Version tracking with rollback capability. Impact assessment for model changes.
  - Why: AIUC-1 E004/E007 mandatory. Uncontrolled model changes can silently degrade safety, introduce bias, or break compliance commitments.
- **L4** — Customer model choice: Customers can select AI providers or models. Bring-your-own-model (BYOM) support. AI supply chain attestation with model provenance verification.
  - Why: Enterprise data sovereignty. Some customers cannot use specific providers due to regulatory, contractual, or geopolitical restrictions.

### 11.10 Agent Authorization & Tool Control
**Question**: How are AI agent actions and tool access controlled?

- **L1** — Unrestricted agent actions: AI agents can invoke any available tool or action without explicit authorization boundaries. No distinction between read and write operations. Agent runs with the user's full permissions.
  - Why: A prompt injection or hallucination can trigger destructive actions. Without tool authorization, every agent is one malicious input away from data deletion or exfiltration.
- **L2** — Allowlisted tools with risk tiers: Agents have an explicit tool allowlist. Actions categorized by risk: low-risk (reads) auto-approved, medium-risk (writes) logged, high-risk (delete, push, external API) require human approval. Agent permissions scoped to current task.
  - Why: Agents need explicit tool permissions, not blanket access. Risk-tiered approval balances usability with safety -- not every action needs human review, but destructive ones do.
- **L3** — Delegation trust chains: Multi-agent systems enforce permission inheritance -- sub-agents inherit at most parent permissions, never exceed them. Delegation chains are auditable. Separation of duties: proposing agent cannot be approving agent. Compromised agent cannot poison swarm context.
  - Why: Sub-agents should inherit at most the permissions of their parent, never exceed them. Without delegation controls, agent orchestration becomes a privilege escalation vector.
- **L4** — Customer-controlled agent policies: Customers define agent tool policies per role or use case. Real-time agent action monitoring with anomaly detection. Policy-as-code for agent authorization. Independent audit trail of all agent decisions separate from provider logs.
  - Why: Enterprise customers need to control what AI agents can do in their environment. Generic policies don't work across industries with different risk profiles.

### 11.11 Memory & Context Security
**Question**: How is AI agent memory and context protected?

- **L1** — No memory controls: AI context/memory persists without explicit controls. No tenant isolation in conversation context. System prompts and user content not separated. Data removed from prompts may persist in provider logs or fine-tuning datasets.
  - Why: Information remnance is a key risk -- data you delete from prompts may still persist in provider logs or training pipelines. Without controls, AI memory becomes a cross-tenant data leakage vector.
- **L2** — Isolated context with retention policies: Strict tenant isolation in all AI context and memory. System instructions separated from user/tool content (prompt injection defense). Memory retention policies enforced with time-based purging. Provider data handling confirmed in writing.
  - Why: System instructions must be strictly separated from user content to prevent prompt injection. Tenant isolation in AI context is the multi-tenancy equivalent of database row-level security.
- **L3** — Encrypted memory with integrity checks: AI memory/context encrypted at rest with integrity verification. Memory poisoning defenses prevent attackers from planting false context. User visibility into what the agent remembers about them. Credential and PII detection prevents sensitive data from entering persistent context.
  - Why: Memory poisoning is an emerging attack -- adversaries plant false instructions in agent memory for later activation. Encrypted memory with integrity checks prevents tampering.
- **L4** — Customer-controlled context lifecycle: Customers control what AI remembers, where context is stored, and retention periods. Customer-managed encryption keys for AI memory. Independent audit of context access. Real-time context purge capability.
  - Why: Full data sovereignty for AI memory. Required for regulated industries where even transient data processing has compliance implications.
