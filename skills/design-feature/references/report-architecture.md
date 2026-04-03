# Report Architecture: Feature Design

Self-contained HTML with bundled markdown. Use [report-base.html](report-base.html) as skeleton.

## Tool output → report section mapping

| Tool output key | Report section | Visual pattern |
|---|---|---|
| `summary`, `risk_level` | Overview > Summary | Risk badge + one-sentence verdict |
| `key_findings` | Overview > Key Findings | Numbered list |
| `gap_assessment` | Analysis > Gap Assessment | Severity cards by category |
| `compound_vulnerabilities` | Analysis > Compound Vulnerabilities | Cross-domain interaction cards |
| `options` | Options > Option A/B/C/D | Option cards (expandable) |
| `recommendation` | Recommendation | Highlighted callout |
| `remediations` | Remediations | P0/P1/P2 timeline |

## Sidebar sections

```
OVERVIEW
  Summary
  Key Findings
ANALYSIS
  Gap Assessment
  Compound Vulnerabilities
OPTIONS
  Option A
  Option B
  ...
RECOMMENDATION
SIMULATION
  Scenario Results
  Discoveries
REMEDIATIONS
```

## Collapsible state on load

- Gap assessment: **always visible**.
- Compound vulnerabilities: **always visible**.
- Recommended option: **open**. Other options: **collapsed**.
- Recommendation: **always visible**.

## Visual patterns

**Option card** — expandable. Header shows: option name (describes the trade-off, not the technology), trade-off one-liner. Body shows:
- "Optimizes" / "Sacrifices" — what this option gains and loses
- Specifications organized by concern (not by domain)
- Risks — where domain interactions create vulnerabilities
- The recommended option gets a distinct accent (green border, "Recommended" badge)

**Compound vulnerability card** — shows gap interaction chain. Purple accent. Description, gaps involved, severity, blast radius.

**Recommendation callout** — prominent box below options. States which option, why, what residual risk is accepted. Green accent border.

**Simulation section** — pass/fail table with scenario name, outcome badge (green pass, red fail, yellow partial), and finding. Discoveries listed below as warning cards with amber accent. Failed scenarios should link to the relevant remediation.

**Remediation cards** — P0 (red before launch), P1 (orange before scale), P2 (blue regulatory insurance). Each links back to which compound vulnerabilities it addresses.

<examples>

Option card:
  ▸ Option B: Supervised Automation with Human Confirmation Gate
    Optimizes: defensible compliance + AI capability
    Sacrifices: full automation benefit (human confirmation adds friction)
    [RECOMMENDED]

    Specifications:
      Protocol: per-tx cap ~VND 13M, daily aggregate cap, approved pairs only
      Compliance: AI is a tool (like a limit order), user is legal initiator
      Governance: 24h session re-auth, auto-revocation on anomalous patterns

    Risks:
      - "Tool-not-agent" is a legal interpretation, not established law
      - Confirmation fatigue may erode the legal distinction

Recommendation callout:
  Option B, supplemented with Option C's regulatory engagement strategy.
  Residual risk accepted: MOF may reject tool classification.
  Mitigated by designing confirmation gate as architecturally removable.

</examples>
