# Save a Document or Document Template file

Use this flow for a PDF, non-macro DOC/DOCX/PPTX, PNG, JPEG, GIF, or WebP file the user wants stored in OrgApp.
Connected-mail attachments belong to [Mail ingestion](knowlaborator-skill://knowlaborator-mail/references/message-ingestion.md) and always use the
ordinary Document purpose.

Document bytes never pass through MCP arguments. Use the same direct two-step
workflow for a Document's first file and every later revision. Inspect the
local file with the client's ordinary local-file tools, but do not put its path,
bytes, upload URL, credentials, or tokens in an MCP argument or summary.

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
4. Determine the file's leaf filename, exact byte size, and lowercase SHA-256
   locally. Call `begin_document_file_upload` with those values, the exact
   Document ID, and one operation-stable idempotency key. A request declaring
   more than 104857600 bytes is rejected before transfer instructions are
   issued.
5. Immediately send only the raw file bytes to the returned short-lived HTTPS
   URL using its exact method and headers. Do not send JSON, multipart form
   data, MCP authorization, or cookies. Do not retain or repeat the URL in the
   conversation after the transfer.
6. Call `complete_document_file_upload` with the returned `uploadIntentId`.
   OrgApp reauthorizes the exact upload, verifies actual size, SHA-256, and file
   format, promotes the immutable version, removes staging, and queues
   processing. File storage does not wait for OKF analysis.
7. If transfer or completion is interrupted or uncertain, reuse the existing
   logical Document. Retry `begin_document_file_upload` with the same
   idempotency key and file metadata while the intent remains valid, transfer the
   same bytes if needed, then retry `complete_document_file_upload` with the same
   upload ID. If the one-hour intent expires before accepting bytes, begin a
   replacement intent with a new idempotency key against the same logical
   Document. Never create a duplicate Document merely because the outcome is
   uncertain.
8. Use `get_document_version` to check processing. When the saved bytes must be
   verified or semantically assessed, call `get_document_file` and inspect the
   exact original through the client's resource/file support.
   Never use indexed chunks, preview text, or metadata as
   a substitute for the actual file. A Playbook may link a Template only after
   its version is current and successfully processed.

## Extract CRM contacts from an ordinary Document

Successful ingestion of an ordinary Document includes bounded CRM contact
extraction. Templates never trigger this step. After reading the exact current
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

Route every selected candidate through `$knowlaborator-crm`. The explicit
Document-ingestion request authorizes these high-confidence contact writes, so
do not ask for separate per-contact confirmation. Apply CRM search, duplicate,
archived-record, Account-affiliation, and source-linking rules. Report contacts
created, existing contacts reused, and candidates skipped; report explicitly
when no qualifying contacts were found.

## Assess an ordinary Document for OKF

Templates do not require OKF ingestion. For an ordinary Document, file saving
and semantic assessment are deliberately separate so analysis cannot delay or
invalidate the stored file.

1. Wait until the saved version is the Document's exact `currentVersionId`,
   check it through `get_document_version`, then call `get_document_file` and
   inspect the original through the client's resource/file support.
2. Identify durable atomic concepts and search each stable subject. Prepare a
   bounded assessment with `candidates`, or `no_durable_knowledge` with empty
   arrays. Every create or update body must include the exact provenance URI
   `orgapp://documents/{document_id}`.
3. Call `ingest_document_okf` with the exact Document and current-version IDs.
   A complete assessment removes that version from the pending list. A partial
   failure remains pending; correct it and retry the same non-file operation
   without saving the file again.

To find existing work, page through `list_documents` with `purpose=document`
and `okfIngestionPending=true`; do not assume one page is complete.
