# Read knowledge and existing exports

For topical and status questions, start with `search_content`, then read selected
sources through their owning exact-read tools. Do not routinely follow a topical
search with `list_knowledge` or scan a whole Workspace to answer one topic.

Use search_content for bounded discovery across knowledge, Documents, and active
Dataset metadata and current active Dataset records. Record hits use keyword matching
across current scalar values, visible reference labels, and Dataset names. Up to
32 distinct terms are separated by whitespace, slashes, or list punctuation.
Partial matches are ranked by how many terms match, so an address and a person
name can match separate fields or linked labels even when a topic term is absent.
Typed number and date literals retain exact matching. Metadata retains hybrid search. Records share the content limit and Workspace filter, and
changedFrom/changedUntil apply to their update times. Document-scoped searches
exclude records. A `dataset_record` hit carries its Dataset name in HeadingPath
and `dataset://datasets/{datasetId}/records/{recordId}/revisions/{revisionId}`
in ResourceUri. Read full values with get_dataset_record, or the exact revision
with list_dataset_record_revisions and its revisionId; a discovery hit is not the
full record. includeHistorical does not include superseded or archived record
revisions. To keep only some kinds, pass `kinds` (`knowledge_object`,
`document_chunk`, `dataset`) or `resourceKinds` (such as `dataset_record`,
`crm_contact`, `project`, `todo`, `calendar_event`); given one, the other's kinds
are left out. A `document_chunk` hit's ResourceUri starts with
`dms://documents/{documentId}`. Read exact results through their owning
operation. A knowledge or document hit carries its one-sentence `description` when it
has one, and a document's search matches it too; use it with the snippet to choose what
to open. Use get_knowledge for one
record and list_knowledge_revisions for history.

Browse knowledge like the folders of an OKF bundle with `list_knowledge`, and only
for inventories and systematic reviews; search returns bounded ranked matches and
cannot enumerate the catalog.

1. Without `type` it returns the folders: one per concept type with readable records,
   with its `pathPrefix`, a short `definition`, `count`, `workspaceCounts` and
   `lastUpdatedAt`. `unlisted: true` marks a stored type that is on no list.
2. With `type` (a folder's type, a type name or an alias) it returns that folder,
   whose `count` is the total, and its records by title: `id`, `workspaceId`,
   `title`, `description`, lifecycle `status`, `updatedAt` and `decisionStanding`
   when set. Follow `nextCursor` while present; do not repeat a page with a larger
   size.
3. `workspaceId` narrows both levels to one Workspace. `changedFrom` keeps records
   updated since then, so counts show what changed per folder.

A folder entry is not the full record: use `get_knowledge` before answering from its
contents or editing. A missing description means none was written. Keep lifecycle
`status` separate from `decisionStanding` (a proposal is not a final decision).

Use list_knowledge_relations for one record's typed relations, filtered by
relationType to enumerate, for example, all products containing a raw material. It
reads a knowledge record by `knowledgeId`, or a Document, contact, account or Dataset
record by `record` (`kind` and `id`). Each relation names the other end's `otherKind`
and `otherId`; open a Document with get_document, a contact with get_crm_contact, an
account with get_crm_account and a Dataset record with get_dataset_record. To find
related knowledge records matching a topic, use search_content with relatedKnowledgeId
and relationType. An unavailable related record is one you cannot read; do not guess it.

`get_knowledge` returns the record's OKF frontmatter in `okf` (`type`, `title`,
`description`, `tags`, `resource`, OKF `status`, `stale_after` and `sources`) and
one top-level `bodyMarkdown` with record fields, `version`,
and `currentRevision` identifiers and provenance. Decision details and authorized
relations appear once; rendered HTML, the repeated revision snapshot and the parsed
email copy are omitted. Metadata is omitted by default, not necessarily empty: set
`includeMetadata=true` when those fields are needed, and before editing a record.
For system `EmailMessage` snapshots, `emailHtmlOmitted=true` means the reading body
excludes the stored HTML section; plain text, quoted history, headers and source links
remain. Use `list_knowledge_revisions` for canonical snapshots and history. Preserve
the returned exact revision ID; a reading projection never changes the stored source.

Search also returns independently authorized live calendar title matches, including
hidden calendars, from seven days back through thirty days ahead by default.
Knowledge Workspace filters do not hide related personal calendar events. Supply
both calendarFromDate and exclusive calendarToDate for another range, up to 62 days.
Respect CalendarFailure, source failures, MoreAvailable, and MoreSourcesAvailable.
Empty title matches do not prove absence; verify appointments with an exact-date
list_calendar_events read before suggesting a calendar change.

Preserve revision, lifecycle, Workspace, provenance, evidence, uncertainty,
unknown fields and redacted references. Taxonomy guidance is advisory metadata,
not organization facts. Read the compact get_workspace_knowledge_guidance
catalog of the Workspace's concept types only when relevant; pass its `type`
only for the exact types needed in that Workspace.

When the user refers to “this”, “here”, or “my basket” in an already-open
organization conversation, call `get_active_context` without identifiers. Treat
`selected`, `focus`, and ordered `basket` as relevance pointers. The basket
combines items collected in the person's open OrgApp browser and Today desk. When
`explorerSceneState` is `available`, use `explorerScene` directly to describe
the visible graph's titles, excerpts, relationships, expanded cluster and
clicked marker; do not rerun a graph query for the same view. Use
the owning canonical read tool when full content, provenance, evidence or an
edit is needed. If there is no active view or the result is stale or ambiguous,
ask the user to re-share or choose the intended OrgApp view; never
pick the newest candidate. A `semantic` relationship is similarity, not fact.

To guide the shared Explorer, first name the exact Knowledge record or Document
and proposed select, focus, or preview action, then obtain fresh confirmation
and call `present_resource_in_active_context` with that exact coordinate and
`confirmed=true`. Omit fresh confirmation only while the user has visibly
enabled Follow Codex. Report success and preview precision only from the
browser acknowledgement; queued, expired, unavailable, or unacknowledged
commands are not success.

OKF export and import run in the browser's Organization Settings (ADR 0195). When the
person needs an export, point them there; never substitute indexed content for the
exported file.

For substantive knowledge source choice, conditionally read the shared
[navigation-learning procedure](navigation-learning.md). Skip it for exact known-record
reads; learning grants no domain mutation or broader task authority.
