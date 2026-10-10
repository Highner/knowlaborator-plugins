# Independent Knowledge questions

An actionable open question is one OKF concept of type `question`, with its own ID,
owner, scope, expected evidence, optional next check date and lifecycle. It is never
a decision, decision plan, assessment or score. Use question tools and question
coverage only; completing decision catch-up does not review questions.

## Decide whether to track a question

Apply these criteria within the person's authorized task, including ingestion,
curation and migration:

1. Track a question only when a person actually asked it and wants it pursued
   (directly or evidenced by the source), or its answer would materially affect a
   concrete pending decision or action. State that need in the context. A missing
   field, speculative risk, generic checklist or interesting unknown alone is not
   a reason to create a question; preserve uncertainty as labeled context.
2. First search existing knowledge and questions, including resolved answers and
   `status: "dismissed"`, and read relevant available evidence. If the evidence
   already answers it, use that answer in the authorized work instead of creating
   a question just to answer it. For an existing question, follow the answer
   proposal/acceptance rules below. Reuse matching questions. Never recreate a
   dismissed question. Reopen its existing record only for a concrete new reason
   or the person's explicit request, keeping its history, including why it was dismissed.
3. Bundle related aspects of the same clarification into one question's context
   and expected evidence. Keep separate records only for independently actionable
   needs. For example, delivery date, quantity and capacity belong together when
   they jointly determine whether one delivery commitment can be met.
4. Zero new questions is a successful outcome. A selected `questions` action
   authorizes evaluating the need; it does not require producing records. If none
   qualify, report a brief reason and complete the action with empty results.

## Author and link

For a qualifying unresolved need, use `create_knowledge_question` with a stable
operation UUID, the organization's primary OKF language, a responsible member (who is asked) and originating
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
- Save an answer discovered in context with `propose_question_answer`, including precise conditions.
  Evidence is optional; cite exact sources when available. This leaves the question `answer_proposed`
  under To confirm, attributed to the authenticated member's agent. Never invent sources.
- When the person explicitly supplies or accepts an answer (for example, “the answer is X”), use
  `answer_knowledge_question` with that text, current `expectedVersion`, and `confirmed: true` to
  answer and resolve atomically. No documents or second confirmation are required. Your own discovery
  remains a proposal; never set confirmed based only on finding evidence.
- `resolve_knowledge_question` requires the person's explicit instruction to accept
  the answer. Update dependent concept facts when authorized and appropriate. Never
  manufacture human approval from the presence of an answer candidate.
- When the person says a proposed answer is wrong or not wanted, use `dismiss_question_answer` with the
  current version and, if they gave one, a short reason. It deletes only the proposal: the question is
  unanswered again for everyone, keeps its review generation, and the proposal stays only in revision
  history. Never delete a proposal on your own judgment, and do not re-propose the same answer from
  sources you already reviewed.
- Use `dismiss_knowledge_question` only on the person's instruction, with the current version and a
  short reason (no longer relevant, duplicate, or no longer being pursued). It closes the shared question
  for everyone, preserves its history, and stops routine review. Never dismiss merely due to age or lack
  of an answer. Dismissed questions are absent from default lists, even with `includeResolved`; request
  `status: "dismissed"` explicitly to inspect them. Reopen with a reason before answering/reviewing again.
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
current accessible record. Read complete bodies and apply the tracking criteria
above. Extract/reuse qualifying questions, replace their text with links, and call
`record_question_migration_review` for the resulting exact revision. Keep other
uncertainty as labeled context; do not invent questions to fill gaps.
`extracted` requires all linked question IDs; `no_open_questions` requires a full
semantic review that found no qualifying questions to extract; uneditable or
ambiguous items stay `blocked` with a reason. Include archived records and non-English prose.
Preserve immutable source snapshots and historical revisions. Restart the inventory
after every pass: changed revisions become pending again. Use `includeReviewed: true` for a second complete audit. Never claim all deployed
data is migrated while access is incomplete, records remain, or blockers exist.
