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

- Shared Explorer: call `get_active_context` once. When `explorerSceneState` is
  `available`, answer visible-graph questions from its bounded `explorerScene` titles,
  excerpts and relationships. Do not rerun the graph query. Read exact records for
  full content, provenance, evidence or edits.
- Exact record edit: read the current record once and use its version for
  `update_knowledge`. Search only if the target is ambiguous; load guidance only if it
  could shape the change.
- New concept: search concise candidates, load relevant Workspace guidance, then create.
- Question or ingestion session: read [questions.md](references/questions.md), list
  relevant independent questions and review their pending evidence. Decision coverage
  never substitutes for question coverage.
- Decision or ingestion session: start with `get_decision_catch_up` when the person has a
  decision monitoring scope, as described in decision-loops.md.

Read only the reference needed for the requested operation:

- [reading.md](references/reading.md): search, exact reads and exports.
- [knowledge-authoring.md](references/knowledge-authoring.md): writes and validation.
- [decisions-and-reviews.md](references/decisions-and-reviews.md): decision records,
  exact-revision standing, direct recording, browser human review, replacing and
  abandoning.
- [questions.md](references/questions.md): independent question concepts, evidence
  review, resolution and migration of embedded open questions.
- [decision-loops.md](references/decision-loops.md): catch-up, assessments, proposal
  evidence, findings and follow-up plans within the monitoring scope.
- [documents.md](references/documents.md): Documents, Templates, exact files and
  signed copies.
- [document-ingestion.md](references/document-ingestion.md): saving a file and
  assessing it for OKF.
- [task-scoped-knowledge-capture.md](references/task-scoped-knowledge-capture.md):
  explicitly requested curation.
- [email-snapshots.md](references/email-snapshots.md): ingested mail.
- [relationship-explanations.md](references/relationship-explanations.md):
  passage-backed connections.

Use Communication for provider-message preservation and Administration for Workspace
taxonomy or retrieval configuration. Resolve an unspecified durable destination
through Work before writing. Preserve provenance and unknown OKF fields; avoid
duplicates and no-op revisions. `archive_knowledge` requires fresh confirmation of
the exact target and lifecycle effect. Read-only work never starts a capture or
export operation.
