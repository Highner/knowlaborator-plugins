---
name: knowlaborator-canvases
description: Read, author, and distill shared Workspace canvases with exact revisions, explicit relationships, safe charts, and canvas-owned assets.
---

# Knowlaborator Canvases

## Fast path

- For “this canvas” or its selection, call `get_active_context` once with no
  identifiers. Require `ACTIVE` on surface `canvas`. Its canvas revision, lens
  and up to 25 selected item revisions are coordinates, not item payloads. If
  the context is missing, stale or ambiguous, ask the user to share or choose
  the view; never guess from recent canvases.
- To read that shared view's items, call `get_canvas_scene` at the returned
  canvas revision. For an item edit, read the **current** scene instead, compare
  affected item revisions with the shared selection, then apply one batch.
  Read only the pages needed for a targeted task; read every page for a whole
  scene. If an item changed since sharing, reconcile before writing and read
  the pinned scene if its original payload is needed.
- With a known canvas ID, call `get_canvas_scene` directly for item content.
  Use `list_canvases` only for discovery. Use `get_canvas` when metadata,
  capabilities or lifecycle state is needed, including before `update_canvas`.

Read only the reference needed for the requested operation:

- [decision-links.md](references/decision-links.md): canonical exact-revision Knowledge decision links and standing.
- [reading-and-inspection.md](references/reading-and-inspection.md): discovery,
  scenes, overview, exact assets and distillation reads.
- [authoring-changes.md](references/authoring-changes.md): metadata and item
  writes, item kinds, payloads, conflicts and idempotency.
- [charts-and-sources.md](references/charts-and-sources.md): chart citations
  and source refreshes.
- [assets-and-ingestion.md](references/assets-and-ingestion.md): canvas-owned
  uploads and explicit Document promotion.
- [distillation.md](references/distillation.md): frozen-source distillation
  and Knowledge-publication preparation.

A canvas belongs to one exact Workspace; IDs and active-context sharing grant
no access. `apply_canvas_changes` rechecks permission and exact item revisions.
On `CANVAS_ITEM_REVISION_CONFLICT`, reload the affected current items and
rebase or keep both as separate items; never overwrite another editor's work.
Preserve exact IDs, revisions, cursors and idempotency keys.

Frames, groups, connectors, typed items and explicit references are the
authoritative relationships. Geometry, proximity, overlap, color and z-order
are visual context only. `all`, `context`, `questions` and `decisions` filter
the same items; they are not separate canvases. Never copy material from a
more restrictive Workspace into a canvas. An out-of-scope lookup that returns
`CANVAS_ACCESS_DENIED` is never retried through another route.

Viewer reads; Contributor or Manager creates and changes. Agents may draft or
revise a proposed decision but never confirm one. Decision confirmation,
Knowledge publication, uningestion, deletion and presence are browser actions
without MCP tools. `prepare_canvas_knowledge_publication` only stores a
candidate. Call `ingest_canvas_asset` only when explicitly asked for that exact
ingestion. For an existing Document image or file, use a `resource` item
referencing its Document ID; the browser renders its authorized preview. Look
for the existing Document before uploading a canvas asset, and do not search
the web for a replacement image.
