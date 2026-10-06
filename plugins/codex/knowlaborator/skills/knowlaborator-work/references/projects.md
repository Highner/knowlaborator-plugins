# Projects

Projects is an optional module (`projects`) for initiatives in one exact Workspace.
Several projects can share a Workspace, and a project's Workspace never changes.
Projects use the existing Viewer, Contributor and Manager roles; organization
administration, ownership, reporting and links grant no additional content access.
Guests can work through their authorized Workspaces.

## Read

Discover projects with `list_projects` and read one with `get_project`. Names can
repeat: preserve returned IDs and resolve ambiguity before changing anything. The
overview's sections load independently: `list_project_milestones`,
`list_project_history`, and `list_project_links` with kind `todo`, `resource`,
`decision` or `calendar_event`. An unavailable target reveals no label or identity;
never infer its contents from an old response or another transport.

## Change a project

Viewer reads; Contributor or Manager changes project data. Follow the returned
`canEdit` and `canReopen` permissions and carry the expected organization and the
exact project revision on each authorized change.

- `create_project` needs name, purpose, Workspace and one eligible active member as
  owner. Find owners with `search_project_owners`; an Org Chart person is not a
  substitute. `update_project` changes name, purpose and target dates only;
  `assign_project_owner` changes accountability explicitly.
- A revoked owner must be reassigned before starting, completing or reopening. A closed
  project permits owner correction when `canReopen` is true; other closed-project edits
  stay unavailable.
- `set_project_status` moves the lifecycle: `start`, `complete`, `cancel` or `reopen`.
  Completion is a user-authorized action, never an inference from a due date or linked
  task state. Lifecycle changes preserve milestones, ToDos, decisions and resources.
- Milestones are explicit dated checkpoints, not tasks or a dependency engine. Use
  `save_project_milestone` (with milestoneId to update), `set_project_milestone_status`
  and `remove_project_milestone`. Completing a project never completes its milestones,
  and completing a milestone changes no ToDo or project lifecycle.

## Links

`link_project_item` links one existing item without copying, moving or changing it;
`unlink_project_item` removes only the association.

- `todo`: find occurrences with `search_project_link_targets` kind `todo`. The link
  addresses one occurrence, not the whole series. Tasks stay ordinary ToDos; create
  and edit them through [todos-and-notices.md](todos-and-notices.md).
- `document` or `knowledge`: find targets with `search_project_link_targets`. Resource
  authorization is independent of project authorization.
- `decision`: targetId is the KnowledgeId and revisionId pins the exact revision. Read
  the decision first; the link does not record approval or follow later revisions.
  `list_project_links` kind `decision` returns the stored snapshot's standing,
  provenance and current-revision status; publication alone does not mean approval.
- `calendar_event`: use the event ID and opaque external event reference returned by
  Calendar; never invent either. Local recurring events link the series, provider
  events the selected occurrence. Linking copies no event content and grants no
  calendar access. Reads need fromDate and an end-exclusive toDate.

## Org Chart reference

Projects works without Org Chart. A unit or position is optional context, never an
owner, permission or approval boundary. Find a reference with
`search_org_chart_references` or `resolve_org_chart_reference`, then call
`set_project_org_chart_reference`; clear both kind and target ID to remove it. New
references need the Org Chart module and full-member directory access.
