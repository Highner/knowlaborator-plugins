# Save a Document or Document Template file

Use this flow for a PDF, non-macro DOC/DOCX/PPTX/XLSX, PNG, JPEG, GIF, WebP, EML, MSG, or ZIP file the user wants stored in OrgApp.
Connected-mail attachments belong to [mail ingestion](../../knowlaborator-communication/references/mail-ingestion.md)
and always use the ordinary Document purpose.

Document bytes never pass through MCP arguments. Use the file-transfer tool
already exposed by the client; do not probe hidden tools or try an alternative
transfer path after failure. The same flow handles the first file and every
later revision. Never include credentials or tokens in arguments or summaries.

1. Confirm the exact target Workspace and at least `Contributor` access. The
   active Workspace is only a default; never infer access from selection or tags.
2. Confirm whether the immutable logical purpose is `document` or `template`.
   Use `template` only for an example intended to guide creation or editing of
   other documents. `documentType` is independent. Templates require a trimmed
   1–500-character general description; ordinary Documents may omit it.
3. For a new logical Document, call `create_document` once and retain its ID.
   For a revision, reuse the existing ID. Use `update_document` only for title,
   description, document type, or supported logical metadata; it never writes
   file bytes and must not create a replacement Document.
4. Save using the workflow exposed in the current tool catalog:
   - **`save_document_file`**: pass the selected file through the client's native
     file input with the Document ID and an operation-stable idempotency key.
     The host supplies the file reference. OrgApp transfers and validates the
     bytes, derives size and checksum, finalizes the version, and queues
     processing in this one call. Do not calculate a checksum, construct a
     download URL, or run a shell upload.
   - **`begin_document_file_upload` / `complete_document_file_upload`**: inspect
     the local file, determine its leaf filename, exact byte size, and lowercase
     SHA-256, and begin with those values, the Document ID, and an operation-stable
     idempotency key. Immediately send only raw file bytes to the returned short-lived
     HTTPS URL using its exact method and headers; do not send JSON, multipart,
     MCP authorization, or cookies. Then complete with the `uploadIntentId`.
     Never place local paths or upload URLs in MCP arguments or summaries.
   Both paths enforce the 100 MiB limit, reauthorize the write, inspect the actual
   file, and use the same version/processing pipeline.
5. If the outcome is uncertain, keep the Document ID and idempotency key. For
   native transfer, retry the same save with the selected file (select it again
   if the host reference expired). For direct transfer, retry begin with the
   same metadata/key while the intent is valid and complete with the same upload
   ID; if the one-hour intent expired before accepting bytes, begin a replacement
   intent with a new key against the same Document. Never create a duplicate
   Document or switch upload tools because the outcome is uncertain.
6. Use `get_document_version` to check processing. When the saved bytes must be
   verified or semantically assessed, call `get_document_file` and inspect the
   exact original through the client's resource/file support using the shared
   [file inspection](../../knowlaborator-work/references/file-inspection.md) flow.
   Never use indexed chunks, preview text, or metadata as a substitute for the
   actual file. File storage does not wait for knowledge maintenance.

## Extract CRM contacts from an ordinary Document

When the Contacts & accounts (CRM) module's tools are listed, successful ingestion
of an ordinary Document includes bounded CRM contact extraction. Templates never
trigger this step. After reading the exact current
version, select only high-confidence real people who are individually material
to a plausible organization relationship. The Document must present the person
as a direct contact, author, addressee, signer, counterparty, decision-maker,
owner, or other active participant, with document-specific relationship context
or useful contact evidence such as an affiliation, role, email address, or
telephone number.

Do not treat every recognized name as a contact candidate. Exclude people who
appear only in large name lists, directories, rosters, attendance or recipient
lists, credits, bibliographies, indexes, acknowledgements, examples, legal
boilerplate, historical or news references, or incidental and random
enumerations. A repeated or prominently formatted name alone is insufficient.
Do not infer missing identity, affiliation, job title, email, or telephone
details. If identity or relationship relevance remains ambiguous, skip the
person and report why instead of interrupting ingestion or creating a doubtful
record.

Route every selected candidate through `$knowlaborator-people`. The explicit
Document-ingestion request authorizes these high-confidence contact writes, so
do not ask for separate per-contact confirmation. Apply CRM search, duplicate,
archived-record, Account-affiliation, and source-linking rules. Report contacts
created, existing contacts reused, and candidates skipped; report explicitly
when no qualifying contacts were found.

## Knowledge from a saved Document

A saved ordinary Document version is an ordinary maintenance source: the next
maintenance run of its Workspace considers it for concepts, relations, proposed answers
and decision evidence with [maintenance.md](maintenance.md). There is no separate
per-Document assessment. When the person wants it processed now, they mark it with
“Process with the agent”, and their Incoming run completes that marked work in the
same session. OrgApp lists the Document (`orgapp://documents/{document_id}`) in the OKF
`sources` of the knowledge maintenance takes from it. Templates are not maintenance sources.

Uploaded EML and MSG files also import supported direct attachments as Documents in the same Workspace, and ZIP archives import their supported files the same way. Use `get_document_email_links(documentId, versionId)` to read exact-version attachment or archive-file links, skip reasons and source backlinks. Attached emails and archives inside an archive are saved intact without recursively expanding their contents. All files remain in the original email or ZIP download.
