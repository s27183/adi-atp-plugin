# Report Architecture: Attack Surface Analysis

Self-contained HTML with bundled markdown. Use [report-base.html](report-base.html) as skeleton.

## Tool output → report section mapping

| Tool output key | Report section | Visual pattern |
|---|---|---|
| `summary`, `risk_level` | Overview > Summary | Risk badge + one-sentence finding |
| `key_findings` | Overview > Key Findings | Numbered list |
| `gap_assessment` | Analysis > Gap Assessment | Severity cards by category |
| `compound_vulnerabilities` | Analysis > Compound Vulnerabilities | Severity cards showing gap interactions |
| `known_attacks` | Attacks > Known Attacks | Attack cards with precedent citations |
| `novel_attacks` | Attacks > Novel Attacks | Attack cards with confidence badges |
| `missing_controls` | Controls > Missing Controls | Control cards with risk created |
| `legal_output` | Legal Analysis | Legal finding cards with Vietnamese citations |
| `remediations` | Remediation > Prioritized Fixes | P0/P1/P2 timeline |

## Sidebar sections

```
OVERVIEW
  Summary
  Key Findings
ANALYSIS
  Gap Assessment
  Compound Vulnerabilities
ATTACKS
  Known Attacks
  Novel Attacks
CONTROLS
  Missing Controls
LEGAL ANALYSIS
SIMULATION
  Scenario Results
  Discoveries
REMEDIATION
  Prioritized Fixes
```

## Collapsible state on load

- Gap assessment: **always visible**.
- Compound vulnerabilities: **always visible**.
- First known attack: **open**. Rest: collapsed.
- All novel attacks: **collapsed**.
- Missing controls: **always visible**.
- Remediations: **always visible**.

## Visual patterns

**Attack vector card** — header shows: ID badge (KA-1, NA-1), label, confidence badge for novel attacks (high/medium/low). Body shows: what compounds, attack path (as styled flow steps), blast radius (red warning box), precedent citation for known attacks.

Visually distinguish known vs novel:
- Known attacks: solid border, precedent section with incident name and dollar figure
- Novel attacks: dashed border, confidence badge, "no precedent" note

**Gap assessment cards** — one per category. Severity badge (critical/high/medium/low), finding text, evidence citation, impact statement. Color-code left border by severity.

**Compound vulnerability cards** — show which gaps interact (list of gap categories), severity, blast radius. Purple accent to indicate cross-domain interaction.

**Missing control cards** — control name, risk created. Simple two-column layout.

**Legal Analysis section** — organized by legal topic (e.g., licensing, capital requirements, KYC/AML). Each topic card contains: applicable laws (Vietnamese references), verbatim Vietnamese legal text in blockquotes, obligations and restrictions, risk level badge, and identified gaps. English reports: state finding in English, cite Vietnamese text in parentheses. Vietnamese reports: cite Vietnamese text inline. Always include legal reference (e.g., "NQ 05/2025, Điều 7"). Only rendered when `<legal_evaluation>` data is provided.

**Simulation section** — pass/fail table with scenario name, outcome badge (green pass, red fail, yellow partial), and finding. Discoveries listed below as warning cards with amber accent. Failed scenarios should link to the relevant remediation.

**Remediation timeline** — P0 (red), P1 (orange), P2 (blue). Each shows: label, which attacks it addresses (reference IDs), whether it requires coordinated changes across areas.

## Flows: HTML/CSS, not Mermaid

Render attack paths as styled HTML flow steps with governance gap annotations (purple badges). Mermaid goes in the markdown file only.

<examples>

Attack card:
  [KA-1] Unscoped delegation exploitation
  Confidence: [HIGH]  Precedent: Bybit ($1.5B, 2025)
  What compounds: No authority scoping + no access rules + no accountability
  Attack path:
    1. Attacker compromises agent credentials
    2. [NO DELEGATION BOUNDARY] Agent has unlimited scope
    3. Attacker executes arbitrary operations
    4. [NO AUDIT TRAIL] Actions not attributable
  Blast radius: ⚠ Complete fund drainage — no limit on scope, no way to attribute

Gap assessment card:
  [CRITICAL] Authorization Boundary Gaps
  No mechanism scopes agent authority to specific operations or amounts.
  Evidence: SECURITY.md defines roles but no per-operation delegation model.
  Impact: Any compromised agent credential grants full system access.

</examples>
