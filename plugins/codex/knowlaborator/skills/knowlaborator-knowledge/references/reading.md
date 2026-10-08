# Read knowledge and existing exports

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
revisions. It has no resource-kind filter. Read exact results through their owning
operation. Use list_knowledge for
catalog pages, get_knowledge for one record, and list_knowledge_revisions for history.
Use list_knowledge_relations for one record's typed relations, and list_knowledge with
relatedKnowledgeId and relationType for questions such as which products contain a raw
material. An unavailable related record is one you cannot read; do not guess it.

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
catalog of the Workspace's concept types only when relevant; load
get_workspace_concept_guidance only for the exact needed types in that Workspace.

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

Use get_okf_operation for an already known operation. When an export is ready
and its actual generated file is needed, use get_okf_export_file with the exact
operation ID, then stage and verify the exact original using the shared
[file inspection](../../knowlaborator-work/references/file-inspection.md) flow.
Do not use a browser
downloadPath or substitute indexed content for that file.

For substantive knowledge source choice, conditionally read the shared
[navigation-learning procedure](navigation-learning.md). Skip it for exact known-record
reads; learning grants no domain mutation or broader task authority.
