# Report Format: Shared Rendering Rules

Shared across all ATP plugin skills. Complements the per-skill `report-architecture.md` (which defines the section map for that skill type). This file defines *how* to render content — what CSS classes to use, how to render findings, legal citations, simulation outcomes, and diagrams.

The CSS classes referenced below all exist in `report-base.html`. Do not invent new classes — the skeleton's stylesheet won't have them.

## Page header (open every report with this)

```html
<div class="page-header">
  <h1>{report title}</h1>
  <div class="meta">
    <div class="meta-item"><strong>Risk:</strong> <span class="badge badge-high">HIGH</span></div>
    <div class="meta-item"><strong>Date:</strong> {YYYY-MM-DD}</div>
    <div class="meta-item"><strong>Target:</strong> {what the user asked about}</div>
    <div class="meta-item"><strong>Scope:</strong> {relevant scope note}</div>
  </div>
</div>
```

Pick the risk badge from `badge-critical`, `badge-high`, `badge-medium`, `badge-low` based on the overall assessment outcome.

## Sections

Wrap each top-level section in a `<section>` element with an `id` so the sidebar nav anchors to it. The skeleton's auto-styling renders `section > h2` as the section header.

```html
<section id="legal-analysis">
  <h2>Legal Analysis</h2>
  ...
</section>
```

## Findings — severity-coded cards

Every discrete finding (risk, gap, constraint, vulnerability) gets its own card. The header shows a severity badge; the body holds description, evidence, impact.

```html
<div class="card">
  <div class="card-header">
    <span class="chevron">▸</span>
    <span class="badge badge-critical">CRITICAL</span>
    <h3>Finding title</h3>
  </div>
  <div class="card-body">
    <p>{one-paragraph description}</p>
    <p><strong>Evidence:</strong> {what data or citation supports this}</p>
    <p><strong>Impact:</strong> {what happens if unaddressed}</p>
  </div>
</div>
```

Severities: `badge-critical` (red), `badge-high` (amber), `badge-medium` (orange), `badge-low` (green).

For sections where findings should be visible on load (comparison tables, scenario results), add `always-open` to the card: `<div class="card always-open">`.

## Callouts — executive ledes and recommendations

For the main takeaway of a section, or standalone recommendations that aren't scoped to one finding:

```html
<div class="callout">
  <h3>Recommendation</h3>
  <p>{one or two sentence takeaway with the critical insight}</p>
</div>
```

## Inline warnings — compact risk notes

For tight inline warnings inside a card body or at the end of a subsection:

```html
<div class="warning">
  Blast radius: total corridor shutdown. Vietnam operations halt until the anchor is re-established.
</div>
```

## Severity badges — inline pills

Use inside headings, table cells, list items, flow step actions:

```html
<span class="badge badge-critical">CRITICAL</span>
<span class="badge badge-high">HIGH</span>
<span class="badge badge-medium">MEDIUM</span>
<span class="badge badge-low">LOW</span>
```

Priority pills (for remediations): `badge-p0` (red, immediate), `badge-p1` (amber, short-term), `badge-p2` (blue, medium-term).

Governance/method badges: `badge-governance`, `badge-method-post`, `badge-method-get`, `badge-method-delete`.

## Invariants — numbered must-hold properties

For protocol invariants. Number them manually — `INV-01`, `INV-02`:

```html
<div class="invariant">
  <span class="invariant-num">INV-01</span>
  <div>No transaction may complete without matching sanctions screening status at the time of broadcast.</div>
</div>
```

## Flow steps — actor + governance sequence

For multi-actor sequence flows (registration, settlement, remediation). Number manually:

```html
<div class="flow-step">
  <div class="flow-step-num">1</div>
  <div class="flow-step-content">
    <div class="flow-step-actor">Korean VASP</div>
    <div class="flow-step-action">Sender initiates remittance via the VASP app, calls <code>POST /v1/remittances</code> with KYC metadata.</div>
    <div class="flow-step-gov">Governance: Travel Rule payload attached</div>
  </div>
</div>
```

The `flow-step-gov` block is optional — include it when the step carries a specific governance obligation, control, or regulatory touchpoint.

## State machines — horizontal state pills

For lifecycle state summaries (transaction states, KYC states):

```html
<div class="state-machine">
  <span class="state">PENDING</span>
  <span class="state-arrow">→</span>
  <span class="state">COMPLIANCE_PASSED</span>
  <span class="state-arrow">→</span>
  <span class="state">SETTLING</span>
  <span class="state-arrow">→</span>
  <span class="state">COMPLETED</span>
</div>
```

For full state transition diagrams with labelled edges, use a mermaid `stateDiagram-v2` block instead.

## Mermaid diagrams — mandatory for flows and lifecycles

Every report with sequence flows or state lifecycles MUST include at least one mermaid diagram. Mermaid JS is bundled in `report-base.html`. Emit as a fenced code block with class `mermaid`:

```html
<pre class="mermaid">
sequenceDiagram
  participant U as User
  participant V as VASP
  participant B as Bank
  U->>V: Initiate remittance
  V->>V: Travel Rule check
  V->>B: Settlement instruction (VND)
  B-->>V: Confirmation
  V-->>U: Receipt
</pre>
```

Diagram type by content:
- **Sequence flows** (registration, settlement, compliance workflow) → `sequenceDiagram`
- **State lifecycles** (transaction status, KYC status, licensing stages) → `stateDiagram-v2`
- **Architecture overviews** (system components, data flow) → `flowchart TD` or `flowchart LR`

Never render these as numbered prose steps or ASCII art when a mermaid diagram would show the same information.

## Legal Analysis section (when `evaluate_legal` ran)

If the pipeline included `evaluate_legal`, add a dedicated `<section id="legal-analysis">` with:

- Verbatim Vietnamese legal text from `legal_findings[].verbatim_text` — cite as inline `<blockquote>`.
- Applicable law reference from `legal_findings[].applicable_laws` — e.g., "NQ 05/2025, Điều 7" or "BLDS 2015, Điều 141".
- English interpretation alongside each citation. For Vietnamese reports, keep citations in Vietnamese inline and omit the English translation.
- Finding card per legal topic (licensing, capital, KYC/AML, settlement currency) with an appropriate severity badge.

```html
<section id="legal-analysis">
  <h2>Legal Analysis</h2>
  <div class="card always-open">
    <div class="card-header">
      <span class="badge badge-high">HIGH</span>
      <h3>VND settlement requirement</h3>
    </div>
    <div class="card-body">
      <p>NQ 05/2025 requires all pilot-phase settlement to occur in VND via licensed banks.</p>
      <blockquote>
        <p><em>"Việc thanh toán phải thực hiện bằng đồng Việt Nam qua ngân hàng thương mại được cấp phép."</em></p>
        <p>— <strong>NQ 05/2025, Điều 4 Khoản 7</strong></p>
      </blockquote>
      <p><strong>Impact:</strong> Any protocol path that settles in USDT or other foreign-denominated assets is non-compliant during the pilot.</p>
    </div>
  </div>
</section>
```

## Simulation section (when `simulate` ran)

Render scenario outcomes as a table with severity-coded outcome pills, then list cross-scenario discoveries as individual cards or a callout.

```html
<section id="simulation">
  <h2>Simulation</h2>
  <h3>Scenario Results</h3>
  <table>
    <thead>
      <tr><th>Scenario</th><th>Outcome</th><th>Finding</th></tr>
    </thead>
    <tbody>
      <tr>
        <td>Happy path: standard remittance</td>
        <td><span class="badge badge-low">PASS</span></td>
        <td>All controls fire as designed.</td>
      </tr>
      <tr>
        <td>Boundary: sub-threshold batching</td>
        <td><span class="badge badge-medium">PARTIAL</span></td>
        <td>Batching evades Travel Rule threshold; limit aggregation needs a 24-hour rolling window.</td>
      </tr>
      <tr>
        <td>Regulatory silence: stablecoin custody</td>
        <td><span class="badge badge-medium">GAP</span></td>
        <td>NQ 05/2025 does not specify custody requirements for stablecoins held as collateral.</td>
      </tr>
    </tbody>
  </table>

  <h3>Discoveries</h3>
  <div class="callout">
    <p>Cross-scenario: every path that involved intermediate stablecoin conversion hit at least one boundary or gap. Direct VND-to-VND paths passed cleanly.</p>
  </div>
</section>
```

**Simulation outcome rendering rules:**
- `PASS` → `badge-low` (green).
- `FAIL` → `badge-critical` (red).
- `PARTIAL` → `badge-medium` (orange).
- `regulatory_gap` → `badge-medium` labelled **GAP** (amber, not red). Frame the finding as a regulatory silence — say what the law does NOT specify, not what the design fails to do. Do not count `regulatory_gap` scenarios in pass/fail tallies; list them separately.

## Section ownership — never restate a finding

Each finding belongs in exactly one section. Do not restate the same finding across sections.

- **Analysis > Requirements Mapping / Findings**: single-domain findings (one regulatory rule, one security incident, one protocol property).
- **Analysis > Cross-Domain Consequences**: ONLY findings that *require composing* two or more domains (e.g., a regulatory rule + a chain property that together create a risk neither has alone).
- **Legal Analysis**: authoritative source for all regulatory citations and verbatim Vietnamese legal text. Other sections may reference legal findings briefly but must not reproduce the verbatim text or obligation lists — link the reader via `<a href="#legal-analysis">Legal Analysis</a>` instead.
- **Simulation > Scenario Results**: scenario-by-scenario outcomes.
- **Simulation > Discoveries**: cross-scenario insights only, not restatements of individual scenario findings.

When the same insight appears in multiple inputs (evaluation finding + legal obligation + simulation outcome), pick the section that owns the strongest framing and keep it there only.

## Safety — never include internal references

This report is a user deliverable. Never include:
- Internal tool names (`gather_evidence`, `evaluate_legal`, `simulate`, `generate_report`).
- Agent names or system architecture details.
- Pipeline implementation references (MCP, ATP, sidecar, orchestrator).
- `evidence_id`, `evaluation_id`, `simulation_id` strings.

If the source data contains such references, omit them or translate to domain language.

## Language

Write the report in the same language as the user's query. For Vietnamese reports:
- Keep technical terms in English when no established Vietnamese equivalent exists in regulation or industry practice — crypto protocols, token names, security terminology.
- Use Vietnamese for all surrounding explanation, analysis, and recommendations.
- When a Vietnamese term exists in official regulation (e.g., "tài sản ảo", "sàn giao dịch"), prefer the Vietnamese term with the English original in parentheses on first use.
- Section headings must also be in Vietnamese. Standard mapping:
  - Overview → Tổng quan
  - Analysis → Phân tích
  - Recommendation → Khuyến nghị
  - Legal Analysis → Phân tích pháp lý
  - Simulation → Mô phỏng
  - Remediations → Biện pháp khắc phục
  - Scenario Results → Kết quả kịch bản
  - Discoveries → Phát hiện
  - Key Findings → Những phát hiện chính

## Hard rules

- Every report opens with a `<div class="page-header">` including risk badge + date + target + scope.
- Every discrete finding is wrapped in a `<div class="card">` with a severity badge in the header.
- Never use raw `<span style="color: red">` or inline styles — use the badge/card/callout class vocabulary only.
- Every sequence flow must have a mermaid `sequenceDiagram`. Every lifecycle with labelled transitions must have a mermaid `stateDiagram-v2`.
- Blockquote Vietnamese legal text verbatim inside Legal Analysis; cite the law reference on a separate line with `<strong>`.
- `regulatory_gap` outcomes use amber (`badge-medium`) and are framed as regulatory silence, not design failure.