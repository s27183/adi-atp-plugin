---
description: Design a compliant exchange feature using ATP three-pillar analysis. Produces a design report with multiple options showing trade-offs across legal, transactional, and security pillars.
---

# Design Feature

The user wants to design: "$ARGUMENTS"

## Phase 1: Intake — understand the user's constraints

Before generating options, ask the user these questions to narrow the design space.
Ask them conversationally, not as a checklist. Skip questions whose answers are
obvious from the request.

**Business constraints:**
- What's your priority — speed to market, regulatory safety, or user experience?
- What's your target transaction volume and typical transaction size?
- Any hard constraints? (e.g., "must not use Tron", "must support mobile", budget limits)

**Technical constraints:**
- Which chains/protocols do you already support or prefer?
- Do you need to support AI agents acting on behalf of users, or human-only for now?
- What's your existing tech stack? (helps with implementation library recommendations)

**Risk tolerance:**
- How do you handle regulatory ambiguity? (conservative: wait for guidance / moderate: build with safeguards / aggressive: launch and adapt)
- What's your appetite for new/less-proven technology vs established patterns?

Wait for the user's answers before proceeding. Their answers define which design
options are relevant and which trade-offs matter.

## Phase 2: Three-pillar analysis

With constraints understood, query ATP tools across all three pillars.
Attach delegation context to all tool calls.

**Transactional pillar:**
- compare_protocols for relevant primitives (finality, consensus, fee model)
- analyze_traceability for candidate protocols
- Identify fee model implications against user's volume/size targets

**Legal pillar:**
- classify_agent for the exchange
- get_obligations for the activity type
- check_compliance for the described feature
- Note all gaps and ambiguities

**Security pillar:**
- Query security incidents relevant to this feature type
- Identify delegation model, policy requirements, intervention needs
- Match user's risk tolerance to security patterns

## Phase 3: Generate design report

Based on the pillar analysis AND the user's constraints, generate 2-4 design
options. Each option represents a different trade-off between pillars.
Do not pad options — only include meaningfully different alternatives.

### Design Report structure:

**For each option:**
1. **Option name** — short, descriptive (e.g., "Conservative Ethereum", "Fast Tron with guardrails")
2. **Trade-off summary** — one paragraph: what this option optimizes for and what it sacrifices
3. **Transactional spec** — chain, protocol, fee model, finality, traceability analysis
4. **Legal spec** — agent classification, obligations with citations, compliance status, gaps
5. **Security spec** — delegation model, policies, intervention capability, relevant incidents
6. **Implementation reference** — which libraries/frameworks suit this option
7. **Risk assessment** — what could go wrong, how likely, how to mitigate

**After all options:**
- **Comparison matrix** — options side by side on key dimensions
- **Recommendation** — which option best fits the user's stated constraints, and why

Every claim must cite its source: legal document, protocol data, or security incident.

## Phase 4: Iterate with user

After presenting the design report:
- Ask if any option should be explored deeper
- Offer to export the selected option as a full spec report (markdown, HTML, or JSON)
- If the user wants implementation code, use the spec report + protocol library MCPs
