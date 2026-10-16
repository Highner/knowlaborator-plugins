---
name: knowlaborator-knowledge
description: Search and maintain reusable OKF knowledge and decisions, find, inspect or save Documents and Document Templates, and curate knowledge from research or an ingested source.
---

# Knowlaborator Knowledge

## Primary OKF language

Read the active organization's `primaryOkfLanguage` from `whoami` (`de` = German,
`en` = English), or use the current organization guidance returned during MCP
initialization/discovery or organization selection. Use it for new and revised OKF
prose unless the user requests another language. Preserve original quotations and
existing content unless translation is requested. Refresh `whoami` after a settings
change or organization switch; the preference does not select the browser or
conversation language.

## Fast path

- Knowledge maintenance (“do the maintenance”): use [maintenance.md](references/maintenance.md)
  for the complete incremental filed source → changed passages → relations, support of
  existing records, issues and decision drafts workflow. Maintenance may create a
  supported decision draft; other knowledge authoring stays with a person. Routine
  maintenance excludes historical backfill unless expressly requested.
  During this procedure the source-choice branches below do not start other workflows; use
  only the maintenance protocol and the owning source/read tools. Incoming items are the
  Incoming run of [My Day](../knowlaborator-my-day/SKILL.md). A scheduled or unattended
  run uses the Knowlaborator Maintenance plugin (`knowlaborator-maintenance`), which carries
  the same protocol with only the narrow decision-draft authoring operation.
- Maintenance review the person decides in chat: after their explicit choice for one exact
  proposal, call `resolve_maintenance_review` with confirmed=true and that version. No
  browser confirmation is needed. Only this plugin offers the tool; the maintenance plugin
  leaves every review to a person.

- Topical or status question: start with `search_content`, then read selected sources
  with their owning exact-read tools. Browse the knowledge folders with
  `list_knowledge` only when the task needs an inventory or a systematic review, and
  read relations with `list_knowledge_relations`; do not routinely add a folder scan
  to a topical search.
- Source choice or several discovery steps: read the
  [navigation-learning procedure](references/navigation-learning.md) before the first
  read. Generate one task-local episode UUID and reuse it as `navigationEpisodeId`
  on supported reads. Keep returned observation IDs for one useful assessment at
  meaningful completion. Skip this for exact known-record reads.
- Shared Explorer: call `get_active_context` once. When `explorerSceneState` is
  `available`, answer visible-graph questions from its bounded `explorerScene` titles,
  excerpts and relationships. Do not rerun the graph query. Read exact records for
  full content, provenance, evidence or edits.
- Exact record edit: read the current record once and use its version for
  `update_knowledge`. Search only if the target is ambiguous; load guidance only if it
  could shape the change.
- New concept: search concise candidates, load relevant Workspace guidance, then create.
- Open issues: read [issues.md](references/issues.md) for discovery, duplicate checking,
  evidence, assignment and the human lifecycle. Both runs can infer issues across sources;
  update matching issues before creating New. Zero findings is a successful outcome.
- Decision drafts from any relevant sources: read
  [decisions-and-reviews.md](references/decisions-and-reviews.md). Distinguish a needed,
  discussing, waiting, agreed or already documented choice; search for an existing decision
  and create no duplicate merely because a source has no dedicated decision record.
- Decision evidence, assessments and follow-up plans: read
  [decision-loops.md](references/decision-loops.md).

Read only the reference needed for the requested operation:

- [maintenance.md](references/maintenance.md): independent maintenance, evidence, contradiction marks and
  chat/browser review.
- [navigation-learning.md](references/navigation-learning.md): conditional source-choice learning
  for substantive discovery; skip exact known-record reads.
- [reading.md](references/reading.md): search, exact reads and exports.
- [knowledge-authoring.md](references/knowledge-authoring.md): writes and validation.
- [decisions-and-reviews.md](references/decisions-and-reviews.md): decision records,
  exact-revision standing, direct recording, user-directed browser/MCP review, replacing and
  abandoning.
- [issues.md](references/issues.md): organizational issues, candidate checks, evidence,
  assignment, review dates and resolution.
- [decision-loops.md](references/decision-loops.md): evidence, assessments, proposal
  evidence, findings, waiting proposals and follow-up plans.
- [documents.md](references/documents.md): Documents, Templates, exact files and
  signed copies.
- [document-ingestion.md](references/document-ingestion.md): saving a file and
  extracting its contacts.
- [task-scoped-knowledge-capture.md](references/task-scoped-knowledge-capture.md):
  explicitly requested curation.
- [email-snapshots.md](references/email-snapshots.md): ingested mail.
- [relationship-explanations.md](references/relationship-explanations.md):
  passage-backed connections.

Use Communication for provider-message preservation and Administration for Workspace
taxonomy or retrieval configuration. Resolve an unspecified durable destination
through Work before writing. Preserve provenance and unknown OKF fields; avoid
duplicates and no-op revisions. `archive_knowledge` requires fresh confirmation of
the exact target and lifecycle effect. Read-only work never starts a content capture or export operation. The bounded
internal navigation-learning operation is the sole task-scoped exception and grants
no domain write authority.
