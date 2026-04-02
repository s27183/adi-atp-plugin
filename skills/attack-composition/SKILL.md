---
description: Cross-domain composition attack on an agent system. Finds vulnerabilities where regulatory, operational, and security concerns meet — where single-domain analysis misses compound failures.
context: fork
---

You are orchestrating a cross-domain attack surface analysis. Your job is to gather context and evidence, then find compound vulnerabilities that single-domain analysis misses.

You will be provided with:
- **Context**: What this skill does. Delimited with <context> tags.
- **Goal**: What the user is asking for. Delimited with <goal> tags.
- **Instructions**: Sequential tasks to execute. Delimited with <instructions> tags.

<context>

This skill finds vulnerabilities at the seams between governance domains — where single-domain analysis misses compound failures because each domain looks adequate in isolation.

See [references/report-architecture.md](references/report-architecture.md) for report rendering guidance.

</context>

<goal>

$ARGUMENTS

</goal>

<instructions>

Execute the following six tasks sequentially.

**Task 1 — Create context questions:** Call the `gather_context` tool with factual, correct, and detailed information from `<goal>`. Stop and wait for the tool to return its output that includes context questions for the user to answer. Don't proceed to task 2 until the tool has returned output.

**Task 2 — Gather context:** Present the context questions gathered in task 1 to the user. Stop and wait for their responses.

**Task 3 — Gather evidence:** Call the `gather_evidence` tool with: (1) factual, correct, and detailed information from `<goal>`; (2) and the users' answers to the questions in task 2. Once the tool returns output, proceed to task 4.

**Task 4 — Enrich with web search:** Use web search to supplement both context and evidence gathered in task 2 and 3. Once the web search process returns output, proceed to task 5.

**Task 5 — Evaluate Attack Surface:** Call the `evaluate_attack_surface` tool with the following information:
- `target`: concise — e.g. "OpenClaw v6 — MCP-based agent framework"
- `evidence`: structured dict. Evidence dict MUST include both `gather_evidence_result` and `gather_context_result`.
- `audiences`: audience aspects with specific needs
- Once the `evaluate_attack_surface` tool returns output, proceed to task 6.

**Task 6 — Craft reports:** Write the findings to a markdown file, then render an interactive HTML report using [report-base.html](references/report-base.html) as skeleton and [references/report-architecture.md](references/report-architecture.md) for structure. Include diagrams where they clarify the analysis. Bundle the markdown inside the HTML.

</instructions>
