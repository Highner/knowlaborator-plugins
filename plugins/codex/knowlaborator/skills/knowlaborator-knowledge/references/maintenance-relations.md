# Relations and support

Read when a passage supports, contradicts or changes a relationship or an existing record. Use the leased submit/apply and checkpoint flow in [the maintenance protocol](maintenance.md).

- **Relations.** Use the relation types the Workspace lists. Each relation keeps the
  passage it rests on. Typed relation commands accept End entries with expected
  versions; end an old period and add the new one in one change. Dates are inclusive, so
  a successor normally starts the following day. Later recurring periods are distinct
  edges. Removal is a correction a person reviews, not a historical ending.
- **Documents, contacts, accounts and Dataset records as ends.** When the listed
  type's ends allow it, name such a record as `targetRecord` (`kind` `document`,
  `crm_account`, `crm_contact` or `dataset_record`, and its `id`) instead of
  `targetKnowledgeId`. Relate to the record itself, never to a knowledge record that
  only stands in for it.
- **Other ends that are no record yet.** Name the other end as `target` instead of
  `targetKnowledgeId`: a label with a person's email address or phone number, an
  organization's website, or a title with its concept type. The one record you can read
  with that key becomes the end, or, when the type ends at contacts or accounts, the one
  contact with that email or phone or account with that website; otherwise the relation
  waits, unseen, until such a record appears, and Knowledge → Wanted shows people the
  records many relations wait for. Never create the record yourself.
- **Relation types that do not exist yet.** When no listed type fits, add the relation
  under the name the source suggests, with a `definition` and an `inverseName`; when a
  listed type does not allow the two records' types, add it under that type. Either is
  kept as a candidate pair with its citation, and administrators turn candidates into
  types; the proposal's other changes apply.
- **Support.** When the source confirms what an existing record says, use `add_support`
  on that record with the claims and quotes that confirm it. Cite current source
  revisions only, and do not set `conflict`. Ordinary support and relation targets may live
  in another Workspace in the same organization when you can contribute to both source and
  target Workspaces. Keep the source in place; links grant no access. The source Workspace's
  maintenance policy still governs review. Never copy restricted excerpts into the target's text.
- **Contradiction.** When a newer source contradicts what a record says, use
  `add_support` on that record with the contradicted statements as `negated` claims,
  citing the passage; the source's own statement may follow as a further claim. Mark
  only original information that aged or was wrong (a person file says the daughter
  likes bears, a later mail says horses), never a record that already says the newer
  thing, and never a passing remark.
- Decisions, issues and saved emails, messages and notes take no `add_support`:
  decisions and issues use their own evidence flows, and a saved source is
  evidence itself.


## Concrete text corrections

When a newer source establishes a replacement for an existing ordinary Knowledge passage, prepare
`correct_knowledge_text` through `submit_maintenance_change`. First read the exact current record and
all relevant assertions. Reuse open contradiction claim IDs; if none exist, submit/apply the existing
negated `add_support` mark first. Do not duplicate a marker merely to attach a correction.

Supply `change.targetId` and `change.textCorrection` with `expectedRevisionId`, `expectedVersion` and
`edits`: exact UTF-16 `start`, `oldText`, concrete `newText`, zero-based `supportingClaimOrdinals` into
this submission's claims, and `resolvesContradictionClaimIds`. Every edit must fully address each named
contradiction, with exact current evidence. Use 1–8 ordered nonoverlapping edits, at most 4,000 characters
per old/new passage, 16,000 replacement characters total and 50 distinct contradiction IDs. Keep unrelated
corrections separately reviewable. Do not use the deprecated generic revision payload.

Corrections ALWAYS wait for human approval, including automatic mode and Process-marked work. Never
call ordinary authoring or switch plugins to bypass this review. Persist the correction before the page
checkpoint and report it as awaiting approval separately from an automatically applied marker. Rejecting
or deferring wording does not refute the contradiction. Only acceptance and successful saving resolve the
explicitly named markers; other markers remain. If the target or evidence changed, reread and prepare a
new reviewed version. Never pretend a stale correction was applied.

A source reference does not authorize publishing its protected content. Cross-Workspace replacement
text requires that the destination's readers can also read every supporting source. Otherwise keep it
unpublished and use an explicitly authorized source-filing flow. Decisions, Issues and original snapshots
retain their own write operations. This does not change Issue evidence boundaries.

For old settled source work with open contradiction markers, an explicitly requested recheck may use
`retry_maintenance_work` with its current fence/progress and a stable operation UUID, then claim and
review the new pass. Do not restart historical work implicitly or create a Process mark to bypass policy.
