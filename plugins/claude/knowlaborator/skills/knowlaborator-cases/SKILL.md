---
name: knowlaborator-cases
description: Inspect, execute and track Cases, their progress history and Case-owned ToDos.
---

# Knowlaborator Cases

For reads, use list_cases for bounded Workspace-scoped discovery and get_case
for one exact Case, its immutable updates and current Playbook when present.
A null currentPlaybook means standalone work. Read the complete current Playbook
before continuing an existing Case; do not replace it with remembered instructions.
Read-only requests stop after inspection.

Read [case-lifecycle.md](references/case-lifecycle.md) only when starting,
continuing or changing a Case. For a newly selected Playbook, first follow
Playbooks' discovery and execution rules, including its casePolicy.

Keep nextSteps and pastSteps concise orientation notes, not ToDos or a duplicate
of update history. Assignment and deadlines belong to Case-owned ToDos.
When the user requests Case execution, carry the work through completion or a
recorded blocker; creating the Case alone does not finish that request.
