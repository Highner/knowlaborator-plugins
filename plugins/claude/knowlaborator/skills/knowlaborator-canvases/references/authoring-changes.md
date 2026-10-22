# Changing a board

Every change is one `change_canvas` call: an atomic batch of 1–40 operations with an idempotency key. Either all
apply or none do; a failure names its operation, for example `operations[3] (add_line): …`.

```json
{
  "baseRevision": 41,
  "idempotencyKey": "board-0193a9f1-pitch-summary-01",
  "operations": [
    { "op": "add_note", "itemId": "0193b1e0-…", "text": "Media is extra at all three agencies", "near": "0193a7c1-…", "side": "below" },
    { "op": "add_line", "lineId": "0193b1e1-…", "itemId": "0193a7c1-…", "text": "Deck p. 12 names a replacement" }
  ]
}
```

Choose new UUIDs for `itemId`, `lineId` and `suggestionId`, and keep them for retries. Reuse an idempotency key only
for an identical retry. `baseRevision` is diagnostic: changes to different items merge.

## Operations

| Operation | Fields | Notes |
| --- | --- | --- |
| `add_note` | `itemId`, `text` (keep it ≤ 140), optional `title` (≤ 120), `details` (≤ 4,000) and `sources` (≤ 6), placement, optional `width` | Your own note, a white card marked "Anna's agent"; Read opens the details (text-items.md) |
| `update_note` | `itemId`, `expectedRevision`, `text`, optional `title`, `details`, `sources` | Only notes your person's agents wrote; send the whole note |
| `add_visual` | `itemId`, `visual`, placement, optional `width` | See text-items.md and charts-and-sources.md |
| `update_visual` | `itemId`, `expectedRevision`, `visual` | Only your own visuals; people answer with lines, you update |
| `add_image` | `itemId`, `assetRevisionId`, `source` (`generated` or `downloaded`), `sourceUrl` (https, for downloaded), `title`, `caption`, `altText`, placement | Upload first (assets-and-ingestion.md) |
| `move` | `itemId`, `x`, `y`, optional `z`, optional `expectedRevision` | Only your own notes, visuals and images |
| `remove` | `itemId`, `expectedRevision` | Only your own notes, visuals and images |
| `add_line` | `lineId`, `itemId`, `text` (≤ 500, one line), optional `record` | A short annotation under any item; a line may link one record |
| `remove_line` | `lineId` | Only your own lines |
| `suggest_record` | `suggestionId`, `record`, `note` (≤ 280), optional `quotes` | See suggestions-and-outcomes.md |
| `suggest_outcome` | `suggestionId`, `kind`, `title`, `fields`, `cited` (`itemId` + `label` ≤ 40), `note` (≤ 500) | See suggestions-and-outcomes.md |
| `withdraw_suggestion` | `suggestionId` | Only your own open suggestions |
| `set_board` | `title`, `goal` | The board's title (≤ 200) and goal (≤ 500) |

A record is `{ "kind": "document", "id": "…" }` with one of these kinds:

- `knowledge`, `decision`, `issue`, `email`, `document`, `dataset_record`;
- `crm_account`, `crm_contact`;
- `project`, `todo`, `calendar_event` (with `externalEventReference` for connected calendars);
- `org_chart_person`, `org_chart_unit`, `org_chart_position`.

The board pins the record's current exact revision for you.

## Placement

- **`near` and `side`:** give `near` (an item id) and `side` (`right`, `left`, `below`, `above`). The board puts the
  new item next to it, sliding along until nothing overlaps.
- **`x` and `y`:** give board coordinates only when the person asked for a spot.
- **Neither:** the board takes the first free space from the top left.

Place an item next to what it is about. Its position is still not a fact: a note says "about" in its text, or you
add a line to the item instead.

## Revisions and conflicts

`update_*` and `remove` carry the item's current `revision` as `expectedRevision`. Moves, lines, likes and pins do
not change an item's revision, so a person dragging a card never invalidates your update. A stale revision fails the
batch with `CANVAS_ITEM_REVISION_CONFLICT`; `conflicts` lists `{id}:{revision}` or `{id}:{revision}:deleted`. Read
the changes since your revision, rebuild the operation on the current item, and retry with a new idempotency key.

## Errors

| Code | Meaning |
| --- | --- |
| `CANVAS_INVALID` | Shape or bounds. Field errors are keyed like `operations[2].visual.rows[1].cells[0].text` |
| `CANVAS_ITEM_NOT_FOUND` | The item, line or suggestion is not on the board, or it was removed |
| `CANVAS_ITEM_REVISION_CONFLICT` | Stale revision, or an id that exists already |
| `CANVAS_SUGGESTION_RESOLVED` | A person placed, dismissed or created it already |
| `CANVAS_ACCESS_DENIED` | Not yours to change, or the person is a Viewer |
| `CANVAS_ITEM_LIMIT` | The board holds at most 1,000 items |
| `CANVAS_SUGGESTION_LIMIT` | At most 20 open suggestions |
| `CANVAS_ANCHOR_NOT_FOUND`, `CANVAS_PAGE_NOT_FOUND` | A quote that does not occur, or a page without readable text |
| `CANVAS_RECORD_UNAVAILABLE` | Your person cannot read the record |
| `CANVAS_ARCHIVED` | The board is archived |
