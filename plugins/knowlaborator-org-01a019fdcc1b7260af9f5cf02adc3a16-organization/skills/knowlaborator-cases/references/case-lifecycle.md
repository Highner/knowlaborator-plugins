# Case lifecycle and updates

For a Playbook-backed Case, proceed only after the complete current Playbook
read this skill requires: the full `get_playbook` response for a newly selected
Playbook, or `get_case` with the entire non-null `currentPlaybook` when
resuming. A standalone Case has a null `currentPlaybook`; follow the user's
requested work and ordinary authorization boundaries without inventing
Playbook instructions or policy.

For a Playbook-backed Case, follow the current `casePolicy`:

- For `required`, use `list_cases` to resume a matching Case or call
  `start_case` before any operational work. Starting it is part of the
  authorized task and requires no separate persistence confirmation.
- For `optional`, start a Case only when the user asks to track the work, it
  spans turns or sessions, needs durable history or reopening, or requires
  durable OKF relationships.
- For `none`, never start a Case; apply the Playbook without one.

If a selected Playbook's `casePolicy` is absent or unrecognized, report the invalid Playbook and do
not begin operational work.

`start_case` takes either the exact `playbookId` for Playbook-backed work or an
exact `workspaceId` with no `playbookId` for standalone tracked work. A
Playbook-backed Case inherits the Playbook's current Workspace; a standalone
Case requires Contributor access to the supplied Workspace. The Workspace
remains the authorization boundary: link only active knowledge the caller is
authorized to use in that Workspace context.

When starting a Case, supply a concise ordered `nextSteps` list as soon as the
work is understood. Each item describes a high-level next move that helps a
reader understand where the Case stands. It is not a ToDo: do not include
assignees, due dates, reminders, or completion state. Use an empty list only
when the next move genuinely cannot yet be determined.

Use a concise title and curated summary; never copy raw content or hidden
reasoning. Link only relevant knowledge as `subject`, `input`, `evidence`,
`output`, or `related`. Use one fresh idempotency key and reuse it only for an
identical uncertain retry.

When the user requests identified Cases, carry out every selected Case and
execute its Playbook when present; do not stop after `start_case`. When all applicable steps succeed, call
`complete_case`. When confirmation, user input, missing authority or access, an
unavailable dependency after reasonable retries, or a future external event
blocks further work, add a concise Case update describing the blocker, leave
the Case active, and report what can unblock it. Use `cancel_case` or
`reopen_case` only for the requested lifecycle transition.

After meaningful progress, a blocker, completion, cancellation, or reopening,
call `update_case_steps` with the complete current lists. Move only concise
high-level milestones that have actually passed into `pastSteps`; keep only
remaining or newly discovered moves in `nextSteps`. Past steps are not a copy
of the immutable update history. A completed or cancelled Case normally has no
next steps unless a concrete follow-up remains relevant. Copy the latest
`caseVersion` automatically, including after any preceding Case mutation.

Inspect Case work with `list_todos` and the exact `workflowCaseId`. Add work
with `create_todo`, that Case ID, and its current Workspace ID; apply the ToDo
rules owned by `$knowlaborator-notices-and-todos`. `update_todo` cannot change
Case ownership; never recreate a ToDo to simulate a move. A Playbook Workspace
change atomically moves its Cases and Case-owned ToDos and is rejected when an
assignee lacks destination access. Case Workspace is read-only.

When the current Playbook instructs you to create a provider draft as part of
persisted Case work, pass the exact current `caseId` to `create_mail_draft` so
the same idempotent operation links the resulting draft. Omit it for unrelated
drafts and whenever no Case was persisted; do not add a separate linking step.

For later work, get_case returns ordered updates, caseVersion, nextSteps and
pastSteps, plus currentPlaybook when present. Read that complete current
Playbook, including when Retired, before continuing. Apply the template-role
rules in [Playbooks' execution guidance](knowlaborator-skill://knowlaborator-playbooks/references/playbook-discovery.md).
A standalone Case has no Playbook. Cases store no template body or version
snapshot and create no Case-output-Document link.

Copy `caseVersion` automatically as `expectedCaseVersion` for
`add_case_update`, `update_case_steps`, `complete_case`, `cancel_case`, or
`reopen_case`.
Never display it or ask the user for it. On `CASE_VERSION_CONFLICT`, reload the
Case,
reconcile intervening updates and current template references, and retry with
the current value and a fresh idempotency key; never overwrite or create a
duplicate.
