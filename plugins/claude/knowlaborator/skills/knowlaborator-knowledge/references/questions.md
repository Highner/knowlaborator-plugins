# Independent Knowledge questions

An actionable open question is one OKF concept of type `question`, with its own ID,
owner, scope, expected evidence, optional next check date and lifecycle. It is never
a decision, decision plan, assessment or score. Use question tools and question
coverage only; completing decision catch-up does not review questions.

## Author and link

Before creating a question, search for existing questions with the same meaning and
scope. Use `create_knowledge_question` with a stable operation UUID, the organization's
primary OKF language, a responsible member (who is asked) and originating
`relatedKnowledgeIds`. Set `askedBy` only when the person or the source names who
asked: `{ "kind": "member", "id": membershipId }` or `{ "kind": "contact", "id":
contactId }` for a Contacts contact. Never infer an asker from who created a record.
Then replace the embedded question in each originating concept with an ordinary
`knowledge://objects/UUID` link using its current version. Create first, replace
second, verify last. Preserve unrelated facts and source quotations. Several
concepts can link to one question; do not keep a second copy of its answer/state.
Use `revise_knowledge_question` for wording, scope, expected evidence, owner, asker or
date; send the existing `askedBy` and links again, because omitting them clears them.

## Review evidence

During an authorized ingestion or question-review session, page through
`list_knowledge_questions` in the relevant Workspace, including resolved questions
when checking for contradictions (`status` narrows the page to one status). For each, call `get_question_catch_up`. It includes
existing source revisions and new material, with up to 50 sources per page.

- Read exact source versions through their owning tools. Search can help prioritize,
  but never acknowledge an unread source as reviewed or irrelevant.
- Compare what the question asks, its scope and expected evidence with what the
  source actually establishes. Partial answers, plans and guesses remain uncertain.
- Save findings with `propose_question_answer`, including precise conditions and
  exact evidence. This leaves the question `answer_proposed`.
- `resolve_knowledge_question` requires the person's explicit instruction to accept
  the answer. Update dependent concept facts when authorized and appropriate. Never
  manufacture human approval from the presence of an answer candidate.
- New contradictory material can justify `reopen_knowledge_question`, with a reason.
  Preserve the previous answer and its history. Reopening starts a new review generation.
- Acknowledge only checked sources with `complete_question_review`: `reviewed`,
  `not_relevant`, `unavailable` (reason), or `deferred`. Save answers before receipts.
  Deferred sources stay pending. The response includes up to 50 `unavailableSources` with exact coordinates and reasons; retry them when readable.
  Review progress is personal and tied to the exact question generation.
- Live mail is separate: exhaust authorized provider search pagination for bounded,
  account-specific UTC windows, then call `record_question_mail_window` with reviewed,
  partial or unavailable. Never cover failed pages or another person's mailbox.
  Personal evidence must be published through authorized ingestion into the question's
  Workspace before it can be quoted or cited in a shared answer.
- Continue pages until done or explicitly report remaining work/gaps. Do not imply
  an agent is continuously monitoring; semantic review runs when the external agent runs.

## Migrate existing embedded questions

Use `list_question_migration`, not a keyword or heading search, to inventory every
current accessible record. Read complete bodies, extract/reuse every actionable
question, replace text with links, and call `record_question_migration_review` for
the resulting exact revision. `extracted` requires all linked question IDs;
`no_open_questions` requires a full semantic review; uneditable or ambiguous items
stay `blocked` with a reason. Include archived records and non-English prose.
Preserve immutable source snapshots and historical revisions. Restart the inventory
after every pass: changed revisions become pending again. Use `includeReviewed: true` for a second complete audit. Never claim all deployed
data is migrated while access is incomplete, records remain, or blockers exist.
