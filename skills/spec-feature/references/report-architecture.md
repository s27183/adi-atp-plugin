# Report Architecture: Implementation Spec

Self-contained HTML with bundled markdown. No external dependencies except Google Fonts (Inter, JetBrains Mono).

Use [report-base.html](report-base.html) as the skeleton — it provides CSS variables, sidebar scroll-tracking, download mechanism, and collapsible pattern. Fill in the content sections.

## Tool output → report section mapping

Transform `evaluate_spec` output mechanically:

| Tool output key | Report section | Visual pattern |
|---|---|---|
| `design_decision` | Overview > Design Decision | Callout box |
| `key_findings` | Overview > Key Findings | Numbered list |
| `gap_assessment` | Analysis > Gap Assessment | Severity cards |
| `compound_vulnerabilities` | Analysis > Compound Vulnerabilities | Severity cards |
| `api_contracts` | Contracts > API Contracts | Endpoint cards |
| `data_models` | Contracts > Data Models | Entity cards |
| `sequence_flows` | Behavior > Sequence Flows | Numbered steps with governance badges |
| `edge_cases` | Behavior > Edge Cases | Collapsible accordions |
| `governance_invariants` | Governance > Invariants | Numbered list |
| `dependencies` | Governance > Dependencies | Table |
| `legal_output` | Legal Analysis | Legal finding cards with Vietnamese citations |
| `remediations` | Governance > Remediations | P0/P1/P2 timeline |

## Sidebar sections

```
OVERVIEW
  Design Decision
  Key Findings
ANALYSIS
  Gap Assessment
  Compound Vulnerabilities
CONTRACTS
  API Contracts
  Data Models
BEHAVIOR
  Sequence Flows
  Edge Cases
LEGAL ANALYSIS
SIMULATION
  Scenario Results
  Discoveries
GOVERNANCE
  Invariants
  Dependencies
  Remediations
```

## Collapsible state on load

- First API endpoint: **open**. Rest: collapsed.
- All edge cases: **collapsed**.
- Sequence flows: **always visible** (not collapsible).
- Gap assessment and compound vulnerabilities: **always visible**.

## Visual patterns

**API endpoint card** — method badge (POST green, GET blue, DELETE red), path in monospace, expandable body with:
- Request table: field, type, required, description
- Response table: field, type, description
- Governance controls: bullet list with purple accent
- Errors: table with code, condition, governance note

**Data model card** — entity name as header, field table with "Governance Role" column. Fields that exist for governance (audit, attribution, scope enforcement) get a purple governance badge. Database invariants listed below. Status state machine rendered as styled HTML (not Mermaid) — CSS grid with state boxes and arrow connectors.

**Sequence flow** — numbered steps with CSS grid (step number circle + content). Each step shows: actor (dim uppercase label), action, governance check (purple badge inline). The legal initiation event gets a distinct accent (blue circle instead of gray). Control points summarized after the flow.

**Edge case accordion** — collapsible card. Header: scenario name + chevron. Body: trigger, governance impact, required response, blast radius if unhandled (red warning box).

**Legal Analysis section** — organized by legal topic (e.g., licensing, capital requirements, KYC/AML). Each topic card contains: applicable laws (Vietnamese references), verbatim Vietnamese legal text in blockquotes, obligations and restrictions, risk level badge, and identified gaps. English reports: state finding in English, cite Vietnamese text in parentheses. Vietnamese reports: cite Vietnamese text inline. Always include legal reference (e.g., "NQ 05/2025, Điều 7"). Only rendered when `<legal_evaluation>` data is provided.

**Simulation section** — pass/fail table with scenario name, outcome badge (green pass, red fail, yellow partial), and finding. Discoveries listed below as warning cards with amber accent. Failed scenarios should link to the relevant remediation.

**Governance invariants** — numbered cards with purple accent border. System-wide "always" and "never" rules.

**Remediation timeline** — P0 (red), P1 (orange), P2 (blue) badges. Each shows: action, what it addresses.

## Flows: HTML/CSS, not Mermaid

Render sequence flows and state machines as styled HTML/CSS — not Mermaid. HTML/CSS allows governance annotations inline (purple badges per step), which Mermaid can't do as cleanly. The Mermaid diagrams go in the markdown spec file, not the HTML report.

## Bundled markdown

The markdown spec is embedded in a hidden element. Two download buttons in the header:

- "Download Markdown" — extracts from hidden element, triggers download
- "Download JSON" — exports the raw tool output

The mechanism is already wired in report-base.html.
