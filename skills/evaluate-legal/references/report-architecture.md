# Report Architecture: Legal Evaluation

Self-contained HTML with bundled markdown. Use [report-base.html](report-base.html) as skeleton.

## Tool output → report section mapping

| Tool output key | Report section | Visual pattern |
|---|---|---|
| `summary`, `risk_level` | Overview > Summary | Risk badge + legal verdict |
| `key_findings` | Overview > Key Findings | Numbered list |
| `entity_classification` | Classification | Key-value card (agent type, legal status, jurisdiction) |
| `obligations` | Obligations > Assessment Table | Sortable table with verbatim Vietnamese text |
| `regulatory_ambiguities` | Ambiguities | Amber cards |
| `cross_border` | Cross-Border Considerations | Card per jurisdiction interaction |
| `legal_output` | Legal Analysis | Topic cards with Vietnamese citations |
| `simulation` | Simulation | Scenario pass/fail table + discoveries |
| `recommendations` | Recommendations | Priority-ordered action list |

## Sidebar sections

```
OVERVIEW
  Summary
  Key Findings
CLASSIFICATION
OBLIGATIONS
  Assessment Table
AMBIGUITIES
CROSS-BORDER
LEGAL ANALYSIS
SIMULATION
  Scenario Results
  Discoveries
RECOMMENDATIONS
```

## Collapsible state on load

- Classification: **always visible** — frames everything below it.
- Obligations table: **always visible** — this is the centerpiece.
- Legal Analysis: **always visible** — cited analysis is why users run this skill.
- Simulation: **always visible**.
- Ambiguities and Cross-Border: **collapsed by default** if empty; visible if populated.

## Visual patterns

**Classification card** — frames the legal analysis. Shows: entity type, applicable legal status (e.g., "Virtual Asset Service Provider — pilot under NQ 05/2025"), jurisdiction (Vietnam primary; additional if cross-border), and any classification caveats or regulator positions. Ambiguities in classification highlighted with amber badge.

**Obligations table** — the centerpiece. Sortable columns: requirement (English paraphrase), legal basis (Vietnamese citation), verbatim text (blockquoted Vietnamese from `nguyên văn`), status (met/unmet/ambiguous/n_a with color badge), gap risk (critical/high/medium/low). Rows sortable by status, gap risk, and legal basis. Status badges: met (green), unmet (red), ambiguous (amber), n_a (gray). Every obligation MUST include a Vietnamese legal citation with article number (e.g., "NQ 05/2025, Điều 7"). Verbatim text column shows Vietnamese original in a blockquote — never paraphrase. This is the defensibility claim.

**Legal Analysis section** — organized by legal topic (e.g., licensing, capital requirements, KYC/AML, cross-border transfer, consumer protection). Each topic card contains: applicable laws with full Vietnamese references, verbatim Vietnamese legal text in blockquotes, obligations and restrictions, risk level badge, and identified gaps or ambiguities. English reports: state finding in English, cite Vietnamese text in parentheses or blockquote. Vietnamese reports: cite Vietnamese text inline. Always include legal reference (e.g., "NQ 05/2025, Điều 7", "BLDS 2015, Điều 584"). This section is the evidence base — users check cited text against their own copies of the laws.

**Ambiguities section** — explicit treatment of where the law does not clearly apply. Each card: the ambiguous area, why it is ambiguous (no statute / conflicting interpretations / absent jurisprudence), what the agent concluded and on what basis (e.g., applied BLDS agency principles to software agents lacking explicit regulation). Important: this is where the skill's value becomes visible — what is NOT regulated explicitly but bridged laterally.

**Cross-Border section** — when the activity involves jurisdictions beyond Vietnam, show per-jurisdiction interaction: what each jurisdiction requires, where they conflict, which controls take precedence, and unresolved gaps. Flag data-localization, licensing reciprocity, and AML information-sharing requirements specifically.

**Simulation section** — pass/fail table with scenario name, outcome badge (green pass, red fail, yellow partial), and finding summary. Discoveries listed below as warning cards with amber accent. Failed scenarios should link to the related obligation in the obligations table.

**Recommendations section** — priority-ordered: legal actions required before the activity can proceed (e.g., obtain license, file notification, update terms), actions required during operation (e.g., periodic reporting, record retention), and risks to escalate to counsel. Each recommendation cites the obligation or ambiguity that motivates it.

<examples>

Obligations table row:
  VASP registration | NQ 05/2025 Điều 7 | "Tổ chức cung cấp dịch vụ tài sản mã hoá phải được cấp phép..." | [UNMET] | HIGH

  Software agent representation | BLDS 2015 Điều 138, 141 | "Người đại diện theo uỷ quyền có thể là cá nhân, pháp nhân..." | [AMBIGUOUS] | MEDIUM

Ambiguity card:
  AREA — Software agents as independent legal actors
  WHY AMBIGUOUS — No specific statute recognizes software as legal persons; BLDS 2015 representation provisions apply by analogy (Điều 134, 138, 141, 143).
  CONCLUSION — Treat the software agent as an instrument of the delegating principal (user or VASP); responsibility and liability flow to the principal. Record the delegation scope in a written authorization consistent with BLDS 2015 Điều 141.

Legal Analysis topic card:
  TOPIC — AML / Transaction Reporting
  APPLICABLE LAW — NQ 05/2025; Luật Phòng, chống rửa tiền 2022.
  VERBATIM — "Giao dịch có giá trị lớn từ 400.000.000 đồng trở lên phải được báo cáo..." (paraphrased — use exact text from `legal_output`)
  OBLIGATIONS — Report transactions ≥ VND 400M within 1 business day; maintain 5-year records; KYC before first transaction.
  RISK — HIGH if enforcement gaps exist at the API layer (reports raised after settlement complete).
  GAPS — none identified / [list gaps]

</examples>
