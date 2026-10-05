---
name: knowlaborator-projects
description: Read and maintain Workspace-owned initiatives, accountable owners, target dates, explicit milestones, and authorized links in the optional Projects module.
---

# Knowlaborator Projects

Use Projects for initiatives in one exact Workspace. Several projects can share a
Workspace. Projects use the existing Viewer, Contributor and Manager roles;
organization administration, ownership, reporting and links grant no additional
content access. Guests can work through their authorized Workspaces. Restricted
connector capability and realm ceilings still apply. Cases are deprecated and
are never project entities.

Retain the current active organization. Inspect `list_organization_modules` when
availability matters; the module ID is `projects`. Discover projects through
`list_projects`, use returned cursors unchanged, and read an exact project with
`get_project`. Names can repeat: preserve returned IDs and resolve ambiguity
before changing anything. A project owns exactly one immutable Workspace.

The overview's sections are independent. Use `list_project_milestones`,
`list_project_todos`, `list_project_resources`, `list_project_decisions` and
`list_project_history` only as needed. An unavailable target reveals no label or
identity. Never infer its contents from an old response or search another
organization/transport to bypass a denied read.

## Explicit changes

Viewer reads; Contributor or Manager changes project data. Follow the returned
`canEdit` and `canReopen` permissions and the server's current checks. Carry the
expected organization, exact project revision and a stable idempotency key for
each authorized mutation. Reuse a key only for identical input on a lost response.
On a revision conflict, read fresh state and reconcile the intent; do not silently
overwrite another editor or replay a different change under the old key.

`create_project` requires name, purpose, Workspace and one eligible active
organization-membership owner. Discover eligible memberships with
`search_project_owners` rather than inventing account IDs. The owner must currently
have access to that Workspace; an Org Chart person is not a substitute.
`update_project` changes name/purpose/target dates only; `assign_project_owner`
changes accountability explicitly. A revoked owner becomes unavailable and must
be reassigned before starting, completing or reopening the project. A closed
project permits authorized owner correction when `canReopen` is true, so the
owner can be repaired before explicit reopen. Other closed-project edits remain
unavailable. Unrelated permitted
edits do not require repairing the owner or installing Org Chart.

Use `start_project`, `complete_project`, `cancel_project` and `reopen_project` for
explicit lifecycle changes. Planned, active, completed and cancelled are project
states. Completion is a user-authorized action, never an inference from a due date
or linked task state. Completion, cancellation and reopening preserve milestones,
ordinary ToDos, decisions and resources. No silent task completion/deletion follows.

Milestones are explicit dated checkpoints, not ordinary tasks or a dependency
engine. Use `create_project_milestone`, `update_project_milestone`,
`complete_project_milestone`, `reopen_project_milestone` and
`remove_project_milestone`. Completing a project never completes its checkpoints;
completing a checkpoint changes no core ToDo or project lifecycle.

## Core task and resource links

Discover existing same-Workspace occurrences with `search_project_todos`, then
`link_project_todo` using the exact ToDo ID. The link addresses one occurrence,
not the whole recurrence series; later occurrences do not inherit it. Tasks stay
ordinary core ToDos visible in Today and task views. Use the
[Notices and ToDos skill](../knowlaborator-notices-and-todos/SKILL.md) for task
creation, editing, completion and recurrence. `unlink_project_todo` removes only
the association. A later legitimate core task move can make a retained link
unavailable; do not move it back or copy it without a separate user request.

Use `search_project_resources` with kind `document` or `knowledge`, then
`link_project_resource`. Exact resource authorization is independent of project
authorization and cross-Workspace references grant nothing. Unlink with
`unlink_project_resource`; the target remains unchanged. Read or change targets
through their owning [Documents](../knowlaborator-documents/SKILL.md) or
[Knowledge](../knowlaborator-knowledge/SKILL.md) operations.

`link_project_decision` pins exact `knowledgeId` and `revisionId` coordinates.
Read the decision first through the core Knowledge decision operations. The link
does not record approval, request a review, or substitute a later revision.
`list_project_decisions` returns honest standing, standing version, provenance,
publication lifecycle and current-revision status for the exact stored snapshot.
Treat proposal, current, withdrawn and superseded standing distinctly; publication
alone does not mean approval. `unlink_project_decision` changes only the project
association.

## Optional Org Chart reference

Projects works without Org Chart. A unit or position is optional context and never
an owner, permission or approval boundary. For a requested new/replaced reference,
use authorized `search_org_chart_references` or `resolve_org_chart_reference`, then
`set_project_org_chart_reference`. The directory requires separate Full-member
access, enabled Org Chart and an active same-organization target. Guests and
realm-bound directory restrictions remain enforced even with project edit access.
Clear both kind and target ID to remove. Unchanged or cleared references and
unrelated project changes need no Org Chart availability. Archived/deactivated
retained references remain readable only to currently authorized directory users.

Projects deactivation preserves authorized reads/history and blocks module-owned
mutations. Linked core actions remain governed by their own operations. On
`MODULE_NOT_INSTALLED` or `MODULE_DEACTIVATED`, refresh availability and explain
what remains possible. Full administrators manage lifecycle in
**Organization Settings -> Modules**; never bypass the state or add a second
plugin, ownership model, Invoicing, time billing or Gantt/dependency engine.

## Calendar events on the timeline

Use `link_project_calendar_event` to associate an exact authorized calendar event
with an open project. An event can be linked to several projects. Use the event ID
and opaque external event reference returned by Calendar; never invent either.
Local recurring events link the series; provider events link the selected event
or occurrence. Project revision, expected organization and a stable idempotency
key remain required. Linking does not copy event content, grant calendar access,
create invitations or reschedule a meeting.

`list_project_calendar_events` reads these links with an end-exclusive date range
(up to ten years), actor-bound cursors and at most 25 links per page. Local series
expand through Calendar's recurrence rules with at most 100 occurrences per link;
`moreAvailable` honestly reports truncation. Single events retain their actual
dates even outside the recurrence window. External events resolve live through
the owning member's connected provider. Unauthorized events reveal no identity,
title, time or provider reference. Retryable source failures remain independent
of milestones and other calendar links. `unlink_project_calendar_event` removes
only the association and never deletes or edits the calendar event.
