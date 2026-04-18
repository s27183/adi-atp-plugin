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

**Task 9 — Craft reports:** Write the findings to a markdown file, then render an interactive HTML report using [report-base.html](references/report-base.html) as skeleton and [references/report-architecture.md](references/report-architecture.md) for structure. Ground the report in the full returned blobs from task 5 (legal), task 6 (attack surface), and task 7 (simulation). Cite Vietnamese legal text verbatim from `legal_findings[].verbatim_text` and reference the applicable laws from `legal_findings[].applicable_laws`. Enumerate known and novel attacks, missing controls, and compound vulnerabilities from the attack-surface report. Show per-scenario setup, action, expected, and outcome from the simulation output. Include diagrams where they clarify the analysis. Bundle the markdown inside the HTML.

</instructions>
