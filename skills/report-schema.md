# Report JSON Schema

> Data contract between analysis skills and report skills. Analysis skills produce this JSON. Report skills consume it to render interactive HTML.

## Design principles

1. **One schema, five flow types.** The top-level structure is shared. Each skill type defines its own `flow.nodes[]` content and `flow.edges[]` topology.
2. **Aspects are user-defined.** During intake, the analysis skill asks who's reading this and what they need to act on. Aspects are generated to match — not hardcoded to management/development/compliance.
3. **Progressive disclosure.** `summary` → `node.headline` → `node.aspects[].summary` → `node.aspects[].details`. Each level is sufficient on its own. Deeper levels add specificity, not different information.
4. **Findings are the data model.** Node content maps to gap assessments, compound vulnerabilities, and remediations from the analysis. This keeps the report grounded in evidence, not ad-hoc narrative.

## Top-level schema

```json
{
  "meta": {
    "skill": "design|review|compare|attack|spec",
    "subtype": "feature|protocol|compliance|security|systems|composition",
    "target": "what was analyzed (user's input)",
    "timestamp": "ISO 8601",
    "version": "2.0"
  },

  "audience": {
    "aspects": [
      {
        "name": "user-defined aspect name (e.g. 'engineering', 'board', 'legal counsel')",
        "description": "who reads this and what they need to decide or act on"
      }
    ]
  },

  "summary": {
    "headline": "one-sentence finding",
    "verdict": "overall assessment — e.g. 'critical gaps in authorization and accountability'",
    "risk_level": "critical|high|medium|low",
    "key_findings": [
      "finding 1 — one sentence each",
      "finding 2",
      "finding 3"
    ],
    "recommendation": "one-paragraph actionable recommendation"
  },

  "flow": {
    "type": "design|review|compare|attack|spec",
    "nodes": [ "...see node schema below" ],
    "edges": [
      {
        "from": "node_id",
        "to": "node_id",
        "label": "optional edge label (e.g. 'feeds into', 'constrains')"
      }
    ]
  }
}
```

## Node schema

Each node represents a stage in the analytical flow. Nodes are the clickable elements in the flow diagram.

```json
{
  "id": "unique node identifier",
  "label": "display name in flow diagram",
  "domain": "operational|regulatory|security|cross-domain|output|info",
  "status": "pass|fail|partial|info",
  "headline": "one-sentence summary shown on click",

  "findings": [
    {
      "category": "analyst-chosen category label",
      "status": "working|partial|known_gap|blind_spot",
      "evidence": "source citation or tool output reference",
      "impact": "what this finding means"
    }
  ],

  "aspects": {
    "[aspect_name]": {
      "summary": "2-3 sentences tailored to this audience",
      "details": {}
    }
  }
}
```

### Aspect detail schemas

Aspects are user-defined, but their `details` schema adapts to the aspect's nature. Common patterns:

**Technical aspects** (e.g. "engineering", "devops", "security team"):
```json
{
  "current_state": "what exists now (with code/config references)",
  "required_changes": [
    {
      "component": "what to change",
      "gap_category": "which governance gap this addresses",
      "specification": "detailed technical spec",
      "controls": ["governance controls to implement"],
      "code_snippets": [
        {
          "language": "python|typescript|json|etc",
          "description": "what this snippet does",
          "code": "actual code"
        }
      ],
      "acceptance_criteria": ["how to verify this is done"],
      "depends_on": ["other component IDs that must be done first"]
    }
  ],
  "implementation_order": ["component IDs in dependency order"]
}
```

**Decision aspects** (e.g. "management", "board", "product owner"):
```json
{
  "risk_level": "critical|high|medium|low",
  "risk_description": "what could go wrong, in business terms",
  "trade_offs": ["what you gain", "what you lose"],
  "decision_required": "what the decision maker needs to decide",
  "recommendation": "what we recommend and why",
  "cost_impact": "effort/cost estimate if available",
  "evidence": ["citations supporting the recommendation"]
}
```

**Regulatory aspects** (e.g. "legal counsel", "compliance officer", "regulator"):
```json
{
  "entity_classification": {
    "agent_type": "classification under applicable law",
    "legal_classification": "legal status",
    "jurisdiction": "applicable jurisdiction"
  },
  "obligations": [
    {
      "requirement": "what's required",
      "legal_basis": "specific law/article citation",
      "status": "met|unmet|ambiguous",
      "evidence": "how we determined status",
      "gap_risk": "critical|high|medium|low|n_a"
    }
  ],
  "regulatory_ambiguities": [
    "areas where no regulation clearly applies"
  ],
  "enforcement_capability": {
    "can_prove_compliance": true,
    "can_enforce_compliance": true,
    "gaps": ["what's missing for provability/enforceability"]
  }
}
```

**Custom aspects** — any aspect that doesn't fit the above patterns uses a freeform structure:
```json
{
  "content": "structured markdown content tailored to this audience",
  "key_points": ["actionable takeaways"],
  "evidence": ["supporting citations"]
}
```

The analysis skill selects the appropriate detail schema based on the aspect's description from intake. Multiple aspects can use the same detail pattern (e.g. two different engineering teams both get technical details, scoped to their domain).

## Flow types

### Design flow

```
nodes: intake → operational → regulatory → security → cross_domain → options → recommendation
edges: linear with cross_domain drawing from all three domain nodes
```

Nodes:
- `intake` (domain: info) — user constraints, requirements, audience definition
- `operational_analysis` (domain: operational) — protocol comparison, traceability
- `regulatory_analysis` (domain: regulatory) — classification, obligations, compliance
- `security_analysis` (domain: security) — incidents, delegation, policy
- `cross_domain` (domain: cross-domain) — cross-domain trade-offs, where domain choices conflict
- `option_1..N` (domain: output) — each design option with cross-domain trade-offs
- `recommendation` (domain: output) — selected option with rationale

### Review flow

```
nodes: classification → obligations → enforcement → evidence → cross_domain → gaps
edges: linear with cross_domain drawing from enforcement and evidence
```

Nodes:
- `classification` (domain: regulatory) — entity type, legal status
- `obligations` (domain: regulatory) — applicable requirements with citations
- `enforcement` (domain: security) — can security architecture enforce obligations?
- `evidence` (domain: operational) — can operational layer prove compliance?
- `cross_domain` (domain: cross-domain) — where compliance fails due to cross-domain gaps
- `gaps` (domain: output) — all gaps: regulatory, enforcement, evidence, cross-domain

### Compare flow

```
nodes: system_a → system_b → side_by_side → cross_domain_exposure → assessment
edges: system_a and system_b feed into side_by_side, then linear
```

Nodes:
- `system_a` (domain: info) — governance coverage for system A
- `system_b` (domain: info) — governance coverage for system B
- `side_by_side` (domain: cross-domain) — direct comparison table
- `cross_domain_exposure` (domain: cross-domain) — unique compound vulnerabilities per system
- `assessment` (domain: output) — what each system needs, ordered by impact

### Attack flow

```
nodes: gap_assessment → missing_controls → replication → discovery → remediation
edges: gap_assessment feeds both replication and discovery; both feed remediation
```

Nodes:
- `gap_assessment` (domain: info) — governance gaps: working/partial/known_gap/blind_spot
- `missing_controls` (domain: info) — absent controls and the risk each creates
- `replication` (domain: cross-domain) — known attacks matched to gap profile
- `discovery` (domain: cross-domain) — novel vectors from compound vulnerabilities
- `remediation` (domain: output) — fixes ordered by blast radius

### Spec flow

```
nodes: design_decision → api_contracts → data_models → sequence_flows → edge_cases → governance_invariants
edges: linear, with edge_cases drawing from api_contracts + data_models + sequence_flows
```

Nodes:
- `design_decision` (domain: info) — which design option was chosen and its key trade-off
- `api_contracts` (domain: operational) — endpoints with governance controls embedded
- `data_models` (domain: operational) — entities with governance fields and invariants
- `sequence_flows` (domain: cross-domain) — flows with governance control points mid-flow
- `edge_cases` (domain: security) — governance edge cases with blast radius
- `governance_invariants` (domain: output) — system-wide rules that must hold

## Export targets

Each aspect's `details` object is self-contained and exportable:

- **Markdown**: render details as structured markdown document
- **PDF**: render markdown → PDF via standard tooling
- **JSON**: raw details object for programmatic consumption

The full report JSON is also exportable as a single document combining all nodes and aspects.
