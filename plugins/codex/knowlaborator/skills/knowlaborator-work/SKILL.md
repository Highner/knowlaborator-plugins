---
name: knowlaborator-work
description: Choose a durable destination for organization information, resolve Workspace context, coordinate broad research, or close an agenda item after fulfilled user-authorized work.
---

# Knowlaborator Work

Use the functional skill for the requested operation. Work owns decisions that
cross functions and closing an agenda item after authorized work from it;
it does not change the invoked binding.

Read only what the request needs:

- [workspaces.md](references/workspaces.md) for Workspace discovery and selection.
- [durable-form.md](references/durable-form.md) when saving information without
  an explicit destination, or designing a procedure's durable inputs and outputs.
- [delegated-discovery.md](references/delegated-discovery.md) for a broad
  read-only research pass when the client supports isolated subagents.
- [file-inspection.md](references/file-inspection.md) when staging and inspecting
  an original file returned by any exact-file tool.

A read or ordinary operational request does not authorize ambient knowledge
capture. For explicit research, analysis, brainstorming, decision support,
save, or import, use Knowledge's task-scoped capture guidance only when writes
are requested or authorized by that workflow and available on this binding.

## Optional module availability

Org Chart (`org-chart`) and Projects (`projects`) are independent optional
modules. When their availability matters, use `list_organization_modules` for
the active organization's current state and discover the live tools and owning
functional skill. Availability flags never grant record access. Use exact
feature-authorized records; reporting relationships grant no Workspace or
approval authority.

A deactivated module retains authorized read access and history while its
mutations are paused. Continue permitted work on linked core ToDos, Documents,
Knowledge and decisions through their own operations. Core work does not require
installing a module. On `MODULE_NOT_INSTALLED` or `MODULE_DEACTIVATED`, refresh
availability and explain what remains possible. Administrator lifecycle changes
belong in **Organization Settings -> Modules**; never bypass the state through
another organization, endpoint or copied tool list. Deprecated Cases are not
project entities.

## Work from an agenda item

A selected agenda item is context, not authorization to execute source or domain
actions. Items reach the basket from the OrgApp browser or the Today desk alike.
Resolve the exact item with `get_active_context`, retain its item ID, and read its
MembershipId and current Version. Obtain the user's actual work request and use the
owning functional skill. Recheck source access and current source facts before
acting; advisory links never grant access.

For a user-authorized ToDo handoff, use `create_todo` or `update_todo` with optional
`agendaItem` as described by
[Notices and ToDos](knowlaborator-skill://knowlaborator-notices-and-todos/references/notices-and-todos.md).
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
them. Use My Day for that preparation; use the owning domain skill for authorized
execution.

## Adding agenda findings

When the user asks to add or change agenda items, read `get_agenda_context` and follow
[My Day](knowlaborator-skill://knowlaborator-my-day/SKILL.md): update the open item that
already covers an issue with its current Version, add only genuinely new issues, and keep
every text very short. Adding an item is proposal-only and never authorizes or executes
its work.

## Shared action execution

Read get_shared_work for every agenda WorkReferences entry before acting. Use the exact
SharedWorkId on create_todo, create_calendar_event or create_payment, including actions
performed outside Today. Conflicts require reviewing the existing target; do not retry
with a fresh work identity or omit it. Unavailable targets never permit silent replacement.
Shared calendar creation uses the owning Workspace calendar; personal provider calendars
remain independent. Personal item processing is separate from domain completion.

Before a shared payment handoff, obtain user authorization and claim_payment responsibility
using the current version and one retry OperationId. Another member's claim blocks the
attempt. Record awaiting_confirmation after an external attempt; claims never expire.
Release only on explicit confirmation that no transfer occurred. Reconcile an uncertain
attempt before another payment. A QR or saved PaymentId never proves a completed transfer.
