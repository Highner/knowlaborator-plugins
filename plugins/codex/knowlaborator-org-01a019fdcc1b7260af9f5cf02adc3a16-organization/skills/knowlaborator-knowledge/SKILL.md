---
name: knowlaborator-knowledge
description: Search and maintain reusable OKF knowledge, including scoped curation from research or an ingested source.
---

# Knowlaborator Knowledge

- [reading.md](references/reading.md): search, exact records, revision history
  and existing exports.
- [knowledge-authoring.md](references/knowledge-authoring.md): concept writes
  and strict local validation.
- [task-scoped-knowledge-capture.md](references/task-scoped-knowledge-capture.md):
  curation during an explicitly knowledge-producing request on a writable binding.
- [email-snapshots.md](references/email-snapshots.md): derive knowledge from an
  already ingested EmailMessage.
- [relationship-explanations.md](references/relationship-explanations.md): explain
  Explorer connections with exact passages, or perform an authorized evidence backfill.

Use Documents for files and Templates, Mail for provider-message preservation,
and Administration for Workspace taxonomy guidance or retrieval configuration.
Resolve an unspecified durable destination through Work before writing.

Search and reconcile before writes. Preserve provenance, unknown OKF fields,
idempotency and current-version preconditions; avoid duplicates and no-op
revisions. archive_knowledge requires fresh confirmation of the exact target
and lifecycle effect. Read-only work never starts a capture or export operation.
