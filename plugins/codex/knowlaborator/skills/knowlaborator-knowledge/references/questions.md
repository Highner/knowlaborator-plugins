# Independent Knowledge questions

An actionable open question is one OKF concept of type `question`, with its own ID,
owner, scope, expected evidence, check date and lifecycle. It is never a decision,
decision plan, assessment or score.

## Decide whether to track a question

Apply these criteria within the person's authorized task, including filing and
curation. Maintenance creates no question: when a source shows a need that meets
them, it reports the question to the member who asked it, to the proposer of the pending
decision it blocks, or else to the member the source belongs to
([maintenance.md](maintenance.md)).

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
4. Zero new questions is a successful outcome. Processing a source authorizes
   evaluating the need; it does not require producing records. If none qualify,
   report a brief reason.

## Reported questions

Questions maintenance found in sources wait in the Questions inbox under Reported, beside
"Still needed?". `list_knowledge_reports` with `kind: "question"` reads the person's own.
After the person's explicit choice in this session, `settle_knowledge_report` with
`action: "confirm"` opens the question, optionally in their words, with them responsible,
the asker and the related records; `action: "discard"` discards it, and OrgApp then never
reports that passage again. Never confirm or discard on your own; undoing a discard
happens in the browser.

## Author and link

For a qualifying unresolved need, use `create_knowledge_question` with a stable
operation UUID, the organization's primary OKF language, a responsible member (who is asked) and originating
`relatedKnowledgeIds` from any Workspace you can read in this organization. Links grant no access; related titles are filtered for each reader. Set `askedBy` only when the person or the source names who
asked: `{ "kind": "member", "id": membershipId }` or `{ "kind": "contact", "id":
contactId }` for a Contacts contact. Never infer an asker from who created a record.
Without a `nextCheckDate` the first check is 30 days ahead.
Then replace the embedded question in each originating concept with an ordinary
`knowledge://objects/UUID` link using its current version. Create first, replace
second, verify last. Preserve unrelated facts and source quotations. Several
concepts can link to one question; do not keep a second copy of its answer/state.
Use `revise_knowledge_question` for wording, scope, expected evidence, owner, asker or
date; send the existing `askedBy` and links again, because omitting them clears them.

## Evidence and checks

Evidence reaches a question in three places, under the same rules:

- **Maintenance** lists the open questions connected to a filed source in
  `get_maintenance_context`; `search_content` shows question hits with their status
  ([maintenance.md](maintenance.md)).
- **The Incoming run** reads live mail and messages. When one answers an open
  question, file it with `process_input` in the question's Workspace, unless the
  person's instructions say otherwise, and cite the filed copy. Personal evidence is
  never quoted or cited in a shared answer before it is filed.
- **Due checks.** Every open question has a check date. When it comes, the
  responsible member's Incoming run searches filed knowledge, live mail and messages
  once. It proposes an answer when the evidence gives one, or calls
  `record_question_check` with outcome `nothing_new` and the current `expectedVersion`.
  The next check then moves 30, then 60, then 90 days out.

Rules for every answer:

- Read exact source versions through their owning tools. Search can help prioritize,
  but never treat an unread source as checked.
- Compare what the question asks, its scope and expected evidence with what the
  source actually establishes. Partial answers, plans and guesses remain uncertain.
- Save an answer discovered in context with `propose_question_answer`, including precise conditions.
  Evidence is optional; cite exact sources when available. This leaves the question `answer_proposed`
  under To confirm, attributed to the authenticated member's agent. Never invent sources.
- Only the person settles a question. Record their explicit choice in this session with
  `settle_knowledge_question`, the current `expectedVersion` and `confirmed: true`. Your own
  discovery remains a proposal; never set confirmed based only on finding evidence. Pick the action:
  - `answer` when the person supplies or accepts an answer (for example, “the answer is X”). Pass
    that text as `answer`; it is recorded and the question resolved atomically. Evidence is
    optional. No documents or second confirmation are required.
  - `accept_answer` when the person accepts the proposed answer. Update dependent concept facts
    when authorized and appropriate. Never manufacture human approval from the presence of an
    answer candidate.
  - `reject_answer` when the person says a proposed answer is wrong or not wanted, with their short
    `reason` if they gave one. It deletes only the proposal: the question is unanswered again for
    everyone, keeps its review generation, and the proposal stays only in revision history. Do not
    re-propose the same answer from sources you already checked.
  - `dismiss` when the person wants the whole question closed, with a short `reason` (no longer
    relevant, duplicate, or no longer being pursued). It closes the shared question for everyone,
    preserves its history, and stops its checks. To drop only a wrong answer, use `reject_answer`.
    Dismissed questions are absent from default lists, even with `includeResolved`; request
    `status: "dismissed"` explicitly to inspect them. Reopen with a reason before answering or
    checking again.
- New contradictory material can justify `reopen_knowledge_question`, with a reason.
  Preserve the previous answer and its history. Reopening starts a new review
  generation and a new check date.
- Do not imply an agent is continuously monitoring; checks run when the external agent runs.

## Still needed?

An open question asks its responsible member whether it is still needed after three
empty checks, when its check is more than 60 days overdue, or when a related decision
was decided, rejected, replaced or withdrawn since it was asked. `stillNeeded` on the
question says which. The person answers in the browser: **Let go** dismisses it with
the reason “No longer pursued” and can be reopened; **Keep asking** sets a new check
date. Never let go of a question yourself because of its age, its empty checks or this
prompt; nothing dismisses a question automatically.
