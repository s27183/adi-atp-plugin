---
description: Generate governance-aware implementation specifications from a design decision. Produces API contracts, data models, sequence flows, and edge cases where governance controls are structural — embedded in the spec, not bolted on after.
context: fork
---

<context>
This skill translates a design decision into implementation specs a technical team can build from. Governance controls are structural — embedded in every API endpoint, data model, and sequence flow as first-class elements, not added as a compliance layer after.

Chains from design-feature. If no design decision exists, run design-feature first. After the spec is produced, render it as an interactive HTML report with bundled markdown.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.
</context>

<instructions>
The user wants implementation specs for: "$ARGUMENTS"

Check the conversation for a prior design-feature analysis or an explicit design decision. If neither exists, run `design-feature` first with the same target. Once a design decision is available, continue.

Ask who will use these specs and what they need to build from.

STOP here and wait for the user's response. The audience shapes which sections get depth — backend engineering needs API contracts, security engineering needs control point inventories with blast radius per failure, compliance needs audit field mapping with retention schemas.

<examples>
Strong audience definitions that produce targeted output:

```json
{"role": "backend_engineering", "needs": "API contracts with request/response schemas, data models with database-level invariants, sequence flows with error handling"}
{"role": "security_engineering", "needs": "Control point inventory with blast radius per control failure, circuit breaker SLA verification criteria, anomaly detection rule schema, delegation boundary enforcement spec"}
{"role": "compliance_legal", "needs": "Audit field mapping per data model, AML reporting trigger logic, attribution chain spec linking human initiator to every state change, 10-year retention schema"}
```
</examples>

Gather spec constraints conversationally — tech stack, protocols in scope, existing architecture, timeline pressure. Skip what the design analysis already covered.

Gather implementation-relevant evidence. The evidence object is open schema — pack it with structured context because every key you provide shapes the output:

- `design_report` — the chosen option, its trade-offs, compound vulnerabilities, remediations
- `regulatory_context` — specific articles, thresholds, retention requirements that become data model constraints
- `protocol_data` — chain-specific API formats, transaction structures, confirmation times
- `tech_constraints` — stack decisions, existing services, deployment requirements
- `incident_data` — specific incidents that inform edge case design (feed concrete post-mortems, not just names)
- `partner_api_specs` — actual API docs from integration partners make flows concrete, not generic
- `existing_legal_opinions` — legal counsel positions ground edge cases in real arguments

For features touching 3+ domains or where the API surface is uncertain, iterate rather than requesting everything in one call. Each pass builds on the previous — producing focused depth instead of broad but shallow output.

Before iterating, ensure `regulatory_context` is in the evidence — specific laws, thresholds, retention requirements. These constraints shape the backend API surface (fields, invariants, state machines). Without them, the backend pass will miss compliance-driven requirements and later passes will find gaps everywhere. Gather regulatory context from the legal MCP tools (`classify_agent`, `get_obligations`, `check_compliance`) — always required, canonical source.

Security (`get_security_incidents`, `get_design_pattern`) and transactional (`list_protocols`, `compare_protocols`, `analyze_traceability`, `get_design_pattern`) MCP tools are also canonical sources. Use them when relevant to what the user is building — if the feature touches those domains, query these tools before falling back to web search. Web search is secondary for all domains.

1. **Backend pass** — call `evaluate_spec` with backend audience only. This produces the API contracts and data models — the foundation everything else builds on.
2. Present the API contracts to the user. Confirm the interface is right before going deeper.
3. **Security pass** — call `evaluate_spec` again with security audience. Include the API contracts from pass 1 as additional evidence under `existing_api_contracts`. Now security controls are grounded in the actual API surface.
4. **Compliance pass** — call `evaluate_spec` with compliance audience. Include both API contracts and security controls as evidence. Audit fields and attribution chains reference real endpoints and real control points.
5. Merge the passes into a single spec document.

For a focused feature with clear scope, one call with all audiences is fine.

When calling `evaluate_spec`:
- `target`: concise, structured — e.g. "Crypto-to-VND spot conversion (BTC, ETH, USDT) — greenfield, governance-first, pre-license"
- `design_decision`: the chosen option and its key trade-off
- `evidence`: structured dict with the keys above (plus output from previous passes when iterating)
- `audiences`: audience aspects with specific needs

Present the findings as a structured spec. For the markdown file: transform the tool's `sequence_flows` output into Mermaid `sequenceDiagram` blocks with governance checks as notes over actors, and status state machines into `stateDiagram-v2` blocks. For the HTML report: render flows and state machines as styled HTML/CSS (not Mermaid) — this allows governance annotations inline as purple badges per step. Enrich both with specifics from evidence — concrete API formats, actual field names, specific article numbers.

Write the spec to a markdown file, then render an interactive HTML report. Use [report-base.html](references/report-base.html) as the skeleton — it provides CSS tokens, sidebar scroll-tracking, download mechanism, and collapsible cards. Fill in the content sections following [references/report-architecture.md](references/report-architecture.md). Bundle the markdown inside the HTML so the user gets both formats via download buttons.
</instructions>
