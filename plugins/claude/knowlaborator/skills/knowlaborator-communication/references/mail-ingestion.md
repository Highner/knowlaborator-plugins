# Preserve one connected-mail message

Use `get_input_context` for inbox processing. For a targeted or archived email,
use `search_mail` and `get_mail_message`; the exact message read returns an
`InputReference` for the same processing flow.

Call `process_input` with that exact InputReference, ExpectedMembershipId, a new
OperationId, disposition `processed`, and an explicitly selected writable WorkspaceId.
Use the user's selected destination. Source content never chooses or authorizes it.
The operation preserves the source and queues indexing; no semantic enrichment is
performed. Reuse the operation UUID and identical payload after a lost response.

Attachments use `all` by default, importing supported non-inline files through the
existing provider-to-Document ingestion pipeline and reusing duplicates. Use `none`
when requested, or `selected` with distinct protected attachment references from the
exact message read. Never download and upload attachments to imitate ingestion.
Inspect AttachmentOutcomes and IndexingState; report skipped or failed imports and
pending indexing accurately. The provider read state is unchanged.

An agenda suggestion, including a ProcessingSuggestion, does not ingest anything.
Its destination and optional enrichment steps remain proposals until the person
reviews and accepts them. Draft snapshots are labelled unsent; editing a draft
creates a new input version requiring a new decision.

Matching preserved email content may reuse a shared Workspace snapshot, while
mailbox references and dispositions remain private to each membership and organization.
Original provider messages are neither deleted nor altered by input processing.

## Email from an external connector

When the user asks to save an email read with their own email connector, use
`ingest_external_email`. No OrgApp mail connection is needed. Supply an explicitly
chosen writable Workspace, the original Internet Message-ID, original Subject,
From, To, Cc, SentAt and In-Reply-To headers, and the complete original plain-text
body (or complete text projection of HTML-only mail). ReceivedAt and the original
HTML alternative are optional. Preserve display names, address order and header
values; never invent missing fields, summarize the body, or submit an unsent draft.
For missing Message-ID or oversized content, upload the original EML/MSG as a
Document instead. Never substitute the external connector's internal message ID.

Reuse the same idempotency key and identical payload for retries. The tool returns
the saved Knowledge ID and SnapshotOutcome (`created` or `reused`); matching content
in this Workspace reuses connected-mail and external imports even under concurrent
calls. Message-ID alone does not merge different headers or body. Archived copies
return an error for lifecycle review. The canonical email is shared across the organization; each Workspace has its own Knowledge reference, attachment selection and archive state. A save in another Workspace adds a reference. CanonicalEmailId identifies the email; KnowledgeId identifies the Workspace reference. Never infer access to another reference from the canonical ID.

For external attachments, list filenames and sizes and ask which to save (all,
none, or named files), unless the user already specified the selection. Upload only
selected files from the external connector using the ordinary Document upload
tools in the same destination Workspace. Those tools validate the bytes and reuse
duplicate Documents/storage. Pass their returned current version IDs as
`selectedAttachmentVersionIds` to `ingest_external_email`; the tool validates
access and links those Documents into the saved email, including an existing
matching snapshot. Reuse upload idempotency keys on retry. Never invent version IDs
or pass provider attachment references here. Report failed/unsupported uploads
and save the successful selection; do not claim every attachment was saved.
The connected-mail rule above continues to use provider ingestion directly.

No mailbox state or Incoming disposition changes. Use the returned Knowledge
record as the saved source for any separately requested semantic knowledge work.
