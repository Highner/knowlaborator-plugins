# Discover and apply a Playbook

For ordinary work, call get_playbook_catalog once when the conversation first
reads mail (including through get_today) or asks for recommendations or next
steps. Retain it for the organization; refresh only on an explicit request or
organization change. Triage owns its own summary-and-proposal flow.

Match locally using triggerPhrases, appliesWhen, doesNotApplyWhen, description
and tags. Keep each mail candidate paired with its source item; do not send
mail bodies through search_playbooks. For a separate explicit process search,
use one short non-sensitive search_playbooks query and get_playbook_summary
only to distinguish plausible candidates. Do not prefetch weak matches, linked
knowledge or template bytes.

Before advising on or executing a selected Playbook, call get_playbook and read its complete
current instructionsMarkdown, casePolicy and ordered templateReferences.
Discovery metadata is not instructions. A surface without get_playbook may
report a candidate but cannot execute it; do not switch bindings silently.

For advice, name the applicable Playbook, outline its process and material
outputs or tracking consequence, and offer execution. Do not look up or mutate
Cases or perform operational work until the user accepts or has already asked
for execution. If none applies, say so and stay within the request.

## Case policy during authorized execution

casePolicy is the sole Playbook-driven persistence decision, never urgency,
importance, an escalation label or the Playbook's name:

- required: resume a matching Case or create one before operational work;
  no separate persistence confirmation is needed.
- optional: track only on user request or when work spans turns or sessions,
  needs durable history, reopening or OKF relationships.
- none: execute without creating a Case.

Stop on an absent or unrecognized policy. Use the Cases skill for execution;
read-only surfaces cannot start it. The policy does not grant tool permission.

## Templates

Interpret the ordered references with the instructions:

- required_output: create or update the described ordinary Document.
- optional_output: do so only when the procedure's conditions and user intent call for it.
- reference: consult the example without an output obligation.

The logical Template description says what it is; reference guidance says how
this Playbook uses it. Read the exact ready current version only when needed,
using Documents' reading guidance and the shared MCP file contract. Create the
result locally and save it with purpose=document through Documents; OrgApp
neither generates the result nor creates a Case-output-Document link.
Templates and Playbooks share a Workspace, but the reference grants no access.
Active edits apply immediately; there are no pinned versions or Case snapshots.
