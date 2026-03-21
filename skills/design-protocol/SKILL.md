---
description: Design or evaluate a protocol configuration for a specific use case
---

# Design Protocol

Design protocol for: "$ARGUMENTS"

## Steps

1. **Understand requirements** — what the protocol needs to support (asset types, transaction patterns, throughput, finality needs)

2. **Compare candidates** — use compare_protocols across relevant primitives:
   - consensus (throughput vs decentralization)
   - finality (speed vs certainty)
   - validation (what checks exist on-chain)
   - record (what's auditable)

3. **Analyze traceability** — use analyze_traceability to identify gaps per protocol

4. **Get design patterns** — use get_design_pattern for implementation guidance

5. **Produce recommendation** with:
   - Protocol selection with rationale
   - Traceability gaps and how ATP closes them (cross-pillar composition)
   - Design patterns to apply
   - Implementation considerations
