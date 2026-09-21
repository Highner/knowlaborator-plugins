# Activate, retire, or delete a Playbook

## Activate

Reload the Playbook when its editing baseline may be stale. Review its purpose,
triggers, applicability, instruction path and escalation, `casePolicy`, owning
Workspace, selected knowledge references, ordered template references and
roles, validation state, and `Draft` status.

A current instruction to activate that named reviewed Playbook authorizes
`activate_playbook` without another confirmation. Copy the opaque `editToken`
automatically and use a fresh idempotency key. Activation is only
`Draft` to `Active`. On validation, reference, or edit conflict, leave the
Playbook as Draft, reload it, and explain what requires reconciliation.

## Retire or permanently delete

Reload the exact Playbook and present its stable ID, name, current lifecycle
status, and effect.

- `retire_playbook` is available through MCP only for `Active` Playbooks that
  must stop discovery and new Case creation. Retired content remains readable
  through existing Cases, including the retained ordered template references,
  and those Cases continue using the current content. Retirement is terminal:
  a Retired Playbook cannot be edited or reactivated.
- Permanent deletion is available only in the Playbook management web UI. There
  is no MCP deletion tool. The browser permits it only when no Cases reference
  the Playbook and removes its owned knowledge and template-reference links and
  traces under the existing erasure rules. Deleting the link rows never deletes
  their logical template Documents.

Obtain current explicit confirmation immediately before either destructive
action. Copy the edit token automatically; never expose or request it. Preserve
one idempotency key only for an identical retirement retry. If the user requests
permanent deletion through an agent, direct them to the web management UI; do
not substitute retirement without their instruction. Because permanent browser
deletion leaves no replay trace, an uncertain result must be checked in the UI
rather than retried automatically. Report the stable Playbook ID and Retired
status after retirement.
