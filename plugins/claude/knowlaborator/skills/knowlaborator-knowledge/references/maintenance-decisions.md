# Decision evidence and drafts

Read when a passage changes an existing decision’s evidence or establishes a useful choice to coordinate. Use the leased submit/apply and checkpoint flow in [the maintenance protocol](maintenance.md).

- A decision takes evidence through `record_decision_evidence` with its exact
  `revisionId`. Its standing decides the kind; parameters of the other kind are refused.
  Read `list_decision_sources` for the exact revision and follow every `nextCursor`;
  each page has 1–50 entries. The active-decision preview does not show every source.
- A proposed decision revision: one exact `source`, when it happened (`occurredAt`)
  and a neutral `note` of at most 200 characters, without a level. At most 200 pending
  source entries may accumulate; a further new source is refused without writing. Do not
  drop relevant evidence merely to get under the limit; report the blocked attachment.
- An in-force decision: read `get_decision_assessments`, then record an assessment
  with labels only, its clause impacts and the assessment version. Weigh correlated
  reports together; a live personal source makes a private draft only you see. Workspace sources may cross readable Workspaces, with notes and derived results protected for each reader.
- **Decisions from any relevant data.** Read the contributing Documents, mail, messages,
  notes, meeting records, Dataset records or other authorized sources. Search for the
  concrete choice and read matching decisions first; use the existing decision and add
  evidence when it matches. Source count alone never justifies a new record.
- **Five situations.** Use `situation` to distinguish:
  - `needed`: a concrete unrecognized choice and why work depends on it. A real
    consequence or dependency is required; missing details or speculative risks are not.
  - `discussing`: the options people discussed, any emerging preference and what remains
    unresolved. Do not invent an agreement or alternatives.
  - `waiting`: the choice depends on confirmation, information, an event or a person.
    State that dependency in required `waitingFor`. Waiting is legitimate, not a demand
    to finalize; these drafts get no automatic decide-or-discard reminder.
  - `agreed`: the sources establish an agreement that needs a coherent written account.
    Preserve scope and conditions; distinguish participants' words from your synthesis.
  - `documented`: a source already clearly records the decision. Use that source and
    create nothing unless a separate record adds concrete coordination or follow-up.
    Name that value in `coordinationPurpose`; without it, record a no-change page
    assessment instead of submitting a draft.
- **Prepare a draft.** Submit a `create_decision_draft` change through
  `submit_maintenance_change`, then apply the preview. Use its `decisionDraft` payload
  with a short `title`, `bodyMarkdown`, `situation`, `reason` (why writing this down helps),
  `options`, `emergingPreference`, `unresolved`, `waitingFor` and `coordinationPurpose`
  as applicable.
  Unknown facts stay unknown. Name an owner or a person providing confirmation only
  when the evidence establishes them. A decision by an outside party, such as a bank
  approving a loan, is normally evidence for the organization's choice or a relation.
- **Keep every contributor.** Cite contributing filed Knowledge and Document passages
  in the proposal's `claims`, with exact revisions, quotes and coordinates; include the
  maintained source. Each claim states what its evidence contributes. For other relevant
  data, or additional exact references, use `decisionDraft.sources`: each entry gives its
  typed `source`, `contribution` (at most 200 characters) and factual `occurredAt` from the
  source or its exact revision. Supported kinds include Dataset records, messages, tasks,
  projects, calls, mail and calendar events as well as Knowledge and Documents. Read each
  source first; unknown occurrence times are not a reason to invent a decision date.
  The adapter combines these with every distinct source from the claims, preserving up
  to 200 exact source references in proposal evidence. The existing claim bounds stay:
  50 claims, 100 citations total, 20 citations per claim. Exact passage quotes remain in
  the claims. Exclude unrelated reading; copied statements are not independent evidence.
  Workspace sources may cross readable Workspaces in this organization; references grant
  no access. Their contribution notes follow each reader's source permissions. Keep restricted
  excerpts out of shared draft wording. Live personal correspondence still requires an
  authorized personal Workspace or explicit filing through its existing publication flow.
- Pass `existingDecisionId` when reusing a matching decision. The server also guards
  exact titles within the Workspace; it does not compare meaning, so semantic matching
  remains your job. Reuse leaves the existing record unchanged and attaches none of the
  submitted sources, including when the maintenance proposal becomes applied. Read
  the result's current revision and standing, and page through `list_decision_sources`.
  For a proposed decision, preserve every additional material source separately with
  `record_decision_evidence`, its exact source, factual occurrence time and neutral
  contribution note. For an in-force decision, follow the assessment path above when
  the new material warrants it; otherwise cite the existing decision and sources.
  Verify the evidence writes before reporting sources preserved. Report failed or
  refused attachments in the page assessment and run report; applying a reused draft
  alone does not save new evidence. Every new draft is proposed,
  even if its situation is agreed or documented. It records no final maker/date or human
  approval. An existing decision's substantive revision is outside maintenance.
- New decision reports are no longer submitted. Existing reports and their pending
  proposals remain readable and resolvable through the normal legacy workflow.
