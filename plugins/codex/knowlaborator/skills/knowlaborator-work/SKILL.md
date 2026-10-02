---
name: knowlaborator-work
description: Choose a durable destination for organization information, resolve Workspace context, coordinate broad research, or record the outcome of fulfilled user-authorized Daily Brief work.
---

# Knowlaborator Work

Use the functional skill for the requested operation. Work owns decisions that
cross functions and the final outcome of authorized work from a Daily Brief item;
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

## Work from a Daily Brief item

A selected recommendation is context, not authorization to execute source or domain
actions. Resolve the exact item with `get_active_context`, retain its item ID and
saved RevisionId, and read its MembershipId and current Processing state/Version. Obtain the
user's actual work request and use the owning functional skill. Recheck source
access and current source facts before acting; advisory links never grant access.

For a user-authorized ToDo handoff, use `create_todo` or `update_todo` with optional
`dailyBriefItem` as described by
[Notices and ToDos](knowlaborator-skill://knowlaborator-notices-and-todos/references/notices-and-todos.md).
This atomically saves or links the task and records the item's processed outcome;
do not separately mark it processed after a successful handoff.

After successfully fulfilling other user-authorized work that originated from this item,
call `mark_daily_brief_item_processed` as the final step. Supply the exact item and
RevisionId, ExpectedMembershipId from the returned MembershipId, ExpectedVersion
from Processing.Version,
a fresh OperationId UUID, and an Outcome of at most 1,000 plain-text characters
describing what actually happened, for example
"Reviewed the offer and drafted the reply." OrgApp attributes the actor, transport,
and timestamp; never invent them or save processing state inside generated Items.
Reading an item, adding it to the basket, discussing possibilities, creating an
unfinished plan, or failing the requested work does not count as processed.
An investigated and concluded issue, deliberate user-authorized decision that no
work is needed, or accepted ToDo handoff can be processed; completion of a linked
ToDo is separate. Do not record a promise as a completed outcome.

Reuse the same operation ID and identical request on a lost response. A stale
revision or processing version requires a fresh exact read and confirmation of the
same issue; never guess, silently substitute a refreshed item, or overwrite a newer
outcome. A deliberately reopened item needs new user-authorized completion; an old
retry must not process it again. Report failed outcome recording honestly even when
the underlying work succeeded.

`reopen_daily_brief_item` requires an explicit request to reopen this item's state,
its exact RevisionId, ExpectedMembershipId, ExpectedVersion, and a fresh OperationId.
Reopening retains the task link and never changes the task. Processing an item never completes
or reopens a linked task. Browser Create ToDo opens an editable draft: opening or
cancelling creates nothing. A successful explicit Save atomically records "ToDo
created" and links the Open task. Merely mentioning ExistingTodoId in a brief does
not accept a handoff. An unavailable task never authorizes a silent replacement.

Daily Brief preparation remains proposal-only: generating or saving a brief does
not execute suggestions, mark items processed, or reopen them. Use My Day only for
that preparation workflow; use the owning domain skill for authorized execution.
