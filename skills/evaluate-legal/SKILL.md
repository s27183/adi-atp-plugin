---
description: Evaluate legal and regulatory compliance under Vietnamese law. Produces cited legal analysis with verbatim Vietnamese legal text, obligation mapping, and compliance status.
context: fork
---

You are orchestrating a legal compliance evaluation. Your job is to gather context and evidence, then produce a cited legal analysis grounded in Vietnamese law that the user can act on and verify.

You will be provided with:
- **Context**: What this skill does. Delimited with <context> tags.
- **Goal**: What the user is asking for. Delimited with <goal> tags.
- **Safety**: Operational constraints. Delimited with <safety> tags.
- **Instructions**: Sequential tasks to execute. Delimited with <instructions> tags.

<context>

This skill evaluates legal and regulatory compliance for activities subject to Vietnamese law — exchanges, asset issuance, remittance, trading, custody, and related operations. It returns cited analysis with verbatim Vietnamese legal text (nguyên văn), obligation mapping, and compliance status.

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

Execute the following eight tasks sequentially.

**Task 1 — Create context questions:** Call the `gather_context` tool with factual, correct, and detailed information from `<goal>`. Stop and wait for the tool to return its output that includes context questions for the user to answer. Don't proceed to task 2 until the tool has returned output.

**Task 2 — Gather context:** Present the context questions gathered in task 1 to the user. Stop and wait for their responses.

**Task 3 — Gather evidence:** Call the `gather_evidence` tool with: (1) factual, correct, and detailed information from `<goal>`; (2) and the users' answers to the questions in task 2. The tool returns an evidence summary with an `evidence_id`. Save this ID — you will pass it to the evaluation tool. Proceed to task 4.

**Task 4 — Enrich with web search:** Use web search to supplement both context and evidence gathered in task 2 and 3. Once the web search process returns output, proceed to task 5.

**Task 5 — Evaluate Legal:** Call the `evaluate_legal` tool with:
- `user_query`: concise — e.g. "USDT remittance corridor legal compliance under Vietnamese law"
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`.
- The response includes an `evaluation_id` and the full legal report — cited Vietnamese legal findings with verbatim text, applicable laws, obligations, restrictions, and penalties. Retain the returned blob for task 8. Pass only the `evaluation_id` to task 6. Proceed to task 6.

**Task 6 — Simulate:** Call the `simulate` tool with:
- `user_query`: same as task 5
- `evaluation_id`: the evaluation_id from task 5. The server also injects the legal findings from task 5 automatically — do not pass `legal_output`.
- The simulation agent runs scenarios against governance controls and checks them against cited legal obligations. The response includes the full simulation output with per-scenario setup, action, expected, and outcome. Retain the returned blob for task 8. Proceed to task 7.

**Task 7 — Present simulation results:** Summarize the simulation findings for the user. Highlight any scenarios that failed or produced unexpected outcomes — these indicate compliance gaps or regulatory risks. Proceed to task 8.

**Task 8 — Craft reports:** Produce a markdown report and a self-contained HTML report for the user, grounded in the full blobs returned by task 5 (legal) and task 6 (simulation). Three references govern the work:
- [references/report-architecture.md](references/report-architecture.md) defines the section map for a legal evaluation report — sidebar, collapsible state, and the Obligations Assessment table as the centerpiece (every obligation must carry a verbatim Vietnamese citation).
- [references/report-base.html](references/report-base.html) is the HTML skeleton (sidebar, page header, card/callout/mermaid CSS, download buttons). Fill in the marker comments `<!-- TITLE -->`, `<!-- SIDEBAR_NAV -->`, `<!-- CONTENT_SECTIONS -->`, `<!-- MARKDOWN_CONTENT -->`, `<!-- JSON_CONTENT -->`.
- [../../references/report-format.md](../../references/report-format.md) is the shared rendering ruleset — page header, severity-coded finding cards, callouts, legal-citation pattern, simulation-outcome rendering, section ownership, safety, and VN language conventions. Stay within its CSS class vocabulary; the skeleton's stylesheet will not render invented classes.

**Sections to emit:**

1. **Page header** with risk badge, date, target, scope. Subtitle states the legal verdict in one sentence (e.g., "Pilot-eligible under NQ 05/2025 subject to two unresolved ambiguities").
2. **Overview section** — one-paragraph summary with the legal verdict, followed by a Key Findings list.
3. **Classification section** (always-open) — one `<div class="card always-open">` framing the entire legal analysis: entity type, applicable legal status (e.g., "Virtual Asset Service Provider — pilot under NQ 05/2025"), jurisdiction, classification caveats or regulator positions. Ambiguities in classification get a `badge-medium` amber pill.
4. **Obligations Assessment table** (always-open) — sortable columns: requirement (English paraphrase), legal basis (Vietnamese citation with article number), verbatim text (blockquoted Vietnamese from `nguyên văn` — never paraphrase), status (`badge-low` MET / `badge-critical` UNMET / `badge-medium` AMBIGUOUS / `badge-governance` N/A), gap risk (`badge-critical` / `badge-high` / `badge-medium` / `badge-low`). This is the defensibility claim — every row must carry a verbatim citation.
5. **Ambiguities section** — one `<div class="card">` per regulatory ambiguity. Each card: the ambiguous area, why it is ambiguous (no statute / conflicting interpretations / absent jurisprudence), what the skill concluded and on what basis (e.g., applying BLDS agency principles to software agents lacking explicit regulation). Collapsed if empty, visible if populated. This is where the skill's lateral-bridging value becomes visible.
6. **Cross-Border section** — one card per jurisdiction interaction when the activity crosses borders. Each card: what each jurisdiction requires, where they conflict, which controls take precedence, unresolved gaps. Flag data-localisation, licensing reciprocity, and AML information-sharing requirements specifically. Collapsed if empty, visible if populated.
7. **Legal Analysis section** — a card per legal topic (licensing, capital, KYC/AML, cross-border transfer, consumer protection). Blockquote the verbatim Vietnamese text from `verbatim_text`, cite the law reference from `applicable_laws` in `<strong>` (e.g., "NQ 05/2025, Điều 7", "BLDS 2015, Điều 584"), and include obligations, restrictions, penalties, and identified gaps. Follow the Legal Analysis pattern in `report-format.md`.
8. **Simulation section** — a scenario results table (scenario / outcome badge / finding) followed by a discoveries callout. Use the outcome rendering rules in `report-format.md` — `regulatory_gap` gets amber + gap framing, not red + failure framing. Failed scenarios should link by `id` to the related obligation in the Obligations table.
9. **Recommendations section** — priority-ordered action list using `badge-p0` (required before activity proceeds — e.g., obtain licence, file notification) / `badge-p1` (during operation — periodic reporting, record retention) / `badge-p2` (escalate to counsel). Each recommendation must cite the obligation or ambiguity that motivates it.

**Diagrams to emit:**
- A mermaid `sequenceDiagram` showing the primary regulated workflow end-to-end across actors with governance touchpoints marked on each step.
- A mermaid `stateDiagram-v2` for any obligation with a lifecycle (AML reporting state, licence lifecycle, record retention window).

**Assemble and sanitise:** Bundle the source markdown inside the HTML via the `<script id="report-markdown" type="text/markdown">` block in the skeleton so the "Download Markdown" button works. Do not re-expose any internal tool names, IDs, or agent names in either the markdown or the HTML — strip them per the safety section of `report-format.md`.

This completes the skill.

</instructions>
