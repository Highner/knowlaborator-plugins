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

Read only the reference for the requested operation:

- [reading-and-inspection.md](references/reading-and-inspection.md): discovery,
  metadata, paginated scenes, SVG overview, exact asset files, distillation reads.
- [authoring-changes.md](references/authoring-changes.md): create or update a
  canvas and apply item batches with the fourteen kinds and their payloads.
- [charts-and-sources.md](references/charts-and-sources.md): chart items, data
  snapshots, source citations and refresh recipes.
- [assets-and-ingestion.md](references/assets-and-ingestion.md): canvas-owned
  uploads and explicit promotion to a Document.
- [distillation.md](references/distillation.md): manual and canonical
  distillation and Knowledge-publication preparation.

Viewer reads; Contributor or Manager creates and changes. Agents may draft or
revise a proposed decision but never confirm one. Decision confirmation,
Knowledge publication, uningestion, automation enable/disable, deletion and
presence are browser actions with no MCP tool. Call `ingest_canvas_asset` only
when the user explicitly asked for that exact ingestion.
`prepare_canvas_knowledge_publication` stores a candidate; it never publishes.
Never copy material from a more restrictive Workspace into a chart, item or
distillation. In the restricted automation session the context tools are
Workspace-scoped by the server (the canvas's Workspace plus exactly referenced
resources); an out-of-scope lookup fails with `CANVAS_ACCESS_DENIED` by design
and is never retried through another route.

Every update or delete carries the exact current item revision. A
`CANVAS_ITEM_REVISION_CONFLICT` means reload the named items and retry, or keep
both as a new nearby item; never overwrite. Preserve exact IDs, revisions,
cursors and idempotency keys.
