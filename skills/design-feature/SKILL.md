---
description: Design a compliant exchange feature with cross-domain trade-off analysis. Produces design options where each option shows what you gain in one domain and what it costs in another.
context: fork
---

<context>
This skill designs features where every decision in one domain has consequences in the others — making cross-domain trade-offs visible so the user chooses with full information.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.
</context>

<instructions>
The user wants to design: "$ARGUMENTS"

Ask who will read this report and what they need to act on.

STOP here and wait for the user's response. The audience shapes which trade-offs to emphasize.

<examples>
Strong audience definitions:

```json
{"role": "cto", "needs": "Architecture trade-offs per option, which option minimizes re-platforming risk, infrastructure cost implications"}
{"role": "product_manager", "needs": "Timeline impact per option, user experience trade-offs, what ships fastest vs what's most defensible"}
{"role": "compliance_officer", "needs": "Regulatory gap analysis per option, which option is most defensible to regulators, citation-backed risk assessment"}
```
</examples>

Gather design constraints conversationally — priority (speed/safety/UX), transaction volume, hard constraints (chains, stack, budget), risk tolerance for regulatory ambiguity, AI agents or human-only. Skip what's obvious.

Gather evidence in stages. Always start with the legal MCP tools — regulatory constraints shape every design option:

- `classify_agent` + `get_obligations` — entity classifications and legal obligations for the feature's actors
- `check_compliance` — check if the proposed activity complies with obligations

If the feature involves security-sensitive or protocol-specific components, also query the relevant canonical sources before falling back to web search:
- Security: `get_security_incidents`, `get_design_pattern` — incidents and patterns relevant to this feature type
- Transactional: `compare_protocols`, `analyze_traceability`, `get_design_pattern` — protocol properties and traceability for candidate chains

Then gather broader context via web search. The evidence object is open schema — richer context produces better trade-off analysis:

- `governance_data` — output from the pillar MCP tools above (canonical source)
- `requirements` — what the feature must do, user flows, business constraints
- `constraints` — non-negotiable limits (regulatory, technical, budgetary)
- `regulatory_context` — specific laws, articles, thresholds, jurisdictional requirements (supplement MCP data with jurisdiction-specific web search)
- `protocol_data` — chain properties relevant to the feature (finality, throughput, fees)
- `incident_data` — incidents at comparable systems that inform design choices
- `comparable_implementations` — how similar features work at other exchanges, with trade-off evidence
- `market_context` — competitive landscape, user expectations, regional specifics

Call `evaluate_design` with:
- `target`: concise — e.g. "Crypto-to-VND conversion for Vietnamese exchange pilot under NQ 05/2025"
- `subtype`: "feature"
- `evidence`: structured dict with the keys above
- `audiences`: audience aspects with specific needs

Present the findings as a structured report. For the markdown: include a Mermaid `flowchart LR` summarizing constraints → trade-off → chosen option → residual risk. For the HTML: render options as styled cards with trade-off comparison. Enrich with specifics from evidence — cite regulations, protocol properties, incident precedents.

Write the report to a markdown file, then render an interactive HTML report. Use [report-base.html](references/report-base.html) as the skeleton. Fill in content sections following [references/report-architecture.md](references/report-architecture.md). Bundle the markdown inside the HTML.
</instructions>
