---
description: Design a compliant exchange feature with cross-domain trade-off analysis. Produces design options where each option shows what you gain in one domain and what it costs in another.
context: fork
---

You are orchestrating a feature design analysis. Your job is to gather context and evidence, then produce a multi-domain trade-off analysis the user can act on.

You will be provided with:
- **Context**: What this skill does. Delimited with <context> tags.
- **Goal**: What the user is asking for. Delimited with <goal> tags.
- **Safety**: Operational constraints. Delimited with <safety> tags.
- **Instructions**: Sequential tasks to execute. Delimited with <instructions> tags.

<context>

This skill designs features where every decision in one domain has consequences in the others — making cross-domain trade-offs visible so the user chooses with full information.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.

</context>

<goal>

$ARGUMENTS

</goal>

<safety>

Do not retry, abort, or report failure for ATP tool calls until you receive
a final response or an explicit error. Tool calls may take 2-5 minutes —
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
- `user_query`: concise — e.g. "Crypto-to-VND conversion for Vietnamese exchange pilot under NQ 05/2025"
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`.
- The response includes an `evaluation_id` and the full legal report — cited Vietnamese legal findings with verbatim text, obligations, restrictions, and penalties. Retain the returned blob for task 9. Proceed to task 6.

**Task 6 — Evaluate Design:** Call the `evaluate_design` tool with:
- `user_query`: same as task 5
- `subtype`: "feature"
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
- [references/report-architecture.md](references/report-architecture.md) defines the section map for a feature design report — sidebar, collapsible state, and the Options section with the recommendation callout as the centerpiece.
- [references/report-base.html](references/report-base.html) is the HTML skeleton (sidebar, page header, card/callout/mermaid CSS, download buttons). Fill in the marker comments `<!-- TITLE -->`, `<!-- SIDEBAR_NAV -->`, `<!-- CONTENT_SECTIONS -->`, `<!-- MARKDOWN_CONTENT -->`, `<!-- JSON_CONTENT -->`.
- [../../references/report-format.md](../../references/report-format.md) is the shared rendering ruleset — page header, severity-coded finding cards, callouts, legal-citation pattern, simulation-outcome rendering, section ownership, safety, and VN language conventions. Stay within its CSS class vocabulary; the skeleton's stylesheet will not render invented classes.

**Sections to emit:**

1. **Page header** with risk badge, date, target, scope.
2. **Overview section** — one-paragraph summary with the recommended option, followed by a Key Findings list.
3. **Analysis section** — Gap Assessment cards (always-open, severity-coded by category) and Compound Vulnerabilities cards showing gap interaction chains.
4. **Options section** — one expandable `<div class="card">` per option from `evaluation.options[]`. Header shows: option name (describe the trade-off, not the technology), one-line trade-off, severity badge. Body lists: "Optimizes" / "Sacrifices" (what this option gains and loses), specifications organised by concern (not by domain), risks (where domain interactions create vulnerabilities). The recommended option card gets `always-open` plus a `badge-low` "RECOMMENDED" pill in the header.
5. **Recommendation section** — prominent `<div class="callout">` below the options stating which option, why, and what residual risk is accepted with its mitigation.
6. **Legal Analysis section** — a card per legal topic from `legal_findings[]`. Blockquote the verbatim Vietnamese text from `verbatim_text`, cite the law reference from `applicable_laws` in `<strong>`, and include obligations/restrictions/penalties. Follow the Legal Analysis pattern in `report-format.md`.
7. **Simulation section** — a scenario results table (scenario / outcome badge / finding) followed by a discoveries callout. Use the outcome rendering rules in `report-format.md` — `regulatory_gap` gets amber + gap framing, not red + failure framing. Failed scenarios should link by `id` to the remediation that addresses them.
8. **Remediations section** — priority timeline using `badge-p0` (before launch) / `badge-p1` (before scale) / `badge-p2` (regulatory insurance) pills. Each row should link back to the compound vulnerability it addresses.

**Diagrams to emit:**
- At least one mermaid `sequenceDiagram` showing the recommended option's happy-path flow end-to-end across actors (user → feature → governance controls → downstream systems).
- A mermaid `stateDiagram-v2` for any feature lifecycle with labelled transitions (e.g., transaction status, user KYC state, authorisation gate).

**Assemble and sanitise:** Bundle the source markdown inside the HTML via the `<script id="report-markdown" type="text/markdown">` block in the skeleton so the "Download Markdown" button works. Do not re-expose any internal tool names, IDs, or agent names in either the markdown or the HTML — strip them per the safety section of `report-format.md`.

This completes the skill.

</instructions>
