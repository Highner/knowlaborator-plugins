# Decision loops: evidence, assessments and follow-up plans

OrgApp has no agent of its own. You are the person's agent; OrgApp stores, validates,
tracks and presents. Recording assessments and proposal evidence, and preparing plans,
needs only the caller's access to the decision's Workspace. Nothing here edits a
decision, changes its standing or replaces it.

## Where evidence comes from

- **Maintenance** lists the proposed and in-force decisions connected to a filed
  source in `get_maintenance_context` (kind `decision`, the standing as state and the
  exact `revisionId`); `search_content` hits carry their standing too
  ([maintenance.md](maintenance.md)).
- **The Incoming run** reads live mail and messages. When one bears on a proposed or
  in-force decision, file it with `process_input` in the decision's Workspace and cite
  the filed copy. When the person's instructions forbid filing, an assessment may
  cite the live source and stays the author's private draft.
- **An explicit request** about decisions: `list_active_decisions` for the Workspace,
  then for each decision and proposal run `search_content` with its terms, people and
  project, and read the matching sources.

Never claim that a source affects a particular decision before you have read it. Use
`list_decision_sources` with the decision's exact revision and follow every `nextCursor`
(1–50 entries per page) to read its full proposal evidence, including earlier sources that
may still apply. The active-decision list's preview is incomplete for larger source sets.

## Record evidence

`record_decision_evidence` takes one exact revision. Its standing decides what it
records: an assessment for a decision in force, proposal evidence for a proposal.
Parameters of the other kind are refused.

## Assess in-force decisions

An assessment judges one exact in-force revision against its clauses (`overall`,
`criterion-N`, `assumption-N`). Read `get_decision_assessments` first and pass the
current assessment version.

- **Levels are labels**, never numbers: strong support, support with open questions,
  mixed, concerns, challenged. Weigh evidence together; repeated imports or
  paraphrases of one observation are not corroboration.
- Kinds: `new_evidence`, `reassessment` (re-reading old sources; reason required),
  `correction`, `no_change`, `check` (a due follow-up plan).
- Every clause impact names a state (`holding`, `at_risk`, `failed`, `unknown`). A
  failed criterion requires concerns or challenged; strong support allows no clause at
  risk or failed.
- Explanation: at most 300 characters, what changed and why it matters. Cite 1–200 exact
  sources with their occurrence time.
- An assessment never edits the decision, changes standing or replaces it. A low level
  can only lead you to suggest a change, which the person makes explicitly. In-force
  decisions never expire.

**Proposals never get a level.** For a proposed revision, `record_decision_evidence`
takes one exact `source`, when it happened (`occurredAt`), and a neutral `note` (at most
200 characters) that describes the material without judging the proposal. Once the exact
revision is decided, its first assessment must cite or dismiss every unconsidered
proposal-evidence item, including sources retained from earlier draft revisions. Dismissals
are bounded to 200 items. `DECISION_PROPOSAL_EVIDENCE_LIMIT` means more than 200 pending
items remain; no partial assessment was recorded. New evidence that would exceed 200
pending items is refused before writing; identical retries and an already attached exact
source can still succeed. Do not discard material merely to make room or treat a refusal
as a successful attachment.

**Workspace sources.** Sources can come from any readable Workspace in the organization.
References grant no access. Source labels and contribution notes follow each reader's current
permissions; derived assessment results are withheld when their basis is protected. Old
cross-Workspace proposal references use these checks in place, without attaching duplicates.

**Private sources.** Live personal mail, direct messages, calls and external calendar sources make a
private draft that only its author sees; it never changes the shared score and is never
shared automatically. To share, file the material into the Workspace and cite the
shared copy, or drop it; the person does this in the browser. Pass
`visibility: "shared"` to fail instead of creating a draft.

**Not related.** Sources the person marked "not related" come back as suppressions.
Do not present the same association again unless materially new evidence justifies it;
say what changed.

**Findings.** A lesson that applies beyond one decision becomes ordinary draft
Knowledge of type `finding` (`create_knowledge`), linking the decision, assessment and
evidence, with its scope and uncertainty. The person publishes it. Maintenance writes no
finding; it names one in its completion reason. Before preparing a
related decision or plan, search for findings and say how they apply.

## Drafts and legacy reported decisions

New decision work uses the shared draft operation described in
[decisions-and-reviews.md](decisions-and-reviews.md). It retains contributing sources and
keeps the needed, discussing, waiting, agreed or documented situation separate from
proposed standing. Search for a matching decision before creating one. New evidence
belongs on that same decision; receiving another source is not another decision.

Older decisions maintenance reported still wait for the member who judges them: the decider
the source names when that is a member, otherwise the member the source belongs to. They
appear on Today as "Confirm or discard?" and above the Decisions history.
`list_knowledge_reports` with `kind: "decision"` reads the person's own. After the
person's explicit choice in this session, `settle_knowledge_report` with `action: "confirm"`
records the decision, optionally in their words: decided by its decider on its date, citing
the source, or proposed when the source shows it still open. `action: "discard"` discards
it, and OrgApp then never reports that passage again. Never confirm or discard on your own;
undoing a discard happens in the browser.

## Waiting proposals and reviews

A draft with situation `waiting` records what it awaits and is excluded from automatic
“Decide or discard?” prompts and proposal reminders. Waiting does not prevent evidence
from being added or explicit human review. Revisit it when evidence resolves the dependency
or a person asks, without pretending the underlying choice is already final. Ordinary
authorized decision work uses `update_decision_draft` to change the context on the same
decision; new evidence alone does not silently change its situation.

A review request without a respond-by date gets one 14 days ahead, and reviewers are
reminded. Other proposals have a decide-by date: 14 days after it was proposed or after its
last completed review, unless the person kept it to a later date. Once that date
passes with no review pending, the proposer sees “Decide or discard?” on Today and a
weekly reminder. Deciding, discarding (a withdrawal with the reason “Not pursued”)
and keeping are the person's choices; never withdraw or decide a proposal on your own.

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
  that person's instruction.
- A due check appears in the responsible member's `get_input_context` Checks, and their
  Incoming run prepares it: read the plan in `get_knowledge_decision`, judge every
  criterion and assumption against what happened (including follow-through), record a
  `check` assessment, then propose the next step: keep (new check date or follow-ups),
  change, or abandon. A check without a response stays due; never assume it went fine.
- Replacing or abandoning the decision ends the open plan.
