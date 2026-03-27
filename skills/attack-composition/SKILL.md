---
description: Cross-domain composition attack on an agent system. Finds vulnerabilities where regulatory, operational, and security concerns meet — where single-domain analysis misses compound failures.
context: fork
---

<context>
This skill finds vulnerabilities at the seams between governance domains — where single-domain analysis misses compound failures because each domain looks adequate in isolation.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.
</context>

<instructions>
The user names a system, deployment, or scenario to attack: "$ARGUMENTS"

Ask who will read this report and what they need to act on.

STOP here and wait for the user's response. The audience shapes what evidence to prioritize and how to present findings.

<examples>
Strong audience definitions:

```json
{"role": "security_engineering", "needs": "Remediation specs with implementation priority, attack path diagrams, control gap inventory with blast radius per gap"}
{"role": "executive_board", "needs": "Risk assessment with business impact, liability exposure, precedent incidents with dollar figures"}
{"role": "legal_counsel", "needs": "Regulatory exposure analysis, which gaps create compliance violations, jurisdiction-specific risk"}
```
</examples>

Gather evidence in stages. Always start with the security MCP tools — attack surface analysis requires curated incident and pattern data:

- `get_security_incidents` — security incidents mapped to governance gaps (filter by dimension: identity, delegation, policy, intervention, revocation)
- `get_design_pattern` — design patterns the target should implement

If the target involves regulated entities or protocol-specific mechanics, also query the relevant canonical sources before falling back to web search:
- Legal: `classify_agent`, `get_obligations` — entity classifications and obligations that create compliance-driven attack surfaces
- Transactional: `compare_protocols`, `analyze_traceability` — protocol properties that create or mitigate attack vectors

Then gather target-specific evidence via web search and source code analysis. Do not proceed until you have concrete evidence about the system's architecture, trust model, and authorization mechanism.

The evidence object is open schema — pack it with structured context:

- `governance_data` — output from the pillar MCP tools above (canonical source)
- `source_code` — auth modules, policy enforcement, delegation/permission code, role definitions
- `documentation` — security docs, architecture docs, trust model descriptions
- `github_issues` — search for: security, permission, authorization, delegation, access control, multi-tenant
- `security_advisories` — CVEs, public audits, incident disclosures
- `incident_data` — specific post-mortems relevant to this system's architecture (feed concrete incidents, not just names)
- `comparable_systems` — evidence from similar systems that share architectural patterns with the target

Call `evaluate_attack_surface` with:
- `target`: concise — e.g. "OpenClaw v6 — MCP-based agent framework"
- `evidence`: structured dict with the keys above
- `audiences`: audience aspects with specific needs

Present the findings as a structured report. For the markdown: transform `known_attacks` and `novel_attacks` into Mermaid `sequenceDiagram` blocks showing attacker actions, exploited gaps, and blast radius. For the HTML report: render attack paths as styled HTML/CSS with governance gap annotations. Enrich with context from evidence gathering — link findings to specific source code, documentation, or GitHub issues.

Write the report to a markdown file, then render an interactive HTML report. Use [report-base.html](references/report-base.html) as the skeleton. Fill in content sections following [references/report-architecture.md](references/report-architecture.md). Bundle the markdown inside the HTML.
</instructions>
