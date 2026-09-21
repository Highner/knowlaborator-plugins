---
name: knowlaborator-canvases
description: Read, author, and distill shared Workspace canvases with exact revisions, explicit relationships, safe charts, and canvas-owned assets.
---

# Knowlaborator Canvases

A canvas belongs to one exact Workspace; canvas, item, asset and distillation
IDs grant no access. The lenses `all`, `context`, `questions` and `decisions`
are filters over the same items, never lanes, states or separate canvases.
Frames, groups, connectors, typed items and explicit references are the
authoritative relationships. Geometry, proximity, overlap, color and z-order
are supportive visual context only; never derive a fact from them.

When the user refers to “this canvas”, “the current canvas”, or its current
selection, first call `get_active_context` with no identifiers. Continue only
when it returns `ACTIVE` for surface `canvas`. Its selected coordinate pins the
canvas ID and exact current numeric revision; its view pins the active lens and
up to 25 selected item IDs with exact item revisions. Read that snapshot with
`get_canvas_scene` using the returned canvas ID and revision. A no-context,
stale, or ambiguous result requires the user to share or choose the intended
view; never guess from recent canvases.

Read only the reference for the requested operation:

- [reading-and-inspection.md](references/reading-and-inspection.md): discovery,
  metadata, paginated scenes, SVG overview, exact asset files, distillation reads.
- [authoring-changes.md](references/authoring-changes.md): create or update a
  canvas and apply item batches with the fourteen kinds and their payloads.
- [charts-and-sources.md](references/charts-and-sources.md): chart items, data
  snapshots, source citations and refresh recipes.
- [assets-and-ingestion.md](references/assets-and-ingestion.md): canvas-owned
  uploads and explicit promotion to a Document.
- [distillation.md](references/distillation.md): frozen-source distillation and
  Knowledge-publication preparation.

Viewer reads; Contributor or Manager creates and changes. Agents may draft or
revise a proposed decision but never confirm one. Decision confirmation,
Knowledge publication, uningestion, deletion and
presence are browser actions with no MCP tool. Call `ingest_canvas_asset` only
when the user explicitly asked for that exact ingestion.
`prepare_canvas_knowledge_publication` stores a candidate; it never publishes.
Never copy material from a more restrictive Workspace into a chart, item or
distillation. An out-of-scope lookup fails with `CANVAS_ACCESS_DENIED` by design
and is never retried through another route.

Every update or delete carries the exact current item revision. A
`CANVAS_ITEM_REVISION_CONFLICT` means reload the named items and retry, or keep
both as a new nearby item; never overwrite. Preserve exact IDs, revisions,
cursors and idempotency keys.

Active-context sharing is a pointer, not edit permission. Before changing a
shared canvas, reload its current metadata and affected live items, then call
the ordinary permission-checked `apply_canvas_changes` or `update_canvas`
operation with the fresh base canvas and item revisions. The shared revision
remains useful for interpreting what the user meant even if later edits have
advanced the live canvas.
