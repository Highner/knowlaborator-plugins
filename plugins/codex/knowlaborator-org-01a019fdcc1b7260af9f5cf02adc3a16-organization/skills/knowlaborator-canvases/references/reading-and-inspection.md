# Read and inspect canvases

If the user means the Canvas currently shared from OrgApp, call
`get_active_context` first. Require `state: ACTIVE` and `surface: canvas`, then
pass the returned selected Canvas ID and `canvasRevision` to
`get_canvas_scene`. The returned lens and `selectedCanvasItems` explain the
browser view and selection; they do not replace the complete paginated scene.

1. Use `list_canvases` for bounded authorized discovery. Optional filters are
   `workspaceId`, `state` (`active` or `archived`), `query`, `cursor` and
   `pageSize` (default 50, at most 200). Each summary carries `id`,
   `workspaceId`, `title`, `state`, `revision`, `itemCount` and
   `capabilities` (`canRead`, `canEdit`, `canManage`). Follow `nextCursor`
   unchanged until it is null; a returned ID never grants access.
2. Call `get_canvas` for the exact selected canvas. It returns metadata, the
   current `revision`, the stored lenses, `capabilities` and `archivedAt`.
3. Call `get_canvas_scene` for the items. Omit `revision` for the current
   scene, or pass an exact historical `revision` to reconstruct that state;
   `historical` is then true and removed items appear with `deleted: true`.
   Page with `cursor` and `pageSize` (at most 200) until `nextCursor` is null.
   A partial page is never a complete scene.

## Item shape

```json
{
  "id": "0193b0a2-…", "canvasId": "0193a9f1-…", "kind": "sticky",
  "itemRevision": 3, "canvasRevision": 41, "deleted": false,
  "geometry": { "x": 120, "y": 80, "width": 200, "height": 120, "rotation": 0, "z": 4 },
  "parentItemId": "0193b09c-…", "parentOrder": 2,
  "sourceItemId": null, "targetItemId": null,
  "lenses": ["context"],
  "payload": { "color": "yellow", "text": "Latency doubled after the 3.2 rollout." },
  "references": [],
  "creatorMembershipId": "…", "creatorActorKind": "member",
  "lastEditorMembershipId": "…", "lastEditorActorKind": "agent",
  "createdAt": "2026-09-01T09:12:00Z", "updatedAt": "2026-09-02T07:40:00Z"
}
```

- `itemRevision` is the object-level concurrency value; every later update or
  delete of this item must quote it exactly. `canvasRevision` is the canvas
  revision that produced this item revision.
- `parentItemId` names the frame or group that contains the item;
  `parentOrder` is its position among siblings. Containment is authoritative
  membership. An item without a parent is top-level.
- `sourceItemId` and `targetItemId` are set only on `connector` items and name
  the related items; the payload's `directed`, `label` and `type` describe the
  relation. A connector is an authoritative relationship.
- `lenses` lists the stored lens flags `context`, `questions` and
  `decisions`. `all` is a browser view, not a stored value; an unclassified
  item is valid and belongs only to `all`.
- `references` lists explicit outgoing links declared by the payload:
  `kind` is `asset`, `resource` or `source`; `targetKind` names the target
  domain; `targetRevision` is the pinned revision where one was declared.
- `payload` is the kind-specific content described in
  [authoring-changes.md](authoring-changes.md). A `decision` payload carries
  the server-owned `state` (`proposed` or `confirmed`) plus
  `confirmedByMembershipId` and `confirmedAt` when confirmed.
- `creatorActorKind` and `lastEditorActorKind` are `member` or `agent`.

## Spatial inference

Geometry, proximity, overlap, color and z-order are supportive visual context.
Two items placed side by side, stacked, or sharing a color are not thereby
related, grouped, ordered or agreed. Report only what frames, groups,
connectors, typed items and explicit references state. When a visual
arrangement suggests a relationship the structure does not record, say so as
an observation and propose an explicit connector or grouping instead of
treating it as fact.

## Overview image

`get_canvas_overview` (optional exact `revision`) returns one deterministic
inert `image/svg+xml` embedded resource with structured metadata `canvasId`,
`revision`, `mediaType`, `byteSize` and `sha256`. Images and files appear as
placeholders. Use it to orient, never as the source of item text or values;
read the scene for content.

## Exact asset originals

`get_canvas_asset_file` with `canvasId` and `assetRevisionId` returns the
exact original bytes of one asset revision as one embedded file resource with
authoritative `fileName`, `mediaType`, `byteSize`, `sha256` and, for raster
images, `width` and `height`. The shared MCP file instructions describe the
client's resource/file support. Inspect the
materialized original; no OCR, caption, alt text or thumbnail substitutes for
it. `CANVAS_ASSET_TOO_LARGE` means the original exceeds the local
materialization limit; report that rather than guessing its content.

## Distillation reads

`list_canvas_distillations` pages saved immutable artifacts (`scope` `canvas`,
`lens` or `selection`, and `sourceCanvasRevision`). `get_canvas_distillation`
returns one exact distillation with `currentRevision.content` (the seven fixed
sections and `citations`) and `sourceDigest`. Citations pin `itemId` plus
`itemRevision`; resolve them with `get_canvas_scene` at the distillation's
`sourceCanvasRevision`, not the current scene. See
[distillation.md](distillation.md) for status meanings.
