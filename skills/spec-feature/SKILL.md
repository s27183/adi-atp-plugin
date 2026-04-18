---
description: Generate governance-aware implementation specifications from a design decision. Produces API contracts, data models, sequence flows, and edge cases where governance controls are structural — embedded in the spec, not bolted on after.
context: fork
---

You are orchestrating an implementation spec generation. Your job is to gather context and evidence, then produce governance-aware specs a technical team can build from.

You will be provided with:
- **Context**: What this skill does. Delimited with <context> tags.
- **Goal**: What the user is asking for. Delimited with <goal> tags.
- **Safety**: Operational constraints. Delimited with <safety> tags.
- **Instructions**: Sequential tasks to execute. Delimited with <instructions> tags.

<context>

This skill translates a design decision into implementation specs a technical team can build from. Governance controls are structural — embedded in every API endpoint, data model, and sequence flow as first-class elements, not added as a compliance layer after.

Chains from design-feature. If `<goal>` does not contain a chosen design option with its key trade-off, ask the user to either provide one or run `/design-feature` first — do not invent a design decision. After the spec is produced, render it as an interactive HTML report with bundled markdown.

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
- `user_query`: concise — e.g. "Crypto-to-VND spot conversion (BTC, ETH, USDT) — greenfield, governance-first, pre-license"
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`.
- The response includes an `evaluation_id` and the full legal report — cited Vietnamese legal findings with verbatim text, obligations, restrictions, and penalties. Retain the returned blob for task 9. Proceed to task 6.

**Task 6 — Evaluate Spec:** Call the `evaluate_spec` tool with:
- `user_query`: same as task 5
- `design_decision`: the chosen option and its key trade-off
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`. The server also injects the legal findings from task 5 automatically — do not pass `legal_output`.
- `audiences`: audience aspects with specific needs
- The response includes an `evaluation_id` and the full spec report (API contracts, data models, sequence flows, edge cases). Retain the returned blob for task 9. Pass only the `evaluation_id` to task 7. Proceed to task 7.

**Task 7 — Simulate:** Call the `simulate` tool with:
- `user_query`: same as task 5
- `evaluation_id`: the evaluation_id from task 6. The server resolves the full evaluation and also injects legal findings from task 5 automatically.
- The simulation agent runs scenarios against operational tools (delegation, policy, identity, intervention) and checks them against cited legal obligations. The response includes the full simulation output with per-scenario setup, action, expected, and outcome. Retain the returned blob for task 9. Proceed to task 8.

**Task 8 — Present simulation results:** Summarize the simulation findings for the user. Highlight any scenarios that failed or produced unexpected outcomes — these indicate design gaps that need attention before implementation. Proceed to task 9.

**Task 9 — Craft reports:** Produce a markdown spec and a self-contained HTML report for the user, grounded in the full blobs returned by task 5 (legal), task 6 (spec), and task 7 (simulation). Three references govern the work:
- [references/report-architecture.md](references/report-architecture.md) defines the section map for an implementation spec report — sidebar, collapsible state, and the API Contracts / Data Models / Sequence Flows triptych as the centerpiece.
- [references/report-base.html](references/report-base.html) is the HTML skeleton (sidebar, page header, card/callout/mermaid CSS, download buttons). Fill in the marker comments `<!-- TITLE -->`, `<!-- SIDEBAR_NAV -->`, `<!-- CONTENT_SECTIONS -->`, `<!-- MARKDOWN_CONTENT -->`, `<!-- JSON_CONTENT -->`.
- [../../references/report-format.md](../../references/report-format.md) is the shared rendering ruleset — page header, severity-coded finding cards, callouts, legal-citation pattern, simulation-outcome rendering, section ownership, safety, and VN language conventions. Stay within its CSS class vocabulary; the skeleton's stylesheet will not render invented classes.

**Sections to emit:**

1. **Page header** with risk badge, date, target, scope.
2. **Overview section** — Design Decision in a `<div class="callout">` (what was chosen, what trade-off was accepted), followed by a Key Findings list.
3. **Analysis section** — Gap Assessment cards (always-open) and Compound Vulnerabilities cards per the shared finding card pattern.
4. **Contracts section** — API Contract cards with method badges (`badge-method-post`, `badge-method-get`, `badge-method-delete`), path in `<code>`, expandable body with request/response tables and a governance-control bullet list. Data Model cards with a field table that includes a "Governance Role" column; `badge-governance` pills on fields that exist for governance (audit, attribution, scope enforcement). First API endpoint `always-open`, rest collapsed.
5. **Behavior section** — Sequence Flows using the `flow-step` / `flow-step-num` / `flow-step-actor` / `flow-step-action` / `flow-step-gov` HTML pattern (always visible, not collapsible). Edge Cases as collapsed `<div class="card">` accordions with trigger / governance impact / required response / blast radius (`<div class="warning">` for red blast radius).
6. **Legal Analysis section** — a card per legal topic from `legal_findings[]`. Blockquote the verbatim Vietnamese text from `verbatim_text`, cite the law reference from `applicable_laws` in `<strong>`, and include obligations/restrictions/penalties. Follow the Legal Analysis pattern in `report-format.md`.
7. **Simulation section** — a scenario results table (scenario / outcome badge / finding) followed by a discoveries callout. Use the outcome rendering rules in `report-format.md` — `regulatory_gap` gets amber + gap framing, not red + failure framing. Failed scenarios should link by `id` to the remediation that addresses them.
8. **Governance section** — Invariants as numbered `<div class="invariant">` cards (`INV-01`, `INV-02` …) for system-wide "always" and "never" rules. Dependencies as a table. Remediations as a `badge-p0` / `badge-p1` / `badge-p2` timeline, each row showing label + what it addresses.

**Diagrams to emit:** sequence flows and state machines render as styled HTML flow-steps/`state-machine` pills in the HTML report (so governance badges render inline). Mermaid goes in the bundled markdown only — a `sequenceDiagram` for the primary end-to-end flow and a `stateDiagram-v2` for any entity lifecycle (transaction status, KYC status). Do not inline mermaid into the HTML report.

**Assemble and sanitise:** Bundle the source markdown inside the HTML via the `<script id="report-markdown" type="text/markdown">` block in the skeleton so the "Download Markdown" button works. Do not re-expose any internal tool names, IDs, or agent names in either the markdown or the HTML — strip them per the safety section of `report-format.md`.

This completes the skill.

</instructions>
