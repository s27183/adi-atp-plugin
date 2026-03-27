---
description: Review whether an activity is truly compliant — not just legally covered, but defensible in practice. Can the system prove compliance and enforce it?
context: fork
---

<context>
This skill reviews compliance across domains. A system can be legally compliant and still fail when it can't prove compliance or enforce obligations in practice. True compliance = obligations defined AND provable AND enforceable.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.
</context>

<instructions>
Review compliance for: "$ARGUMENTS"

Ask who will read this report and what they need to act on.

STOP here and wait for the user's response. The audience determines how to frame gaps and recommendations.

<examples>
Strong audience definitions:

```json
{"role": "compliance_officer", "needs": "Obligation-by-obligation assessment with legal citations, provability status per obligation, enforcement gap inventory, STR/SAR workflow gaps"}
{"role": "engineering_lead", "needs": "Which obligations require system changes to prove or enforce, audit trail requirements, data retention constraints that affect architecture"}
{"role": "ceo", "needs": "Overall compliance posture, top 3 risks with business impact, what happens if regulators audit today, cost of remediation"}
```
</examples>

Gather evidence in stages. Always start with the legal MCP tools — compliance review requires the authoritative source for regulatory constraints:

- `classify_agent` — classify the system's entities under Vietnamese law (determines which obligations apply)
- `get_obligations` — legal obligations for each agent type and activity
- `check_compliance` — check if specific actions comply with obligations

If the target involves security-sensitive components or protocol-specific operations, also query the relevant canonical sources before falling back to web search:
- Security: `get_security_incidents`, `get_design_pattern` — where security gaps create compliance violations
- Transactional: `analyze_traceability`, `get_traceability` — what the operational layer can and can't prove (evidence gaps)

Then gather broader context via web search. The evidence object is open schema:

- `governance_data` — output from the pillar MCP tools above (canonical source)
- `regulatory_framework` — specific laws, articles, thresholds, with jurisdiction (supplement MCP data with jurisdiction-specific search)
- `system_architecture` — how the system currently works (docs, code, configs)
- `current_controls` — what compliance controls exist today
- `jurisdictions` — which regulatory bodies have oversight
- `enforcement_actions` — recent enforcement against comparable systems in this jurisdiction
- `audit_history` — past audit findings, remediation status
- `contractual_obligations` — terms of service, partnership agreements that create compliance requirements

Call `evaluate_review` with:
- `target`: concise — e.g. "Crypto exchange pilot compliance under NQ 05/2025"
- `subtype`: "compliance"
- `evidence`: structured dict with the keys above
- `audiences`: audience aspects with specific needs

Present the findings as a structured report. For the markdown: include Mermaid `flowchart LR` for gap chains showing obligation → provable? → enforceable? For the HTML: render as styled obligation table and three-gap breakdown. Enrich with specific legal citations and enforcement precedents.

Write the report to a markdown file, then render an interactive HTML report. Use [report-base.html](references/report-base.html) as the skeleton. Fill in content sections following [references/report-architecture.md](references/report-architecture.md). Bundle the markdown inside the HTML.
</instructions>
