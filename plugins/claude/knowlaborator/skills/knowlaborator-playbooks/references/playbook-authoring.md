# Author or update a Playbook

Keep Playbooks free of credentials and real customer payloads. Never store
organization Playbook content in plugin files. Use `search_content` and
`get_knowledge` only to resolve deliberately selected knowledge references.

For an existing Playbook, call `get_playbook_for_editing` and use every returned
editable field plus its opaque `editToken`. Do not construct a partial baseline
from discovery results.

## Shape the Playbook

- `name`: a unique one-line name, at most 200 characters.
- `description`: required, at most 500 characters.
- `triggerPhrases`: 1–20 unique one-line operational signals, each at most 200
  characters and free of secrets or copied customer data.
- `appliesWhen`: required, at most 1,000 characters; state testable entry
  conditions.
- `doesNotApplyWhen`: optional, at most 1,000 characters; state exclusions and
  handoffs.
- `tags`: at most 20 unique one-line values.
- `casePolicy`: `none`, `optional`, or `required`. Explain the operational
  consequence in the proposal.
- `workspaceId`: the exact owning Workspace. It is required for `optional` and
  `required`, and for every Playbook with one or more template references,
  including when `casePolicy` is `none`. A Playbook with existing Cases must
  remain connected. Preserve it unless the administrator intends to move every
  existing Case and Case-owned ToDo, immediately changing their access
  boundary. The move is atomic and is rejected when any linked ToDo assignee
  lacks access to the destination Workspace or any selected template does not
  belong to that exact destination.
- `knowledgeRecordIds`: at most 20 deliberately selected active shared records
  from the active organization. Never invent or infer an inaccessible ID.
- `templateReferences`: at most 20 ordered entries. Each entry contains one
  distinct exact `templateDocumentId`, one exact role, and trimmed non-empty
  `guidance` of at most 1,000 characters:
  - `required_output`: create or update the described document during Playbook
    execution.
  - `optional_output`: create or update it only when Playbook conditions and
    user intent call for it.
  - `reference`: consult it as an example, with no output obligation.
  Every target must be an active, non-archived logical Document with
  `purpose: template`, a trimmed general Document `description` of 1–500
  characters, a current successfully processed version, and the exact same
  Organization and Workspace as the Playbook. The Document description explains
  what the reusable example is and remains separate from this reference's
  Playbook-specific `guidance`. The authoring admin must
  have ordinary data-plane read access to that Workspace; admin control-plane
  authority, a title, metadata, or a link grants no access. Preserve array order
  as the stable sort order and never encode the IDs or roles only in Markdown.
- `instructionsMarkdown`: required, at most 32 KiB as UTF-8 bytes.

Write `instructionsMarkdown` last, following the "Write the instructions"
section below, and preserve ordinary permission and confirmation boundaries.

## Design the operational capability path

Before drafting instructions, map only the capabilities the procedure actually
needs. State decision criteria and intended outcomes rather than copying tool
names or making every possible side effect part of the Playbook.

- **Sources and inputs:** distinguish ordinary Documents and Templates;
  connected-mail messages, threads, and attachments; Dataset records; OKF
  knowledge; calendar events; internal conversations, notices, and ToDos; CRM
  Accounts, contacts, interactions, and resources; and approved Components.
- **Operational effects:** identify whether execution may create or update a
  Document, query or maintain Dataset records, preserve mail or create a mail
  draft, schedule calendar work, send an internal message, publish a notice,
  maintain a ToDo, invoke an approved Component, or maintain CRM data. Route
  the selected effect through its owning installed skill at execution time and
  preserve that skill's authorization, confirmation, and validation rules.
- **Durability and provenance:** distinguish a live authorized read from
  preservation. For mail, separately decide whether to read provider content,
  ingest one exact immutable `EmailMessage` source snapshot, import selected or
  supported attachments as ordinary Documents, derive reusable OKF knowledge,
  or associate the provider message with a persisted Case. Ingestion does not
  itself create a Case link. Case-aware draft creation accepts the persisted
  Case ID and links a draft produced by that Case's Playbook execution. This is
  a centralized runtime rule; do not duplicate the linking mechanic in each
  Playbook. Drafts outside persisted Case work remain unlinked.
- **Prerequisites:** identify required Workspace access, connected accounts,
  ready Templates, an existing Active Dataset and compatible schema, approved
  Components, or other setup. Treat Dataset creation or schema revision as an
  explicit governance prerequisite owned by Datasets, never as an
  incidental runtime step.
- **Bindings:** `knowledgeRecordIds` and ordered `templateReferences` are the
  Playbook's structured resource bindings. Dataset, mail, calendar, messaging,
  notice, ToDo, Component, and CRM requirements in Markdown are selection
  guidance only; they grant no access and must be rediscovered and reauthorized
  at execution time. If a procedure requires a stable binding the Playbook
  model does not provide, disclose that limitation or make a product change a
  prerequisite instead of implying the binding exists.

Keep tool-level mechanics in the owning skills so the Playbook remains concise
and does not duplicate a changing tool catalog.

## Choose Case persistence

Choose `casePolicy` from the intrinsic tracking needs of a normal execution,
not from urgency, importance, an escalation label, or the wording of one
request. State the rationale and operational consequence in the proposal.

- Use `required` when every valid execution needs a durable operational identity
  before work begins, such as mandatory multi-turn or multi-person coordination,
  assignment or Case-owned ToDos, immutable progress history, later resumption
  or reopening, or the Case as the required anchor for linked subjects, inputs,
  evidence, outputs, related knowledge, or provider-email references. Execution
  must resume a matching Case or create one before operational work and needs no
  separate persistence confirmation.
- Use `optional` when a complete one-shot execution is valid but some runs merit
  tracking. At execution time, start a Case only when the user requests it, the
  work spans turns or sessions, or it needs durable history, reopening, or OKF
  relationships.
- Use `none` when the procedure is intentionally transient and neither its
  correct execution nor its outputs need Case identity or Case history. A Case
  must never be started for that Playbook.

Search for an existing matching Case before creating a new one. `casePolicy` is
the sole Case-persistence decision at execution time; Playbook instructions
must not contradict or override it.

## Choose durable inputs and outputs

Apply [Work's durable-form rules](knowlaborator-skill://knowlaborator-work/references/durable-form.md).
For template selection, use `list_documents` with `purpose=template` and the exact
Workspace when the current user can read it through the active Knowlaborator organization.
Otherwise ask for an exact authorized template selection; never infer an ID
from a title or silently widen scope.

A Playbook has no structured Dataset-reference field. Describe record shape
and selection criteria, not a purported binding or consent to query or write.
Schema design is an explicit prerequisite, not incidental execution.

## Write the instructions

The primary reader of `instructionsMarkdown` is an executing agent that loads
the entire text into an already busy context. Every line competes with live
task context, so shortness is a correctness feature, not a style preference.
Write the shortest Playbook a competent executor can follow — target roughly
one screen (30–60 lines). The 32 KiB limit is a ceiling, never a budget.

Structure:

- Use this skeleton and omit any section with nothing to say: `Purpose` (one
  line), `Prerequisites`, `Steps`, `Outputs`, `Escalation`, `Done when`.
- Numbered steps for sequence, bullets for unordered facts. One action per
  step, imperative mood, ideally one line.
- Prose paragraphs only for a concept that genuinely cannot be a list, never
  as narrative between steps.

Decision points:

- Encode branching as conditional bullets inline, under the step where the
  decision occurs:
  1. Check the notice against current contract terms.
     - If it violates agreed terms → step 4.
     - Else → continue.
- Conditions must be testable from information the executor will have.
- Nest at most two levels. A deeper tree means the branch is its own step or
  its own Playbook; name the handoff explicitly.
- When the procedure is classification-first (triage), the whole Steps
  section may be one decision tree of conditional bullets.
- Never use ASCII-art or diagram syntax for branching; conditional bullets
  stay editable, diffable, and unambiguous for agents and people. Use a small
  table only for a genuine mapping (for example, request type → template).

Cut before saving — delete anything that:

- repeats `name`, `description`, `appliesWhen`, `doesNotApplyWhen`, or
  trigger phrases; the structured fields already carry them;
- restates platform behavior the executing skills already enforce
  (authorization, confirmation, `casePolicy` semantics, tool mechanics);
- explains background or rationale — move durable background to knowledge
  records and bind them via `knowledgeRecordIds`;
- describes what a template is — that belongs in the template Document's
  description and the reference's Playbook-specific `guidance`;
- greets, frames, or restates the obvious ("This Playbook helps you…").

When updating an existing Playbook, shorten while you edit: merge changes
into existing steps instead of appending, and re-apply the cut list to the
whole text.

## Review and save

Present every field before create or a complete field-by-field diff before
update, including the ordered template roles and guidance, the live Workspace
connection, and its impact on existing Cases. Flag instructions that exceed
the one-screen target and justify the length in the proposal. A current
instruction to save the reviewed proposal authorizes the mutation; otherwise
ask after the review.

`create_playbook` always creates `Draft`; activation is separate. Use
`update_playbook` with every editable field, including the complete ordered
template-reference collection, and the exact opaque `editToken`.
Update preserves `Draft` or `Active` status. An Active update takes effect
immediately for all existing and future Cases, including its template
references. There are no revisions, historical versions, pinned versions,
template copies, or Case snapshots. Retired Playbooks are read-only and retain
their template references.

Generate one fresh idempotency key and reuse it only for an identical uncertain
retry; the request identity includes the ordered template references. On
validation failure, correct reported fields without discarding the proposal.
Duplicate, missing, ordinary-Document, archived, unready, cross-Organization,
cross-Workspace, or caller-inaccessible template targets are invalid and must
not be replaced by guessed IDs. On `PLAYBOOK_EDIT_CONFLICT`, reload, compare
changed fields, reconcile explicitly, and retry with the new token. Never ask
the user for a token. Report the stable Playbook ID and lifecycle status, never
the edit token.
