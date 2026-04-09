# Report Architecture: Protocol Design

Self-contained HTML with bundled markdown. Use [report-base.html](report-base.html) as skeleton.

## Tool output → report section mapping

| Tool output key | Report section | Visual pattern |
|---|---|---|
| `summary`, `risk_level` | Overview > Summary | Risk badge + one-sentence recommendation |
| `key_findings` | Overview > Key Findings | Numbered list |
| `gap_assessment` | Analysis > Requirements Mapping | Severity cards |
| `compound_vulnerabilities` | Analysis > Cross-Domain Consequences | Interaction cards |
| `options` | Protocols > Candidate A/B/C | Protocol cards + comparison table |
| `recommendation` | Recommendation | Highlighted callout |
| `legal_output` | Legal Analysis | Legal finding cards with Vietnamese citations |
| `remediations` | Remediations | Priority timeline |

## Sidebar sections

```
OVERVIEW
  Summary
  Key Findings
ANALYSIS
  Requirements Mapping
  Cross-Domain Consequences
PROTOCOLS
  Comparison Table
  Candidate A
  Candidate B
  ...
RECOMMENDATION
LEGAL ANALYSIS
SIMULATION
  Scenario Results
  Discoveries
REMEDIATIONS
```

## Collapsible state on load

- Comparison table: **always visible**.
- Recommended protocol: **open**. Others: **collapsed**.
- Cross-domain consequences: **always visible**.

## Visual patterns

**Protocol comparison table** — candidates as columns, properties as rows (consensus, finality, throughput, audit capability, response window, fee model). Cells color-coded: green (favorable for this use case), amber (trade-off), red (creates exposure). This is the centerpiece of the report.

**Protocol card** — expandable. Header: protocol name + cross-domain profile characterization. Body:
- Properties with actual data (not "fast" — "2.5s finality")
- Cross-domain consequences: for each property, what it implies for compliance and security
- Risks specific to this protocol for this use case

**Legal Analysis section** — organized by legal topic (e.g., licensing, capital requirements, KYC/AML). Each topic card contains: applicable laws (Vietnamese references), verbatim Vietnamese legal text in blockquotes, obligations and restrictions, risk level badge, and identified gaps. English reports: state finding in English, cite Vietnamese text in parentheses. Vietnamese reports: cite Vietnamese text inline. Always include legal reference (e.g., "NQ 05/2025, Điều 7"). Only rendered when `<legal_evaluation>` data is provided.

**Simulation section** — pass/fail table with scenario name, outcome badge (green pass, red fail, yellow partial), and finding. Discoveries listed below as warning cards with amber accent. Failed scenarios should link to the relevant remediation.

**Consequence trace** — for key protocol properties, a visual chain showing operational property → regulatory implication → security implication. Styled as horizontal flow steps with domain-colored badges.

<examples>

Comparison table row:
  Finality | Ethereum: ~13min (amber: slow for response) | Solana: ~0.4s (red: outpaces compliance checks) | Tron: ~3s (green: balanced)

Protocol card:
  ▸ Ethereum — High audit coverage, slow response window
    Consensus: PoS, ~900k validators
    Finality: ~13 minutes (creates compliance window but limits emergency response)
    Throughput: ~15 TPS (constrains high-frequency operations)
    Audit: Full on-chain visibility (satisfies KYC/AML evidence requirements)
    Consequence: Slow finality gives compliance checks time to run, but means emergency response can't freeze in-flight transactions.

</examples>
