---
description: Design or evaluate a protocol configuration for a specific use case. Goes beyond operational matching to show regulatory and security consequences of protocol choices.
context: fork
---

You are orchestrating a protocol design analysis. Your job is to gather context and evidence, then produce a cross-domain evaluation of protocol choices the user can act on.

You will be provided with:
- **Context**: What this skill does. Delimited with <context> tags.
- **Goal**: What the user is asking for. Delimited with <goal> tags.
- **Safety**: Operational constraints. Delimited with <safety> tags.
- **Instructions**: Sequential tasks to execute. Delimited with <instructions> tags.

<context>

This skill selects protocols where every property has cross-domain consequences: fast finality reduces response windows, high throughput complicates accountability, small validator sets may satisfy regulatory preference but create concentration risk.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.

</context>

<goal>

$ARGUMENTS

</goal>

<safety>

Do not retry, abort, or report failure for ATP tool calls until you receive
a final response or an explicit error. Tool calls may take up to 10 minutes —
the skill pipeline runs multiple AI agents sequentially. If you see a timeout
warning, ignore it and wait for the result.

</safety>

<instructions>

Execute the following nine tasks sequentially.

**Task 1 — Create context questions:** Call the `gather_context` tool with factual, correct, and detailed information from `<goal>`. Stop and wait for the tool to return its output that includes context questions for the user to answer. Don't proceed to task 2 until the tool has returned output.

**Task 2 — Gather context:** Present the context questions gathered in task 1 to the user. Stop and wait for their responses.

**Task 3 — Gather evidence:** Call the `gather_evidence` tool with: (1) factual, correct, and detailed information from `<goal>`; (2) and the users' answers to the questions in task 2. The tool returns an evidence summary with an `evidence_id`. Save this ID — you will pass it to the evaluation tool. Proceed to task 4.

**Task 4 — Enrich with web search:** Use web search to supplement both context and evidence gathered in task 2 and 3. Once the web search process returns output, proceed to task 5.

**Task 5 — Evaluate Legal:** Call the `evaluate_legal` tool with:
- `user_query`: concise — e.g. "Protocol selection for VND settlement rail — Ethereum vs Solana vs Tron"
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`.
- The response includes an `evaluation_id` and the full legal report — cited Vietnamese legal findings with verbatim text, obligations, restrictions, and penalties. Retain the returned blob for task 9. Proceed to task 6.

**Task 6 — Evaluate Design:** Call the `evaluate_design` tool with:
- `user_query`: same as task 5
- `subtype`: "protocol"
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`. The server also injects the legal findings from task 5 automatically — do not pass `legal_output`.
- `audiences`: audience aspects with specific needs
- The response includes an `evaluation_id` and the full design report (options, recommendation, remediations). Retain the returned blob for task 9. Pass only the `evaluation_id` to task 7. Proceed to task 7.

**Task 7 — Simulate:** Call the `simulate` tool with:
- `user_query`: same as task 5
- `evaluation_id`: the evaluation_id from task 6. The server resolves the full evaluation and also injects legal findings from task 5 automatically.
- The simulation agent runs scenarios against operational tools (delegation, policy, identity, intervention) and checks them against cited legal obligations. The response includes the full simulation output with per-scenario setup, action, expected, and outcome. Retain the returned blob for task 9. Proceed to task 8.

**Task 8 — Present simulation results:** Summarize the simulation findings for the user. Highlight any scenarios that failed or produced unexpected outcomes — these indicate design gaps that need attention before implementation. Proceed to task 9.

**Task 9 — Craft reports:** Produce a markdown report and a self-contained HTML report for the user, grounded in the full blobs returned by task 5 (legal), task 6 (design), and task 7 (simulation). Three references govern the work:
- [references/report-architecture.md](references/report-architecture.md) defines the section map for a protocol design report — sidebar, collapsible state, and the protocol comparison table as the centerpiece.
- [references/report-base.html](references/report-base.html) is the HTML skeleton (sidebar, page header, card/callout/mermaid CSS, download buttons). Fill in the marker comments `<!-- TITLE -->`, `<!-- SIDEBAR_NAV -->`, `<!-- CONTENT_SECTIONS -->`, `<!-- MARKDOWN_CONTENT -->`, `<!-- JSON_CONTENT -->`.
- [../../references/report-format.md](../../references/report-format.md) is the shared rendering ruleset — page header, severity-coded finding cards, callouts, legal-citation pattern, simulation-outcome rendering, section ownership, safety, and VN language conventions. Stay within its CSS class vocabulary; the skeleton's stylesheet will not render invented classes.

**Sections to emit:**

1. **Page header** with risk badge, date, target, scope.
2. **Overview section** — one-paragraph summary with the recommended protocol, followed by a Key Findings list.
3. **Protocol comparison table** as an `always-open` card — candidates as columns, properties as rows (consensus, finality, throughput, audit capability, response window, fee model). Cells coloured with the severity badges (`badge-low` green for favourable, `badge-medium` amber for trade-off, `badge-critical` red for exposure).
4. **Per-candidate protocol cards** — one card per option from `evaluation.options[]`. Header shows protocol name + cross-domain profile + severity badge. Body lists actual property values (not "fast" — "2.5s finality"), cross-domain consequences (what each property implies for compliance and security), and protocol-specific risks for this use case. Recommended protocol card gets `always-open`.
5. **Cross-domain consequences** — only findings that require composing two or more domains. Use the finding card pattern with a severity badge and mandatory **Evidence** + **Impact** paragraphs.
6. **Recommendation** — a `<div class="callout">` with the chosen protocol and the one-sentence justification.
7. **Legal Analysis section** — a card per legal topic from `legal_findings[]`. Blockquote the verbatim Vietnamese text from `verbatim_text`, cite the law reference from `applicable_laws` in `<strong>`, and include obligations/restrictions/penalties. Follow the Legal Analysis pattern in `report-format.md`.
8. **Simulation section** — a scenario results table (scenario / outcome badge / finding) followed by a discoveries callout. Use the outcome rendering rules in `report-format.md` — `regulatory_gap` gets amber + gap framing, not red + failure framing. Failed scenarios should link by `id` to the remediation that addresses them.
9. **Remediations** — priority-tagged list. Use `badge-p0` / `badge-p1` / `badge-p2` pills for immediate / short-term / medium-term.

**Diagrams to emit:**
- At least one mermaid `sequenceDiagram` showing the recommended protocol's happy-path settlement flow end-to-end across actors (user → VASP → chain → counterparty).
- A mermaid `stateDiagram-v2` for any transaction lifecycle with labelled transitions.

**Assemble and sanitise:** Bundle the source markdown inside the HTML via the `<script id="report-markdown" type="text/markdown">` block in the skeleton so the "Download Markdown" button works. Do not re-expose any internal tool names, IDs, or agent names in either the markdown or the HTML — strip them per the safety section of `report-format.md`.

This completes the skill.

</instructions>
