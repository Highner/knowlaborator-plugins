---
name: knowlaborator-work
description: Coordinate everyday organization work (Workspaces, ToDos, notices, calendar events, payments and projects), choose a durable destination, delegate broad research, or close an agenda item after fulfilled user-authorized work.
---

# Knowlaborator Work

Work owns ordinary operational work and decisions that cross functions. Read only
what the request needs:

- [workspaces.md](references/workspaces.md): Workspace discovery, selection and roles.
- [todos-and-notices.md](references/todos-and-notices.md): ToDos, their context
  links and recurrence, and the notice board.
- [calendar.md](references/calendar.md): calendar reads and event changes.
- [payments.md](references/payments.md): payment suggestions with a QR code (core),
  saved Banking payments, ToDo payments and shared payment responsibility.
- [projects.md](references/projects.md): initiatives, milestones and project links.
- [durable-form.md](references/durable-form.md): where to save information without
  an explicit destination.
- [delegated-discovery.md](references/delegated-discovery.md): a broad read-only
  research pass on clients with isolated subagents.
- [file-inspection.md](references/file-inspection.md): staging and inspecting an
  original file returned by any exact-file tool.

A read or ordinary operational request does not authorize ambient knowledge
capture. For explicit research, analysis, brainstorming, decision support, save
or import, use Knowledge's task-scoped capture guidance. A decision's follow-up
to-dos and milestones keep their own state; link them from its follow-up plan
([decision-loops.md](../knowlaborator-knowledge/references/decision-loops.md)).

## Optional modules

Projects (`projects`), Org Chart (`org-chart`), Banking (`banking`), the Notice
board (`notices`) and other optional modules list their tools only where they are
installed. Use `list_organization_modules` when availability matters; it never
grants record access. A deactivated module keeps authorized reads and history while
its writes pause; linked core ToDos, Documents, Knowledge and decisions keep working
through their own operations. On `MODULE_NOT_INSTALLED` or `MODULE_DEACTIVATED`,
refresh availability and explain what remains possible. Installation belongs to
administrators in **Organization Settings -> Modules**; never bypass a module state
through another organization, endpoint or copied tool list.

## Work from an agenda item

A selected agenda item is context, not authorization to execute source or domain
actions. Items reach the basket from the OrgApp browser or the Today desk alike.
Resolve the exact item with `get_active_context`, retain its item ID, and read its
MembershipId and current Version. Obtain the user's actual work request and use the
owning skill. Recheck source access and current source facts before acting; advisory
links never grant access.

For a user-authorized ToDo handoff, use `create_todo` or `update_todo` with optional
`agendaItem` as described in [todos-and-notices.md](references/todos-and-notices.md).
This atomically saves or links the task and closes the item as done; do not close it
again after a successful handoff.

After successfully fulfilling other user-authorized work that originated from the item,
call `close_agenda_item` as the final step with Reason `done`. Supply ExpectedMembershipId
from the returned MembershipId, ExpectedVersion from the item's Version, a fresh
OperationId UUID, and an Outcome of at most 80 plain-text characters describing what
actually happened, for example "Antwort entworfen". Link the exact ToDo or calendar event
the work created with TodoId or Event. OrgApp attributes the actor and time; never invent
them. Reading an item, adding it to the basket, discussing possibilities, creating an
unfinished plan, or failing the requested work does not count as done. Do not record a
promise as a completed outcome.

Reuse the same operation ID and identical request on a lost response. A stale Version
requires a fresh exact read and confirmation of the same issue; never guess, silently
substitute a refreshed item, or overwrite a newer outcome. Items the person closed stay
closed: only the person reopens them in the browser. Report failed closing honestly even
when the underlying work succeeded. Merely mentioning ExistingTodoId on an item does not
accept a handoff, and an unavailable task never authorizes a silent replacement.
When the user asks you to attach an item's suggested `TodoAttachment` sources, link each with
`link_todo_context`, then close the item with Reason `done` and the TodoId.

Agenda preparation remains proposal-only: adding or updating items does not execute
them. To add or change agenda items, read `get_agenda_context` and follow
[My Day](../knowlaborator-my-day/SKILL.md): update the open item that already covers an
issue with its current Version, add only genuinely new issues, and keep every text very
short.

## Shared work

Read `get_shared_work` for every agenda WorkReferences entry before acting. Use the exact
SharedWorkId on `create_todo`, `create_calendar_event` or `create_payment`, also for
actions performed outside Today. A conflict requires reviewing the existing target; do not
retry with a fresh work identity or omit it, and an unavailable target never permits a
silent replacement. Shared calendar creation uses the owning Workspace calendar; personal
provider calendars stay independent. Personal item processing never completes shared work.
A shared payment additionally needs claimed responsibility; see
[payments.md](references/payments.md).
