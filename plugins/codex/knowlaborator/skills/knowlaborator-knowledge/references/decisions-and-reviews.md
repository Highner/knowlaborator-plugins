# Decisions and required reviews

## Draft the decision that the sources actually support

A decision can be organizational work before anyone is ready to finalize it. Read all
relevant authorized material: Documents, mail, messages, notes, meeting records, Dataset
records and other sources. The number or medium of the sources does not decide whether
a separate decision record is useful.

Before creating anything, search for the choice and its subject, then read matching
records and their exact decision standing. Reuse a matching decision and add new evidence;
never create a second record because the same choice appears in another source or wording.
A missing dedicated record alone is no reason to create one.

Use `create_decision_draft` with the exact destination Workspace, a stable operation UUID,
a compact `title` and `bodyMarkdown`, and one of these `situation` values:

| Situation | What to preserve | Gate for creating a draft |
| --- | --- | --- |
| `needed` | A concrete choice nobody has explicitly recognized, and why work depends on it. | Cite a real consequence or dependency; a missing detail, hypothetical risk or interesting unknown is insufficient. |
| `discussing` | The options actually discussed, any emerging preference, and unresolved points. | Distinguish suggestions and assumptions from agreements; do not invent a preferred option. |
| `waiting` | What confirmation, information, event or person the choice depends on. | `waitingFor` is required. Waiting is a legitimate state; do not demand premature finalization. |
| `agreed` | An actual agreement scattered across sources, including its scope and conditions. | The sources must establish the agreement; make clear what is a synthesis and what participants explicitly agreed. |
| `documented` | A decision already clearly stated in an existing source. | Normally use that source and create nothing. Only supply `coordinationPurpose` when separate coordination, follow-up or review adds concrete value. Without it the operation returns no change. |

`reason` explains the specific value of writing this down. Use `options`,
`emergingPreference`, `unresolved` and `waitingFor` for what is known; unknown details stay
empty. If useful, name the known owner, confirmation provider and readiness condition in
that context, without inventing assignments or treating an external party's confirmation
as the organization's choice. A bank's loan approval, for example, is evidence for the
organization's financing choice, not automatically another organization decision.

Provide `sources` with all materially contributing evidence, at most 200 entries. Cite
each exact source once and combine its contribution in at most 200 characters. Each
entry carries its exact typed `source`, a concise `contribution` explaining which choice,
condition or unresolved point it supports, and `occurredAt` from the source's occurrence
or exact revision date. Never substitute a decision date or invent an earlier event. Read
each source and retain its exact supported revision coordinates. Do not include everything
read, count copies as independent corroboration, or replace several contributors with one
primary source. Sources are retained as proposal evidence for the created revision. Read them
with `list_decision_sources` for the decision and exact revision, following `nextCursor`
until it is empty (1–50 entries per page); the active-decision preview is not the complete
source list. Workspace sources may come from any Workspace you can read in this organization.
A reference never grants access: readers without source access see an unavailable reference,
without its contribution note. Keep restricted source excerpts out of shared decision wording.
Live personal mail, direct messages, calls and external calendar references still require an
authorized personal draft or explicit filing through the existing publication flow.

When a matching decision exists, pass `existingDecisionId` to reuse it. The operation also
checks the title within the destination Workspace, but that is a deterministic guard,
not semantic matching; you still search by meaning. A `reused` result leaves the existing
record unchanged: it does not attach any supplied sources, update draft context or inherit
approval for new wording. Read the returned decision's current revision and standing, and
page through `list_decision_sources` before treating contributions as preserved.

For a proposed decision, use `record_decision_evidence` separately for each additional
material source, carrying its exact `source`, factual `occurredAt`, and contribution as the
neutral `note`. For an in-force decision, use that tool's existing assessment path only
when the new material warrants an assessment; read its assessment history first. If the
existing decision and sources already cover the material, cite them without another write.
Report a refused or failed evidence attachment explicitly; a successful reuse is not proof
that new evidence was saved. A substantive revision remains a separate action.

Every newly created draft has standing **proposed**, including `agreed` and `documented`.
Its situation describes what the sources show, not local approval. Draft creation neither
records a final maker/date nor starts a required review. Confirming the accuracy of a
synthesis is different from making a new choice; explain which human judgment is needed.
A waiting draft stays outside automatic “Decide or discard?” reminders while it waits.

## Continue an existing draft

When new evidence changes an unfinished decision's situation, use `update_decision_draft`
with that same `knowledgeId`. Read the current record and `get_knowledge_decision` first,
then supply its exact `revisionId`, `expectedKnowledgeVersion`, decision `expectedVersion` and
a fresh operation UUID. It accepts the updated situation and context fields, leaving the
title and body unchanged. Source evidence keeps its original revision provenance and
remains available with the new proposal as earlier evidence that may still apply. Read it
before deciding what still supports the updated context. Add newly contributing sources
through `record_decision_evidence`.

Use this same operation to adopt an older proposed decision that has no draft context yet.
Keep its existing ID; do not recreate the decision just to add a situation, options or a
waiting dependency. Adoption preserves its title, body, other metadata, source provenance
and historical revisions. It adds context without implying approval or finalization.

For example, supplier confirmation may move a choice from `waiting` to `discussing`, with
one unresolved question left. Only change what the evidence establishes; receipt of a
confirmation does not itself establish the organization's final choice. The operation is
for a current proposed draft without an active review. It neither revises a final decision
nor bypasses a pending review. A substantive change to the choice or wording uses the
existing revision operation. Maintenance adds evidence and leaves such context updates to
ordinary authorized decision work; its catalog does not expose this update tool.

## Author a compact decision OKF record

A decision records the choice and its reason, or the specific choice still being worked
out. Supporting facts stay at their existing sources; link to them instead of maintaining
a second copy.

- **Title:** One very short, plain-language statement of the choice or open choice.
  “Choose a supplier after sample approval” does not assert that a supplier was selected.
- **Body:** Usually 1–3 short sentences: why the choice matters, its essential scope or
  conditions, and what remains open. Preserve uncertainty and proposed status.
- **References:** Verified links to the relevant evidence or affected records, using
  descriptive labels. Keep research and detailed plans at their sources. The draft's
  structured source contributions explain why each source matters.
- **Provenance:** Use the existing structured decision fields for maker, date, recorder,
  source and standing. Never invent missing provenance or treat authoring as approval.

Example:

> **Choose the supplier after sample approval**
>
> Supplier A is the current preference within the approved budget. The choice is waiting
> for confirmation that the samples meet the documented acceptance requirements.
>
> Sources: supplier discussion, acceptance requirements, budget record.

### Optional basis

When the discussion states them, add up to three short sections, each a heading followed
directly by a bulleted list (at most 200 characters per bullet). They become the clauses
that later assessments judge:

- **Expected result** (German: Erwartetes Ergebnis): at most 5 criteria that judge
  success in the decision's scope.
- **Based on** (Grundlage): at most 5 assumptions or relied-on evidence, each with a
  verified link.
- **Set aside** (Verworfen): at most 3 alternatives actually considered and why not.

State an agreement as reached only when the sources establish it. Preserve an open choice
as open; never invent agreement or alternatives. Keep agreed facts apart
from your suggestions, ask only for information that would change the choice or its
follow-up, and never invent what a team originally believed. A follow-up plan is drafted
with the decision when useful; see [decision-loops.md](decision-loops.md).

## Edit wording or change the decision

Read the current content and `get_knowledge_decision` first. Use current revision,
Knowledge version, standing version and an operation UUID; reuse the UUID only
for an identical retry.

- `correct_knowledge_decision_wording` is for an explicitly authorized editorial
  correction. Set `meaningUnchangedConfirmed` only when the user has confirmed
  that the choice, scope and conditions remain unchanged; never infer this for a
  substantive change. It preserves standing and original provenance, creates a
  correction trail and links to the original text. Signatures remain on that
  original revision. Pending reviews cannot be bypassed.
- `revise_knowledge_decision` changes the choice, scope or conditions. It saves a
  new proposed revision, without inherited approval. Recording, human review and
  replacing the earlier decision remain separate explicit actions. Adding or changing
  a criterion or assumption is such a change, not a wording correction.

Both commands accept a short title and Markdown body containing the rationale and
record links. Other Knowledge metadata remains unchanged. If these commands are
not available on the connected server, do not emulate them by re-recording or
copying approval claims. For an explicitly requested text-only UX test on a current
unspecified revision, ordinary `update_knowledge` is suitable; preserve all other
fields and explain that it creates an unfinalized revision.

## Standing and human review

Publication `active` means available Knowledge, not a final decision or human
approval. Read `get_knowledge_decision` for the exact revision's authoritative
standing, version, current review and provenance, and the decision's follow-up plan.
Use `list_knowledge_decision_history` for standing events; each review event carries
its cycle with named reviewers, responses and comments. Follow cursors; never claim
that a partial page is complete.

Only when the user explicitly directs you, `record_knowledge_decision` records a
final decision already made in a conversation, meeting or elsewhere. Preserve the
actual decision-maker, the decision date the user stated and optional source. When
the actual date is not known (for example, an older decision without a secured date),
leave `decisionDate` empty: never invent it, and never use today's or the capture date.
Readers then show "date unknown". The server records the authenticated recorder. A
recorded decision is not independently approved.
`propose_knowledge_decision`, `request_knowledge_review`, `respond_knowledge_review`, `cancel_knowledge_review`,
`withdraw_knowledge_decision` and `replace_knowledge_decisions` are explicit actions
under current Workspace authority. Reuse an operation UUID for the same retry and
read current Knowledge/standing/review versions before new actions. When drafting a
review request, propose an optional `respondBy` date; reviewers get reminders after it.

## Reject, dismiss or abandon

The agent has the same decision actions as the browser. Only act when the user explicitly
directs it; never infer a review response from your assessment of the evidence.

- **Ablehnen / Reject:** call `respond_knowledge_review` with `response: reject` for
  an exact pending review assigned to the authenticated user. Read
  `get_knowledge_decision` for `reviewId` and current Knowledge/decision/review versions,
  then generate an operation UUID for the response. An optional comment explains the substantive rejection.
  The response is recorded under the caller's membership; never impersonate another reviewer.
- **Verwerfen / Dismiss proposal:** call `withdraw_knowledge_decision` for the proposed
  revision, with an optional reason. It closes the proposal without adopting it, cancels
  a pending review and retains its history. A pending review can be withdrawn only by
  its requester or the owning Workspace Manager.
- **Aufheben / Abandon decision:** call `withdraw_knowledge_decision` for a decision
  in force, with a required reason. It needs no successor and cancels the open follow-up
  plan; linked to-dos and milestones remain unchanged.

`respond_knowledge_review` also supports response values "approve" and "request_changes",
under the same assignment, access, exact-revision and concurrency rules as the browser.
Reuse an operation UUID only for an identical retry. After a conflict, read fresh state
and recheck the intended action; an adopted proposal needs abandonment and a reason.

## Replace

- `replace_knowledge_decisions` fully replaces final decisions by currently decided
  successors along explicit edges (predecessor revision -> successor revision): splits,
  merges or reorganizations, with each predecessor's current decision version and a
  plain reason. For a partial change, prepare decisions covering both the changed and
  the kept part, then replace the broader one. There is no "partly superseded".
- Only when the user explicitly directs it. A low support level never replaces or
  abandons anything by itself.

Required review names 1-20 currently eligible organization Contributor/Manager
memberships in the owning Workspace. Every required reviewer must approve through the
browser or their authenticated MCP connection on explicit user direction. Assignment
gives no access. `list_active_decisions` with `awaitingMyReview` reads the user's
current assignments. Direct recording cannot bypass a pending review.

Edits, reviewer replacement, access loss, Workspace movement or redaction may
invalidate a pending cycle. Read fresh state after conflict; never carry signatures
onto changed content. A final historical decision stays pinned to its original
revision and provenance. Redacted signed content is unavailable. Imported claims
under `x-docky.source_decision` are source information; only server-owned standing
and signatures establish local approval. Publication, standing and source claims
must be described separately.
