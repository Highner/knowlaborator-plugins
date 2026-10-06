# Decision loops: catch-up, assessments and follow-up plans

OrgApp has no agent of its own. You are the person's agent; OrgApp stores, validates,
tracks and presents. Work here only within the person's **decision monitoring scope**
(Workspaces, source domains and allowed actions they set in the browser). Writes
outside it fail with `DECISION_MONITORING_SCOPE_REQUIRED`; explain that only the person
can widen the scope in their settings. The scope grants no extra access and authorizes
no other capture.

## Start each decision or ingestion session with catch-up

1. `get_decision_catch_up`: scopes, unreviewed counts and since when, covered-through
   times, last progress, look-backs, due plans, gaps and mail/calendar windows. Report
   it in one line, for example "14 items waiting since 2 Oct". Never claim that an
   unreviewed item affects a particular decision before you have reviewed it.
2. `claim_decision_catch_up` for one scope. Keep the returned fencing token; a lost or
   expired lease (`DECISION_CATCH_UP_LEASE_LOST`) means claim again and reread.
3. Work **decision-first**: `list_active_decisions` for the scope, then for each
   in-force decision and proposal run `search_content` in the page's time range with
   its terms, people and project. Read the flagged and linked entries; read the rest in
   full only when the page is small. Messages and transcripts come from the page.
4. Save results through the narrow operations (below), then
   `complete_decision_catch_up` with one disposition per entry: `assessed` (with the
   assessment or proposal-evidence IDs), `no_applicable_decision`, `unavailable`
   (reason) or `deferred` (stays pending). Reading a page never completes it.
5. Repeat while pending work remains, or `release_decision_catch_up`.

Mail and calendar stay at the provider: review them with the existing provider search
from the covered time onward, then `record_decision_review_window` with the outcome
(`reviewed`, `partial`, `unavailable`). Look-backs cover new decisions and widened
scopes; handle them like pages.

## Assess in-force decisions

`record_decision_assessment` judges one exact in-force revision against its clauses
(`overall`, `criterion-N`, `assumption-N`). Read `get_decision_assessments` first and
pass the current assessment version.

- **Levels are labels**, never numbers: strong support, support with open questions,
  mixed, concerns, challenged. Weigh evidence together; repeated imports or
  paraphrases of one observation are not corroboration.
- Kinds: `new_evidence`, `reassessment` (re-reading old sources; reason required),
  `correction`, `no_change`, `check` (a due follow-up plan).
- Every clause impact names a state (`holding`, `at_risk`, `failed`, `unknown`). A
  failed criterion requires concerns or challenged; strong support allows no clause at
  risk or failed.
- Explanation: at most 300 characters, what changed and why it matters. Cite exact
  sources with their occurrence time.
- An assessment never edits the decision, changes standing or replaces it. A low level
  can only lead you to suggest a change, which the person makes explicitly.

**Proposals never get a level.** For a proposed revision use `record_proposal_evidence`:
one exact source, when it happened, and a neutral note (at most 200 characters) that
describes the material without judging the proposal. Once the exact revision is decided,
its first assessment must cite or dismiss every proposal-evidence item.

**Private sources.** Visibility follows the cited sources. A private source makes a
private draft that only its author sees; it never changes the shared score and is never
shared automatically. To share, ingest the material into the Workspace through the
existing publication path and cite the shared copy, or drop it; the person does this in
the browser. Pass `visibility: "shared"` to fail instead of creating a draft.

**Not related.** Sources the person marked "not related" come back as suppressions.
Do not present the same association again unless materially new evidence justifies it;
say what changed.

**Findings.** A lesson that applies beyond one decision becomes ordinary draft
Knowledge of type `finding` (`create_knowledge`), linking the decision, assessment and
evidence, with its scope and uncertainty. The person publishes it. Before preparing a
related decision or plan, search for findings and say how they apply.

## Follow-up plans and checks

A plan says who checks what, and when to bring the decision back.

- `propose_decision_plan` with the decision: exactly one responsible member (helpers take
  part through linked to-dos), the question and the evidence that would answer it (at
  most 200 characters each), an optional check date with a one-line reason, conditions
  (`date`, `todo_status`, `milestone`, `semantic`) and links to to-dos, milestones and
  sources. Keep what was agreed apart from what you suggest; a date you propose is shown
  as suggested. Small decisions may need no plan.
- The responsible person or the recorder confirms in the browser. Confirming, settling
  (with an outcome) and cancelling (with a reason) through `update_decision_plan` need
  the person's instruction and the "prepare follow-ups" action in their scope.
- A due check: read `get_decision_plan`, judge every criterion and assumption against
  what happened (including follow-through), record a `check` assessment, then propose
  the next step: keep (new check date or follow-ups), change, or abandon. A check
  without a response stays due; never assume it went fine.
- Replacing or abandoning the decision ends the open plan.
