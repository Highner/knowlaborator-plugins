# Charts and their sources

A `chart` item is a bounded declarative specification plus the data snapshot
it renders. Nothing in the payload is executed; the server rejects HTML,
script, CSS, remote loads and arbitrary color values. Create or update it
through `apply_canvas_changes` like any other item.

## Payload

```json
{
  "title": "p95 latency by week",
  "mark": "line",
  "xLabel": "Week",
  "yLabel": "ms",
  "series": [
    { "id": "eu", "label": "EU", "color": "series-1",
      "points": [ { "x": "2026-W33", "y": 412 }, { "x": "2026-W34", "y": 838 } ] },
    { "id": "us", "label": "US", "color": "series-2",
      "points": [ { "x": "2026-W33", "y": 390 }, { "x": "2026-W34", "y": 401 } ] }
  ],
  "sources": [
    { "kind": "dataset", "id": "0193a7c0-…", "revision": "0193a7c1-…" },
    { "kind": "canvas_item", "id": "0193b0a2-…", "revision": 3 }
  ],
  "refresh": {
    "datasetId": "0193a7c0-…",
    "query": { "fields": ["week", "region", "p95_ms"], "filters": [], "sort": [], "pageSize": 200 }
  }
}
```

- `mark` is `bar`, `line`, `area`, `scatter` or `donut`.
- `title`, `xLabel` and `yLabel` are optional, at most 200 characters.
- `series` holds 1–50 entries. Each needs a unique `id` (1–64 characters), a
  `label` (≤ 200), a `points` array and an optional `color` token `series-1`
  through `series-8`. Across all series a chart stores at most 2,000 points.
- Each point has a finite numeric `y` and an `x` that is either a finite
  number or a string of at most 200 characters.
- `sources` is required with 1–20 entries. Each has `kind` `canvas_item`,
  `knowledge` or `dataset`, a GUID `id` and an optional `revision` (string or
  number, ≤ 64 characters). Sources become explicit item references and count
  toward the 20-reference limit.
- `refresh` is optional: a Dataset GUID plus a bounded query object
  (≤ 8,192 characters serialized).

## Snapshot semantics

The chart always stores the exact values it rendered. Readers of a historical
revision see the numbers that were on the canvas then, not a live query. A
refresh recipe is documentation of how the snapshot was produced; it does not
run by itself. Refreshing means running the cited query again and submitting a
new `update` operation with the new `series` and the exact
`expectedItemRevision`. That creates a new item revision and a new canvas
revision, so the history shows before and after.

## Building from a Dataset

Use `$knowlaborator-datasets` for the data: inspect the Dataset with
`get_dataset` and `get_dataset_schema`, call `validate_dataset_query` and then
`query_dataset_records` with bounded fields, filters, sorts and page size.
Cite the Dataset as `{ "kind": "dataset", "id": "<datasetId>", "revision": "<schemaRevisionId>" }`
so the values are pinned to the schema revision they were read under. Map
record values into points yourself; the server does not infer charts from
records. Keep the `refresh.query` identical to the validated query you ran.

Cite a Knowledge record as `{ "kind": "knowledge", "id": "<knowledgeId>", "revision": "<version>" }`
when the numbers come from a stated claim. Cite a canvas item as
`{ "kind": "canvas_item", "id": "<itemId>", "revision": <itemRevision> }`
when the values were typed on the canvas, for example in a sticky or text
item. When a source is another chart, cite that chart's item and revision.

## Source boundaries

Every source must be a canvas item of this canvas or a Knowledge record or
Dataset the current user can read in this same Workspace. The server
validates declared sources, but it cannot detect values that were copied by
hand. Never transcribe numbers, labels, quotes or summaries from a more
restrictive Workspace, a private mail or Teams message, or any source you may
not cite into a chart or into any other item. If the only evidence lives
somewhere you cannot cite, say so and leave the chart unmade.
