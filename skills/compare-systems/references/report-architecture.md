# Report Architecture: Systems Comparison

Self-contained HTML with bundled markdown. Use [report-base.html](report-base.html) as skeleton.

## Tool output → report section mapping

| Tool output key | Report section | Visual pattern |
|---|---|---|
| `summary`, `risk_level` | Overview > Summary | Comparison verdict |
| `key_findings` | Overview > Key Findings | Numbered list |
| `gap_assessment` | Analysis > Gap Assessment | Severity cards |
| `compound_vulnerabilities` | Analysis > Compound Exposure | Per-system exposure cards |
| `systems` | Comparison > Side-by-Side | System profile cards + comparison table |
| `key_differences` | Comparison > Key Differences | Difference cards |
| `assessment` | Assessment | What both systems need |
| `remediations` | Remediations | Priority timeline |

## Sidebar sections

```
OVERVIEW
  Summary
  Key Findings
COMPARISON
  Side-by-Side
  Key Differences
ANALYSIS
  Gap Assessment
  Compound Exposure
SIMULATION
  Scenario Results
  Discoveries
ASSESSMENT
REMEDIATIONS
```

## Collapsible state on load

- Side-by-side comparison: **always visible** (centerpiece).
- Key differences: **always visible**.
- Compound exposure: **always visible**.
- Per-system detail: **collapsed** (expand for deep dive).

## Visual patterns

**Side-by-side comparison table** — the centerpiece. Categories as rows, systems as columns. Each cell shows: status (working/known gap/blind spot with color badge), one-line evidence. Rows grouped by domain (operational, regulatory, security). Maturity profile summary at top: working count, known gap count, blind spot count per system.

**System profile card** — expandable. Header: system name, maturity level (high/medium/low badge), maturity profile bar chart (working green, known gaps amber, blind spots red). Body: strengths, weaknesses, system-specific gaps.

**Key difference cards** — each difference grounded in governance coverage, not opinion. Shows: what System A does better, what System B does better, with evidence.

**Simulation section** — pass/fail table with scenario name, outcome badge (green pass, red fail, yellow partial), and finding. Discoveries listed below as warning cards with amber accent. Failed scenarios should link to the relevant remediation.

**Compound exposure cards** — per-system. Shows which cross-domain failures each system is vulnerable to. Two systems can have the same score but radically different exposure — this section makes that visible.

<examples>

Side-by-side row:
  Authority Scoping | System A: [WORKING] Per-tool granular scoping | System B: [BLIND SPOT] Binary all-or-nothing access

System profile header:
  OpenClaw v6 [MEDIUM]
  Working: 5  |  Known Gaps: 6  |  Blind Spots: 5

Key difference card:
  System A: Strong identity model (cryptographic agent identity, per-session tokens)
  System B: Shared API keys, no per-agent identity
  Governance impact: System B cannot attribute actions to specific agents — accountability is impossible.

</examples>
