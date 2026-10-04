---
name: knowlaborator-knowledge
description: Search and maintain reusable OKF knowledge, including scoped curation from research or an ingested source.
---

# Knowlaborator Knowledge

## Fast path

- Shared Explorer: call `get_active_context` once. When `explorerSceneState`
  is `available`, answer visible-graph questions from its bounded
  `explorerScene` titles, excerpts and relationships. Do not rerun the graph
  query. Read exact records for full content, provenance, evidence or edits.
- Exact record edit: read the current record once and use its version for
  `update_knowledge`. Search only if the target is ambiguous; load guidance
  only if it could shape the change.
- New concept: search concise candidates, load relevant Workspace guidance,
  then create with an idempotency key.

Read only the reference needed for the requested operation:

- [decisions-and-reviews.md](references/decisions-and-reviews.md): decision OKF authoring, exact-revision standing, direct recording and browser human review.
- [reading.md](references/reading.md): search, exact reads and exports.
- [knowledge-authoring.md](references/knowledge-authoring.md): writes and validation.
- [task-scoped-knowledge-capture.md](references/task-scoped-knowledge-capture.md):
  explicitly requested curation.
- [email-snapshots.md](references/email-snapshots.md): ingested mail.
- [relationship-explanations.md](references/relationship-explanations.md):
  passage-backed connections.

Use Documents for files and Templates, Mail for provider-message preservation,
and Administration for Workspace taxonomy or retrieval configuration. Resolve
an unspecified durable destination through Work before writing. Preserve
provenance, unknown OKF fields, idempotency and current-version preconditions;
avoid duplicates and no-op revisions. `archive_knowledge` requires fresh
confirmation of the exact target and lifecycle effect. Read-only work never
starts a capture or export operation.
