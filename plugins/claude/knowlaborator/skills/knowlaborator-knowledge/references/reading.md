# Read knowledge and existing exports

Use search_content for bounded discovery across knowledge and Documents, and
persisted Cases when kinds includes case. Read exact results through their
owning operation. Use list_knowledge for catalog pages, get_knowledge for one
record, and list_knowledge_revisions for history.

Preserve revision, lifecycle, Workspace, provenance, evidence, uncertainty,
unknown fields and redacted references. Taxonomy guidance is advisory metadata,
not organization facts. Read the compact get_workspace_knowledge_guidance
catalog only when relevant; load get_workspace_concept_guidance only for the
exact needed types in that Workspace.

When the user refers to “this”, “here”, or “my basket” in an already-open
organization conversation, call `get_active_context` without identifiers. Treat
`selected`, `focus`, and ordered `basket` as relevance pointers only, then use
the owning canonical read tools for content. If the result is stale or
ambiguous, ask the user to re-share or choose the intended OrgApp view; never
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
operation ID, then read the returned link with the client's MCP resource reader.
Follow the shared MCP file handoff instructions. Do not use a browser
downloadPath or substitute indexed content for that file.
