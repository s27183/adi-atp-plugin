---
description: Compare agent systems on governance maturity — what's working, what's a known gap, and what's a blind spot.
context: fork
---

<context>
This skill compares systems on governance maturity — the gap between capability (what the system can do) and governance (what controls exist over what it does). Two systems with the same maturity score can have radically different exposure profiles.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.
</context>

<instructions>
The user names systems to compare: "$ARGUMENTS"

Ask who will read this report and what they need to act on.

STOP here and wait for the user's response.

<examples>
Strong audience definitions:

```json
{"role": "cto", "needs": "Side-by-side maturity profile, which system is more governable, migration cost if switching"}
{"role": "security_team", "needs": "Control-by-control comparison, which system has blind spots the other covers, compound exposure per system"}
{"role": "product_leadership", "needs": "Which system is more defensible to enterprise customers, governance maturity as competitive differentiator"}
```
</examples>

Gather evidence in stages. The canonical pillar MCP tools provide the governance baseline for comparison — query the ones relevant to what's being compared:

- Security: `get_security_incidents`, `get_design_pattern` — incidents mapped to governance dimensions, design patterns each system should implement. Absent patterns are governance gaps.
- Legal: `classify_agent`, `get_obligations` — entity classifications and obligations that apply to the systems being compared
- Transactional: `list_protocols`, `compare_protocols`, `analyze_traceability` — protocol properties for systems with on-chain components

Use these canonical sources before falling back to web search. Then gather per-system evidence via web search and source code analysis. Prioritize primary sources (source code, official docs) over secondary (blog posts, marketing). Do not evaluate based on marketing claims when source code is available.

The evidence object per system is open schema:

- `governance_baseline` — output from the pillar MCP tools above (canonical evaluation framework)
- `source_code` — auth, policy, delegation, permission, role modules
- `documentation` — security docs, architecture docs, trust model
- `github_issues` — security, authorization, delegation, access control, multi-tenant
- `security_advisories` — CVEs, public audits, incident reports
- `architecture` — system design, trust boundaries, deployment model
- `governance_claims` — what the system claims about its governance (to verify against evidence)

Call `evaluate_comparison` with:
- `target`: concise — e.g. "OpenClaw vs LangChain — governance maturity"
- `systems`: array of `{name, evidence}` — one per system, each with structured evidence dict
- `audiences`: audience aspects with specific needs

Present the findings as a structured report. The centerpiece is the side-by-side comparison — same categories, different assessments per system. Enrich with specific source code references, issue citations, and documentation links.

Write the report to a markdown file, then render an interactive HTML report. Use [report-base.html](references/report-base.html) as the skeleton. Fill in content sections following [references/report-architecture.md](references/report-architecture.md). Bundle the markdown inside the HTML.
</instructions>
