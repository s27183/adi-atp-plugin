---
description: Cross-domain composition attack on an agent system. Finds vulnerabilities where regulatory, operational, and security concerns meet — where single-domain analysis misses compound failures.
context: fork
---

You are orchestrating a cross-domain attack surface analysis. Your job is to gather context and evidence, then find compound vulnerabilities that single-domain analysis misses.

You will be provided with:
- **Context**: What this skill does. Delimited with <context> tags.
- **Goal**: What the user is asking for. Delimited with <goal> tags.
- **Safety**: Operational constraints. Delimited with <safety> tags.
- **Instructions**: Sequential tasks to execute. Delimited with <instructions> tags.

<context>

This skill finds vulnerabilities at the seams between governance domains — where single-domain analysis misses compound failures because each domain looks adequate in isolation.

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
- `user_query`: concise — e.g. "OpenClaw v6 — MCP-based agent framework"
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`.
- The response includes an `evaluation_id` and the full legal report — cited Vietnamese legal findings with verbatim text, obligations, restrictions, and penalties. Retain the returned blob for task 9. Proceed to task 6.

**Task 6 — Evaluate Attack Surface:** Call the `evaluate_attack_surface` tool with:
- `user_query`: same as task 5
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`. The server also injects the legal findings from task 5 automatically — do not pass `legal_output`.
- `audiences`: audience aspects with specific needs
- The response includes an `evaluation_id` and the full attack-surface report (known/novel attacks, missing controls, compound vulnerabilities). Retain the returned blob for task 9. Pass only the `evaluation_id` to task 7. Proceed to task 7.

**Task 7 — Simulate:** Call the `simulate` tool with:
- `user_query`: same as task 5
- `evaluation_id`: the evaluation_id from task 6. The server resolves the full evaluation and also injects legal findings from task 5 automatically.
- The simulation agent runs scenarios against operational tools (delegation, policy, identity, intervention) and checks them against cited legal obligations. The response includes the full simulation output with per-scenario setup, action, expected, and outcome. Retain the returned blob for task 9. Proceed to task 8.

**Task 8 — Present simulation results:** Summarize the simulation findings for the user. Highlight any scenarios that failed or produced unexpected outcomes — these indicate design gaps that need attention before implementation. Proceed to task 9.

**Task 9 — Craft reports:** Produce a markdown report and a self-contained HTML report for the user, grounded in the full blobs returned by task 5 (legal), task 6 (attack surface), and task 7 (simulation). Three references govern the work:
- [references/report-architecture.md](references/report-architecture.md) defines the section map for an attack surface report — sidebar, collapsible state, and the Attacks section (known + novel) as the centerpiece.
- [references/report-base.html](references/report-base.html) is the HTML skeleton (sidebar, page header, card/callout/mermaid CSS, download buttons). Fill in the marker comments `<!-- TITLE -->`, `<!-- SIDEBAR_NAV -->`, `<!-- CONTENT_SECTIONS -->`, `<!-- MARKDOWN_CONTENT -->`, `<!-- JSON_CONTENT -->`.
- [../../references/report-format.md](../../references/report-format.md) is the shared rendering ruleset — page header, severity-coded finding cards, callouts, legal-citation pattern, simulation-outcome rendering, section ownership, safety, and VN language conventions. Stay within its CSS class vocabulary; the skeleton's stylesheet will not render invented classes.

**Sections to emit:**

1. **Page header** with risk badge, date, target, scope. Subtitle states the attack-surface verdict in one sentence.
2. **Overview section** — one-paragraph summary with the attack-surface verdict, followed by a Key Findings list.
3. **Analysis section** — Gap Assessment cards (always-open, severity-coded by category) and Compound Vulnerabilities cards showing which gap categories interact, severity, and blast radius.
4. **Attacks section** — two subsections:
   - **Known Attacks** — one `<div class="card">` per item from `known_attacks[]`, first one `always-open`. Header: `[KA-1]` ID badge, attack label, severity badge. Body: "What compounds" (list of gap categories), attack path as `flow-step` HTML (each step shows actor and the governance gap it exploits via `badge-governance` or `badge-critical`), blast radius in `<div class="warning">`, precedent citation (incident name + dollar figure) with a solid card border styling cue.
   - **Novel Attacks** — one card per item from `novel_attacks[]`, all collapsed. Same structure but with a `[NA-n]` ID badge and a confidence pill (`badge-low` HIGH / `badge-medium` MEDIUM / `badge-high` LOW-confidence) plus a "no precedent" note. Use dashed border styling cue.
5. **Controls section** — Missing Controls cards (always-visible), one per item from `missing_controls[]`. Two-column layout: control name + risk created.
6. **Legal Analysis section** — a card per legal topic from `legal_findings[]`. Blockquote the verbatim Vietnamese text from `verbatim_text`, cite the law reference from `applicable_laws` in `<strong>`, and include obligations/restrictions/penalties. Follow the Legal Analysis pattern in `report-format.md`.
7. **Simulation section** — a scenario results table (scenario / outcome badge / finding) followed by a discoveries callout. Use the outcome rendering rules in `report-format.md` — `regulatory_gap` gets amber + gap framing, not red + failure framing. Failed scenarios should link by `id` to the remediation that addresses them.
8. **Remediation section** — prioritised fixes timeline using `badge-p0` / `badge-p1` / `badge-p2` pills. Each row: label, which attacks it addresses (reference `KA-n` / `NA-n` IDs), whether it requires coordinated changes across areas.

**Diagrams to emit:** attack paths render as styled HTML flow-steps with governance-gap annotations (`badge-governance`, `badge-critical`) in the HTML report. Mermaid goes in the bundled markdown only — a `sequenceDiagram` for at least one known attack and one novel attack showing attacker → compromised control → blast-radius actor chain. Do not inline mermaid into the HTML report.

**Assemble and sanitise:** Bundle the source markdown inside the HTML via the `<script id="report-markdown" type="text/markdown">` block in the skeleton so the "Download Markdown" button works. Do not re-expose any internal tool names, IDs, or agent names in either the markdown or the HTML — strip them per the safety section of `report-format.md`.

This completes the skill.

</instructions>
