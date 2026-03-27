---
description: Review security posture across domains. Security gaps compound — a delegation flaw becomes catastrophic when actions can't be attributed or reversed.
context: fork
---

<context>
This skill reviews security across domains. Security controls that appear adequate in isolation may be structurally undermined by gaps in entity classification or operational architecture — creating false confidence. The most dangerous finding is a control everyone trusts that doesn't actually work.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.
</context>

<instructions>
Review security for: "$ARGUMENTS"

Ask who will read this report and what they need to act on.

STOP here and wait for the user's response.

<examples>
Strong audience definitions:

```json
{"role": "ciso", "needs": "Risk matrix with blast radius per control failure, false confidence inventory, remediation priority with dependency ordering"}
{"role": "security_engineering", "needs": "Control-by-control assessment with cross-domain dependency chain, specific implementation gaps, what each control depends on in other domains"}
{"role": "legal_counsel", "needs": "Where security gaps create regulatory exposure, which gaps would be cited in an enforcement action, liability chain when controls fail"}
```
</examples>

Gather evidence in stages. Always start with the security MCP tools — security review requires curated incident data and governance gap mapping:

- `get_security_incidents` — incidents mapped to governance dimensions (identity, delegation, policy, intervention, revocation). Curated with root cause analysis and governance gap mapping.
- `get_design_pattern` — design patterns the target should implement

If the target involves regulated entities or protocol-specific mechanics, also query the relevant canonical sources before falling back to web search:
- Legal: `classify_agent`, `get_obligations` — entity classifications that determine what security model is legally required
- Transactional: `compare_protocols`, `analyze_traceability` — protocol properties that affect security posture (finality, irreversibility, validator trust)

Then gather target-specific evidence via web search and source code analysis. The evidence object is open schema:

- `governance_data` — output from the pillar MCP tools above (canonical source)
- `source_code` — auth modules, policy enforcement, delegation/permission code, key management
- `security_docs` — security architecture, threat models, access control documentation
- `architecture` — system architecture, trust boundaries, data flow
- `public_audits` — third-party security audits, penetration test results
- `incidents` — security incidents at this system or comparable systems, with post-mortems (supplement MCP data)
- `credential_management` — how credentials are issued, rotated, revoked, expired
- `operational_procedures` — incident response plans, circuit breaker procedures, escalation paths

Call `evaluate_review` with:
- `target`: concise — e.g. "OpenClaw v6 — MCP agent framework security posture"
- `subtype`: "security"
- `evidence`: structured dict with the keys above
- `audiences`: audience aspects with specific needs

Present the findings as a structured report. For the markdown: include Mermaid `flowchart TD` for false confidence dependency chains. For the HTML: render as styled control cards with dependency visualization and false confidence marking. Enrich with specific source code references, audit findings, and incident citations.

Write the report to a markdown file, then render an interactive HTML report. Use [report-base.html](references/report-base.html) as the skeleton. Fill in content sections following [references/report-architecture.md](references/report-architecture.md). Bundle the markdown inside the HTML.
</instructions>
