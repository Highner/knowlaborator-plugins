# Change notices and ToDos

## Notices

- Use `list_notices` and `get_notice` for reads. Preserve the owning Workspace.
- Use `publish_notice` or `update_notice` with structured external links and
  typed member, authorized-Document, or authorized-Knowledge mentions. Never
  turn inaccessible cross-Workspace targets or display text alone into mentions.
- Authors can edit or remove their own notices. Admin-only pinning and broader
  management remain server-authorized.
- Ask for explicit confirmation before `remove_notice`; it is permanent.

## ToDos

- Use `list_todos`, `create_todo`, and `update_todo` for Workspace-owned ToDos.
- Every ToDo has a required short `header` and a required `description`. Use the
  header as the concise action label and put the supporting detail in the
  description; do not combine them into a legacy body field.
- A new ToDo starts `Open` in one exact Workspace. Every assignee must be an
  active organization member with at least `Viewer` access to that Workspace.
  Send the complete distinct assignee set on updates; never forge cross-Workspace
  assignments.
- To create a Case-owned ToDo, supply the exact `workflowCaseId` and that
  Case's current `workspaceId`. Use `list_todos` with `workflowCaseId` to
  read the Case list. A ToDo can belong to at most one Case, and `update_todo`
  cannot attach, detach, or relink it.
- A Case-owned ToDo moves atomically whenever its Case moves. The move preserves
  assignees and fails completely if any assignee lacks access to the destination
  Workspace; never compensate by silently removing assignees.
- A deadline may be absent, date-only, or date-and-time in the organization
  timezone from current context or an explicit user value. If the timezone is
  unavailable, ask rather than infer it. Never invent a time for a date-only
  deadline.
- Allowed transitions are `Open` to `Done` or `Closed`, and either terminal
  state back to `Open`. Do not request `Done` directly to `Closed` or vice versa.
- Confirm the saved header, description, complete assignee set, deadline,
  status, and derived overdue state from the mutation result.
