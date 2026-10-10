# Decisions and required reviews

## Author a compact decision OKF record

A decision records the choice and its reason. Supporting facts stay in the existing
OKF records; link to them instead of maintaining a second copy.

- **Title:** One very short, plain-language statement of the chosen action, for
  example, "Use one shared customer directory".
- **Body:** Usually 1–3 short sentences: why this choice, plus only the conditions
  or scope needed to understand it. Do not restate the title or summarize each
  source. Preserve uncertainty and proposed status.
- **References:** A few verified links to the relevant evidence or affected OKF
  records, using descriptive labels. Read the records before citing them. Keep
  research, alternatives, detailed plans and implementation instructions in those
  records. Do not add a mini-summary to every link.
- **Provenance:** Use the existing structured decision fields for maker, date,
  recorder, source and standing. Do not repeat those fields in the body or invent
  missing provenance; an unknown decision date stays empty. Authoring text alone never
  establishes approval.

Example:

> **Use one shared customer directory**
>
> One directory avoids duplicate customer maintenance. Each team keeps its own
> access permissions.
>
> References: Customer data policy, Access requirements (each a link to the
> verified record).

### Optional basis

When the discussion states them, add up to three short sections, each a heading followed
directly by a bulleted list (at most 200 characters per bullet). They become the clauses
that later assessments judge:

- **Expected result** (German: Erwartetes Ergebnis): at most 5 criteria that judge
  success in the decision's scope.
- **Based on** (Grundlage): at most 5 assumptions or relied-on evidence, each with a
  verified link.
- **Set aside** (Verworfen): at most 3 alternatives actually considered and why not.

State the choice actually reached; never turn it into a poll. Keep agreed facts apart
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
standing, version, current review and provenance. Use
`list_knowledge_decision_history` for standing events and `list_knowledge_reviews`
for bounded historical cycles, named reviewers, responses and comments. Follow
cursors; never claim that a partial page is complete.

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
gives no access. `list_outstanding_knowledge_reviews` reads
the user's current assignments. Direct recording cannot bypass a pending review.

Edits, reviewer replacement, access loss, Workspace movement or redaction may
invalidate a pending cycle. Read fresh state after conflict; never carry signatures
onto changed content. A final historical decision stays pinned to its original
revision and provenance. Redacted signed content is unavailable. Imported claims
under `x-docky.source_decision` are source information; only server-owned standing
and signatures establish local approval. Publication, standing and source claims
must be described separately.
