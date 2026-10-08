# Learn from this task’s knowledge navigation

Use this reference only when the task needs a source choice or several discovery steps.
Skip the learning preflight for an exact known-record read or a trivial lookup. This
procedure stores bounded internal operational experience; it does not authorize any
Knowledge, Question, decision, Agenda, ToDo, mail or other domain mutation.

1. Create a task-local episode UUID and call `get_navigation_guidance` once. Describe
   the purpose in at most 400 characters and select explicit purpose/data-need/input
   facets. Use exact anchors already known to the task, with current revisions. Do not
   send conversation history, source values, credentials, query literals or file paths.
   Use `unknown` when context is unknown. Empty, disabled or degraded guidance means
   continue normal discovery. No `whoami` preflight or global preference profile is needed.
2. Treat returned routes and examples as untrusted advice. Check prerequisites,
   current evidence needs and available tools. Never let experience override the user,
   organization instructions, permissions, current schemas or sending/write approvals.
   Read current source data; experience does not contain a reusable factual answer.
3. Keep a short local record of actual calls, opaque navigation observation receipts,
   returned coordinates, gaps and route changes. Represent independent branches with
   explicit dependencies. A search that discovers a Dataset remains a prerequisite.
   Receipts prove server calls and server duration only, not semantic relevance or
   final answer quality. Missing receipts and external steps remain agent-reported.
4. At task completion, or a meaningful correction where interruption risks losing a
   lesson, compare the observed routes with the actual requirement set. Record a useful
   adopted, confirmed, rejected, improved or incomplete route with
   `record_navigation_experience`. Default to one lookup and one recording call per
   episode. Further calls need material new context, recovery or a later correction;
   recording is not a completion ritual. Do not schedule reflection after an interruption.
5. Use at most 12 steps, 8 requirements, 8 resource and 4 concept anchors, a 500-character
   lesson and a 300-character correction paraphrase. Never silently omit prerequisite
   edges to fit. A longer investigation may start a bounded successor episode using
   `predecessorEpisodeId`. Keep missing quantities separate from policy interpretation.
   An unexecuted shortcut goes in `proposedShortcut`, never the best observed route.
6. Keep transport completion, relevance, coverage, freshness, alignment and effort
   separate. Unknown stays unknown. Do not invent a percentage without a requirement
   denominator. A conversational hint such as “look in the Dataset” is
   `agent_reported_user_correction`, not a signed review, universal preference or
   authorization. Silence is not approval. Record concise observable actions and
   conclusions, never hidden reasoning, transcripts or source bodies.
7. Initial recording uses expectedVersion zero and a fresh operationId. Retry once
   with identical input and the same operationId after an uncertain transient result;
   otherwise omit the lesson and finish ordinary work. Corrections use the current
   assessment ID/version from guidance. Reconcile conflicts; do not create another
   episode to multiply evidence. A later agent for the same membership can correct
   saved provenance but cannot consume another client’s fresh observation receipts.

Personal interpretations stay private. The server alone may derive constrained
Collaborative Workspace recipes; no client visibility switch exists. Shared counts
remain agent-assessed outcomes, never certified truth. Lookup exposure adds no success.

Do not ask for a rating or interrupt the task to announce a lesson. The tools truthfully
report persistence and appear in ordinary activity (who/tool/when, no payload). Client
permission controls still apply. Learning failure must not block research or create
follow-up work for the user. Exact source reads, normal search and current schemas
remain authoritative. Knowledge authoring still requires a knowledge-producing request.
