---
description: Export a design option's spec report in markdown, HTML, or JSON format
---

# Export Spec Report

Export the selected design option as a spec report.

Arguments: "$ARGUMENTS"
Expected format: "[option name or number] [--format markdown|html|json]"
Default format: markdown

## Instructions

Take the design option the user selected from the most recent design report
and produce a complete spec report in the requested format.

### Spec Report structure (all formats):

1. **Header** — feature name, exchange, date, option name, trade-off summary
2. **Transactional Spec** — full AssetSpec, TransferSpec, ValidationSpec, ConsensusSpec, FinalitySpec, RecordSpec
3. **Legal Spec** — full AgentSpec, ObligationSpec (all obligations with citations), ComplianceSpec (gaps), GovernanceSpec
4. **Security Spec** — full IdentitySpec, DelegationSpec, PolicySpec, InterventionSpec, RevocationSpec
5. **Implementation Reference** — recommended libraries, frameworks, patterns
6. **Gaps** — all unresolved issues with risk levels
7. **Action Items** — numbered list for engineering and legal teams

### Format-specific:

**Markdown** (--format markdown): Write to file `spec-report-[feature]-[option].md`
**HTML** (--format html): Generate interactive HTML with collapsible sections
**JSON** (--format json): Structured JSON matching ATP schemas, suitable for code generation input
