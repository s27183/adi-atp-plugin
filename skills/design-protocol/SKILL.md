---
description: Design or evaluate a protocol configuration for a specific use case. Goes beyond operational matching to show regulatory and security consequences of protocol choices.
context: fork
---

<context>
This skill selects protocols where every property has cross-domain consequences: fast finality reduces response windows, high throughput complicates accountability, small validator sets may satisfy regulatory preference but create concentration risk.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.
</context>

<instructions>
The user wants to design or evaluate a protocol for: "$ARGUMENTS"

Ask who will read this report and what they need to act on.

STOP here and wait for the user's response.

<examples>
Strong audience definitions:

```json
{"role": "infrastructure_engineering", "needs": "Consensus properties, node requirements, RPC endpoints, throughput benchmarks, fee model implications"}
{"role": "compliance_officer", "needs": "Audit coverage per protocol, what's visible on-chain vs requires off-chain correlation, KYC/AML implications of each protocol's transparency model"}
{"role": "cto", "needs": "Cross-domain consequence summary per candidate, which protocol creates the least damaging failure modes for this use case"}
```
</examples>

Gather requirements conversationally — asset types, transaction patterns, throughput needs, finality requirements, audit coverage, regulatory jurisdiction, authority model.

Gather evidence in stages. Always start with the transactional MCP tools — protocol mechanics are the foundation of protocol design:

- `list_protocols` + `compare_protocols` — governance coverage per protocol, compare specific dimensions (identity, asset, transfer, validation, consensus, finality, record)
- `analyze_traceability` — what is and isn't traceable on each candidate protocol
- `get_traceability` — activity-specific traceability (crypto_to_fiat_conversion, cross_border_stablecoin_transfer, custody)

If the use case involves regulatory constraints or security-sensitive operations, also query the relevant canonical sources before falling back to web search:
- Legal: `get_obligations`, `classify_agent` — legal obligations that constrain protocol choice
- Security: `get_security_incidents`, `get_design_pattern` — incidents relevant to each protocol's governance properties

Then gather broader context via web search. The evidence object is open schema:

- `governance_data` — output from the pillar MCP tools above (canonical source)
- `requirements` — operational needs mapped to protocol properties
- `candidate_protocols` — which protocols to evaluate, with version/network specifics
- `regulatory_jurisdiction` — which regulations constrain protocol choice
- `protocol_docs` — chain-specific documentation on consensus, finality, account model, fee model (supplement MCP data)
- `throughput_data` — actual performance data, not marketing claims
- `audit_requirements` — what must be traceable, what retention period, what format

Call `evaluate_design` with:
- `target`: concise — e.g. "Protocol selection for VND settlement rail — Ethereum vs Solana vs Tron"
- `subtype`: "protocol"
- `evidence`: structured dict with the keys above
- `audiences`: audience aspects with specific needs

Present the findings as a structured report. Render the protocol comparison as a properties × candidates matrix with cross-domain impact annotations. Enrich with specifics from evidence — actual confirmation times, real fee data, specific regulatory articles.

Write the report to a markdown file, then render an interactive HTML report. Use [report-base.html](references/report-base.html) as the skeleton. Fill in content sections following [references/report-architecture.md](references/report-architecture.md). Bundle the markdown inside the HTML.
</instructions>
