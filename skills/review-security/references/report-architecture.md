# Report Architecture: Security Review

Self-contained HTML with bundled markdown. Use [report-base.html](report-base.html) as skeleton.

## Tool output → report section mapping

| Tool output key | Report section | Visual pattern |
|---|---|---|
| `summary`, `risk_level` | Overview > Summary | Risk badge + security posture verdict |
| `key_findings` | Overview > Key Findings | Numbered list — false confidence findings highlighted |
| `gap_assessment` | Controls > Security Control Coverage | Control cards |
| `compound_vulnerabilities` | Analysis > Compound Vulnerabilities | Cross-domain cards |
| `obligations` | Analysis > Security Requirements | Requirements from regulatory/operational sources |
| `regulatory_gaps` | Gaps > Regulatory Undermining | Where regulatory ambiguity undermines security |
| `enforcement_gaps` | Gaps > Enforcement Gaps | Where security controls are insufficient |
| `evidence_gaps` | Gaps > Forensic Gaps | Where the system cannot support forensics |
| `remediations` | Remediations | P0/P1/P2 timeline |

## Sidebar sections

```
OVERVIEW
  Summary
  Key Findings
CONTROLS
  Security Control Coverage
  False Confidence
ANALYSIS
  Compound Vulnerabilities
  Security Requirements
GAPS
  Regulatory Undermining
  Enforcement Gaps
  Forensic Gaps
REMEDIATIONS
```

## Collapsible state on load

- Security control coverage: **always visible**.
- False confidence section: **always visible** (this is the most important section).
- Compound vulnerabilities: **always visible**.
- All gap sections: **always visible**.

## Visual patterns

**Control assessment card** — shows: control name, status (working/partial/gap/blind spot), evidence, cross-domain dependency chain. For each control, shows what it depends on in other domains. Broken dependencies highlighted with red connector.

**False confidence card** — visually distinct from regular controls. Striped or hatched border pattern. Shows: the control that appears adequate, what it depends on, where the dependency breaks, why this creates false confidence. This is the report's signature finding — give it visual prominence.

**Dependency visualization** — for each control, a horizontal chain: control → depends on → depends on → (break). Styled as flow steps with domain-colored badges. The break point gets a red "X" marker.

**Compound vulnerability cards** — security gaps that compound with other domain gaps. Shows: which controls interact, why the combination is worse than the sum of parts, blast radius.

<examples>

Control card:
  Identity Verification [PARTIAL]
  Evidence: JWT tokens with 24h expiry, no per-operation scoping
  Depends on: Entity classification (regulatory domain) → NOT DEFINED
  Impact: Access rules enforce on legally undefined entities — who is accountable when rules fail?

False confidence card:
  ⚠ FALSE CONFIDENCE: Access Control
  Control: Role-based access rules appear comprehensive
  Depends on: Entity classification → UNDEFINED
  Depends on: Audit trail → PRESENT but unlinked to entities
  Why false: Strong access rules + no entity classification = rules enforce on legally undefined entities.
  Who is accountable when rules fail? No answer exists in the current architecture.

Dependency chain:
  [Access Control] → [Entity Classification] ✗ → [Accountability] ✗
       working          not defined              impossible

</examples>
