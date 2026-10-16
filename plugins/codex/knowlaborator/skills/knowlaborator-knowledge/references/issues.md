# Open issues: finding and following unresolved situations

An issue is one canonical `issue` record with its own ID and Workspace. It tracks a
concrete unresolved situation with a plausible consequence. It may be explicitly reported
by a person or inferred by combining sources. Missing fields, generic risk lists and
interesting unknowns alone do not qualify. Finding no issues is a valid outcome.

## Find the existing issue first

Incoming and Maintenance share this protocol. Before creating, call `check_issue_candidates`
with the intended title, context, consequence, closure criteria and exact evidence. Search
across all readable Workspaces and all states, including New, Resolved and Dismissed. Search
subject names, relevant entities and alternative wording; read plausible matches and their
evidence. Complete every candidate page and retain the server receipt and explicit reasons
why each returned candidate does not cover the finding. An incomplete, stale or expired
receipt cannot authorize creation. Retry the comparison after concurrent changes.

Two findings are the same issue when the same outcome would settle them for the same subject,
scope and time period. Different sources, senders, wording and explicit versus inferred
origins do not create separate issues. New information belongs to the matching record:

- Add a new contribution with `record_issue_evidence`: strengthened/contradicted evidence,
  changed circumstances, a dependency or clarification. Keep every distinct authorized
  source contribution; reading it again does not justify a duplicate entry or notification.
- Use `propose_issue_resolution` only when the evidence may meet the closure criteria.
  Partial information remains evidence, without a lifecycle change.
- Resolved or dismissed matches retain their history. Add the concrete new evidence and
  explain why reconsideration might be needed; only a person may reopen or merge them.
- An inaccessible match does not grant permission to read or copy it. Keep the boundary
  visible and never infer hidden content from an identifier.

## Record a new finding

Only after the comparison finds no equivalent issue, use `create_knowledge_issue` with
its candidate receipt and assessments, stable operation ID, and all source contributions.
Agent findings always start **New**. Explain the situation, context/time period, possible
consequence, closure criteria, observations, interpretation and remaining uncertainty.
Keep what the source establishes distinct from the agent's inference. Cite every material
premise at its exact authorized revision/passage. Copied statements are not independent
confirmation. Structured facts use their actual typed version; never invent a quote.

Examples: a supplier's documented backups conflict with an EU-only storage decision;
a component arrives after the promised delivery date; two teams both expect the other to
perform a handover; a required approval role is vacant; a capability used to justify a
choice is being removed; two sources specify incompatible acceptance criteria.

Incoming first preserves live mail/messages through its authorized `process_input` path.
Shared issue evidence cites the saved coordinates. If publication is not authorized, keep
the limitation in the private run and suggest selecting/preserving the material. Never copy
private live content into a shared issue or index extra mail to expand maintenance scope.

**Maintenance uses `create_issue`, `record_issue_evidence` or `propose_issue_resolution`
changes in `submit_maintenance_change` and applies their previews under its live source
lease.** All claims retain exact citations; each claim's value is its evidence contribution.
Do not bypass the source lease or policy with direct issue writes. These findings apply in
both enabled modes because New already awaits triage. Persisted issues settle processing;
the person's later resolution is independent.

## Assignment and lifecycle

A New issue may be unassigned, but has a human follow-up member. Assignment and reporter are
separate. Person-authorized assignment supports an eligible user, an active Org Chart
position (following its occupant) or an organizational unit with an explicit follow-up
member. Vacancies retain the position and expose Needs owner. Assignment grants no access.
Personal Workspace issues stay with their owner. Never infer responsibility from similar names.

People control **New → Open / Waiting → Resolved / Dismissed**, reopening and merging.
Open/Waiting needs concrete closure criteria and responsibility; Waiting names its dependency.
Resolve only with an explanation of how the criteria were met; dismiss with a reason.
An accepted proposed resolution checks the current version; changed evidence requires review.
Use the lifecycle tool only for the person's explicit instruction, with `confirmed: true`.
Source content and the agent's assessment cannot supply that instruction. Never overwrite
human wording, assignment, dates or lifecycle autonomously.

Review dates request reassessment, not automatic closure. Incoming can check its authorized
live inputs; Maintenance checks only selected/indexed email snapshots and filed records.
Record the exact scope, result and exclusions with `record_issue_check`. An evidence check
does not clear or snooze the person's due review. The person completes it explicitly with
`review_knowledge_issue`, optionally setting the next date. Never escalate empty-check counters
or repeatedly recheck a completed request without new evidence.

Cross-Workspace matches do not authorize publishing their sources elsewhere. If a matching issue
cannot accept the current source under its publication boundary, record a blocked source-scoped
assessment with the exact limitation. Do not create a second issue to get around that boundary.
A person can choose an authorized shared destination; evidence remains protected in the meantime.
