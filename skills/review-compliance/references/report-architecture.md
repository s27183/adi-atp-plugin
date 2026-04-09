# Report Architecture: Compliance Review

Self-contained HTML with bundled markdown. Use [report-base.html](report-base.html) as skeleton.

## Tool output → report section mapping

| Tool output key | Report section | Visual pattern |
|---|---|---|
| `summary`, `risk_level` | Overview > Summary | Risk badge + compliance verdict |
| `key_findings` | Overview > Key Findings | Numbered list |
| `obligations` | Obligations > Assessment Table | Sortable table |
| `gap_assessment` | Analysis > Gap Assessment | Severity cards |
| `compound_vulnerabilities` | Analysis > Compound Vulnerabilities | Cross-domain cards |
| `regulatory_gaps` | Gaps > Regulatory Gaps | Amber section |
| `enforcement_gaps` | Gaps > Enforcement Gaps | Red section |
| `evidence_gaps` | Gaps > Evidence Gaps | Orange section |
| `legal_output` | Legal Analysis | Legal finding cards with Vietnamese citations |
| `remediations` | Remediations | P0/P1/P2 timeline |

## Sidebar sections

```
OVERVIEW
  Summary
  Key Findings
OBLIGATIONS
  Assessment Table
ANALYSIS
  Gap Assessment
  Compound Vulnerabilities
GAPS
  Regulatory Gaps
  Enforcement Gaps
  Evidence Gaps
LEGAL ANALYSIS
SIMULATION
  Scenario Results
  Discoveries
REMEDIATIONS
```

## Collapsible state on load

- Obligations table: **always visible** (this is the centerpiece).
- Gap assessment: **always visible**.
- Three gap sections: **always visible**.
- Compound vulnerabilities: **always visible**.

## Visual patterns

**Obligations table** — the centerpiece. Sortable columns: requirement, source (legal citation), status (met/unmet/ambiguous with color badge), provable (yes/no/partial), enforceable (yes/no/partial). Rows sortable by status and source. Status badges: met (green), unmet (red), ambiguous (amber).

**Three-gap breakdown** — three visually distinct sections:
- Regulatory gaps (amber border) — obligations that are unclear or inapplicable
- Enforcement gaps (red border) — obligations the system cannot enforce in practice
- Evidence gaps (orange border) — obligations the system cannot prove to a third party
Each gap is a card with the obligation it relates to and what's missing.

**Legal Analysis section** — organized by legal topic (e.g., licensing, capital requirements, KYC/AML). Each topic card contains: applicable laws (Vietnamese references), verbatim Vietnamese legal text in blockquotes, obligations and restrictions, risk level badge, and identified gaps. English reports: state finding in English, cite Vietnamese text in parentheses. Vietnamese reports: cite Vietnamese text inline. Always include legal reference (e.g., "NQ 05/2025, Điều 7"). Only rendered when `<legal_evaluation>` data is provided.

**Simulation section** — pass/fail table with scenario name, outcome badge (green pass, red fail, yellow partial), and finding. Discoveries listed below as warning cards with amber accent. Failed scenarios should link to the relevant remediation.

**Compound vulnerability cards** — where compliance exists on paper but cross-domain gaps make it unenforceable or unprovable. Shows the gap chain as a styled flow: obligation → provable? → enforceable? → where it breaks.

<examples>

Obligations table row:
  VND-only trading | NQ 05/2025 Đ11-15 | [MET] | Provable: system rejects non-VND pairs | Enforceable: pair whitelist at API level

  AI agent classification | NQ 05/2025 | [AMBIGUOUS] | Provable: no legal opinion on file | Enforceable: classification determines which obligations apply

Gap card:
  ENFORCEMENT GAP
  Obligation: AML reporting for transactions > VND 26M
  Source: NQ 05/2025
  Gap: AML check runs post-settlement. System cannot block a reportable transaction before it completes.
  Impact: Structurally non-compliant — reporting after the fact doesn't satisfy the obligation.

</examples>
