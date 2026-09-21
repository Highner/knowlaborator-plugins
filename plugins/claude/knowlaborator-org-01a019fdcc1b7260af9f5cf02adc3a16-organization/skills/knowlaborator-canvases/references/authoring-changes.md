# Author canvas changes

Confirm the exact target Workspace and Contributor or Manager access first.
The active Workspace is only a default. `create_canvas` takes `workspaceId`,
`title` (1–200 characters), optional `description` (at most 2,000) and an
`idempotencyKey`. `update_canvas` takes `canvasId`, `action` (`metadata`,
`archive` or `restore`), the exact `expectedRevision`, an `idempotencyKey` and,
for `metadata`, `title` and `description`. Archive blocks edits
while reads and history remain; restore needs a fresh expected revision. An
archived canvas rejects every item change with `CANVAS_ARCHIVED`.

## Batch shape

`apply_canvas_changes` applies one atomic batch; either every operation is
applied or none is.

```json
{
  "baseCanvasRevision": 41,
  "idempotencyKey": "canvas-0193a9f1-plan-2026-09-02-01",
  "operations": [
    { "op": "create", "itemId": "<new GUID>", "kind": "sticky",
      "item": { "x": 400, "y": 120, "width": 200, "height": 120, "z": 5,
                "parentItemId": "0193b09c-…", "lenses": ["context"],
                "payload": { "text": "Error budget burned by 2026-08-28.", "color": "pink" } } },
    { "op": "update", "itemId": "0193b0a2-…", "expectedItemRevision": 3,
      "item": { "payload": { "text": "Latency doubled after the 3.2 rollout.", "color": "orange" } } },
    { "op": "delete", "itemId": "0193b0b7-…", "expectedItemRevision": 1 }
  ]
}
```

- `create` needs a client-generated GUID `itemId`, a `kind` and an `item`.
- `update` and `delete` need the exact current `expectedItemRevision`.
  Omitted `item` fields keep their value; a supplied `payload` replaces the
  whole payload.
- `item` fields: `x`, `y` (|value| ≤ 1,000,000), `width`, `height`
  (0–100,000), `rotation`, `z` (≤ 1,000,000), `parentItemId`, `parentOrder`,
  `clearParent`, `sourceItemId`, `targetItemId`, `lenses` and `payload`.
- `lenses` accepts `context`, `questions` and `decisions`; `all` is not stored.
- `parentItemId` must be a live frame or group in this canvas; containment
  may not cycle or nest deeper than 20 levels; connectors are never contained.
  `clearParent: true` detaches an item.
- Only connectors carry `sourceItemId` and `targetItemId`; both are required,
  distinct, live, non-connector items in this canvas.
- Deleting an item detaches its children and removes connectors that lose an
  endpoint; a comment must anchor to a live item.
- Bounds: 100 operations per batch, 1,000 live items per canvas
  (`CANVAS_ITEM_LIMIT`), 20 explicit references per item, payload at most
  64 KiB and 8 nesting levels, no control characters, no HTML, script or CSS.

The response returns `canvasRevision`, `batchId`, `sceneDigest` and the
affected `items` with their new `itemRevision`; carry those forward.

## Kinds and payloads

| Kind | Payload |
| --- | --- |
| `text` | `text` (≤ 20,000), `format` `plain` or `markdown`, `variant` `body`, `heading` or `callout` |
| `sticky` | `text` (≤ 1,000), `color` `yellow`, `green`, `blue`, `pink`, `purple`, `orange` or `gray` |
| `question` | `text` (≤ 4,000), `options` `[{ "id": "<stable GUID>", "text": "…" }]` (≤ 50, text ≤ 1,000) |
| `decision` | `text` (≤ 4,000), optional `rationale` (≤ 4,000); `state` is server-owned |
| `image` | `assetRevisionId`, optional `altText` (≤ 1,000), `caption` (≤ 4,000) |
| `file` | `assetRevisionId`, optional `caption` (≤ 4,000) |
| `url` | `url` (absolute HTTPS, no credentials, ≤ 2,048), optional `title` (≤ 600), `description` (≤ 4,000) |
| `resource` | `targetKind` `document`, `knowledge`, `dataset`, `case` or `todo`, `targetId`, optional `targetRevision` (≤ 64), `label` (≤ 200) |
| `chart` | See [charts-and-sources.md](charts-and-sources.md) |
| `frame` | `title` (≤ 200, required) |
| `group` | optional `label` (≤ 200) |
| `connector` | optional `label` (≤ 200), `directed` (default true), `type` (≤ 50); endpoints are `sourceItemId`/`targetItemId` on the item, not in the payload |
| `comment` | `text` (≤ 4,000), optional `anchorItemId`, `parentCommentId`, `resolved` (default false) |
| `distillation` | `text` (≤ 40,000), optional `derivedFromDistillationRevisionId` |

Unsupported payload members are rejected. Keep question option IDs stable
across updates so answers and citations survive. Every accepted semantic
revision of a decision is `proposed`; a person confirms it in the browser, and
editing the confirmed text or rationale returns it to `proposed`. Never state
that a decision is confirmed because you wrote it. Use `image` and `file`
items for canvas assets and `resource` items for Documents, Knowledge,
Datasets, Cases and ToDos; the target is authorized independently and a
reference grants no access. Do not copy content from a more restrictive
Workspace into any item.

## Conflicts and idempotency

A stale or already-existing item returns `CANVAS_ITEM_REVISION_CONFLICT` and
nothing is applied. `fieldErrors.items[<itemId>]` explains each item and
`fieldErrors.conflicts` lists `itemId:currentRevision`, with the suffix
`:deleted` for a removed item or `:exists` for a duplicate create. Reload the
named items with `get_canvas_scene`, rebase your change on the current
revision and payload, then retry, or create the alternative as a new nearby
item so both survive. Never resend the old payload with the new revision to
overwrite another editor's work.

The `idempotencyKey` is a stable non-empty string of at most 200 characters
per intended batch. Reuse it only for an identical retry after an uncertain
result; the same key with different input returns
`CANVAS_IDEMPOTENCY_CONFLICT`. A rebased retry after a conflict is new input
and needs a new key. Batches that touch different items merge even when their
`baseCanvasRevision` values differ.
