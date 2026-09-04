---
name: knowlaborator-documents
description: Find, inspect, upload or maintain ordinary Documents and reusable Document Templates.
---

# Knowlaborator Documents

Read [reading.md](references/reading.md) for discovery, exact versions and files.
Read [document-ingestion.md](references/document-ingestion.md) only when saving
or assessing a file. Connected-mail attachment import belongs to Mail's
message-ingestion workflow; do not recreate it with download and upload calls.

Use update_document for logical metadata, never file bytes. Purpose is immutable:
document or template. Templates require a trimmed general description of 1–500
characters; ordinary Documents may omit it. General description and contextual
Playbook-reference guidance are distinct.

For move_document, read the current revision and confirm the exact source and
destination Workspaces and access change. Both require Manager access.
Templates referenced by a Playbook cannot be moved or archived until an
administrator unlinks them; never work around DOCUMENT_IN_USE with a duplicate.
