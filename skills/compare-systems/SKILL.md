---
description: Compare agent systems on governance maturity — what's working, what's a known gap, and what's a blind spot.
context: fork
---

You are orchestrating a governance maturity comparison. Your job is to gather context and evidence, then produce a side-by-side assessment the user can act on.

You will be provided with:
- **Context**: What this skill does. Delimited with <context> tags.
- **Goal**: What the user is asking for. Delimited with <goal> tags.
- **Safety**: Operational constraints. Delimited with <safety> tags.
- **Instructions**: Sequential tasks to execute. Delimited with <instructions> tags.

<context>

This skill compares systems on governance maturity — the gap between capability (what the system can do) and governance (what controls exist over what it does). Two systems with the same maturity score can have radically different exposure profiles.

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

**Task 3 — Gather evidence:** Call the `gather_evidence` tool with: (1) factual, correct, and detailed information from `<goal>`; (2) and the users' answers to the questions in task 2. Once the tool returns output, proceed to task 4.

**Task 4 — Enrich with web search:** Use web search to supplement both context and evidence gathered in task 2 and 3. Once the web search process returns output, proceed to task 5.

**Task 5 — Evaluate Comparison:** Call the `evaluate_comparison` tool with the following information:
- `target`: concise — e.g. "OpenClaw vs LangChain — governance maturity"
- `systems`: array of `{name, evidence}` — one per system, each with structured evidence dict
- `evidence`: structured dict. Evidence dict MUST include both `gather_evidence_result` and `gather_context_result`.
- `audiences`: audience aspects with specific needs
- Once the `evaluate_comparison` tool returns output, proceed to task 6.

**Task 6 — Simulate:** Call the `simulate` tool with:
- `target`: same target as task 5
- `evaluation`: the full evaluation output from task 5
- The simulation agent will run scenarios from the evaluation output against operational tools (delegation, policy, identity, intervention) to validate whether the design actually works. Once the `simulate` tool returns output, proceed to task 7.

**Task 7 — Present simulation results:** Summarize the simulation findings for the user. Highlight any scenarios that failed or produced unexpected outcomes — these indicate design gaps that need attention before implementation. Proceed to task 8.

**Task 8 — Craft reports:** Write the findings to a markdown file, then render an interactive HTML report using [report-base.html](references/report-base.html) as skeleton and [references/report-architecture.md](references/report-architecture.md) for structure. Ground the report in both evaluation (task 5) and simulation (task 6) results. Include a Simulation section showing scenario pass/fail results and discoveries. Include diagrams where they clarify the analysis. Bundle the markdown inside the HTML.

</instructions>
