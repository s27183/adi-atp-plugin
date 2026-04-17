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
a final response or an explicit error. Tool calls may take 2-5 minutes —
the skill pipeline runs multiple AI agents sequentially. If you see a timeout
warning, ignore it and wait for the result.

</safety>

<instructions>

Execute the following eight tasks sequentially.

**Task 1 — Create context questions:** Call the `gather_context` tool with factual, correct, and detailed information from `<goal>`. Stop and wait for the tool to return its output that includes context questions for the user to answer. Don't proceed to task 2 until the tool has returned output.

**Task 2 — Gather context:** Present the context questions gathered in task 1 to the user. Stop and wait for their responses.

**Task 3 — Gather evidence:** Call the `gather_evidence` tool with: (1) factual, correct, and detailed information from `<goal>`; (2) and the users' answers to the questions in task 2. The tool returns an evidence summary with an `evidence_id`. Save this ID — you will pass it to the evaluation tool. Proceed to task 4.

**Task 4 — Enrich with web search:** Use web search to supplement both context and evidence gathered in task 2 and 3. Once the web search process returns output, proceed to task 5.

**Task 5 — Evaluate Legal:** Call the `evaluate_legal` tool with the following information:
- `user_query`: concise — e.g. "USDT remittance corridor legal compliance under Vietnamese law"
- `evidence_id`: the evidence ID string from task 3
- `evidence`: structured dict containing `gather_context_result` (user answers from task 2). The server resolves the full evidence internally using `evidence_id`.
- The response includes an `evaluation_id` and summary with cited Vietnamese legal findings. Save the `evaluation_id` for task 6. Proceed to task 6.

**Task 6 — Simulate:** Call the `simulate` tool with:
- `user_query`: same as task 5
- `evaluation_id`: the evaluation_id from task 5. The server resolves full legal output automatically.
- The simulation agent will run scenarios against governance controls and legal obligations to validate compliance boundaries. Once the `simulate` tool returns output, proceed to task 7.

**Task 7 — Present simulation results:** Summarize the simulation findings for the user. Highlight any scenarios that failed or produced unexpected outcomes — these indicate compliance gaps or regulatory risks. Proceed to task 8.

**Task 8 — Craft reports:** Write the findings to a markdown file, then render an interactive HTML report using [report-base.html](references/report-base.html) as skeleton and [references/report-architecture.md](references/report-architecture.md) for structure. Ground the report in both legal evaluation (task 5) and simulation (task 6) results. Include a Legal Analysis section with verbatim Vietnamese citations. Include diagrams where they clarify the analysis. Bundle the markdown inside the HTML.

</instructions>
