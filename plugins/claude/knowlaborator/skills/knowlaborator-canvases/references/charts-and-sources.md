# Visuals and their sources

You send data; the board draws it with its own look. Never send coordinates, SVG, HTML, colors or code. Add a visual
with `add_visual` and change it with `update_visual` after people answer in lines. People never edit a visual's
data; they write lines, and you update the visual when you next read the board.

## Sources and estimates

The table, chart, matrix, timeline, outline and argument visuals list their `sources`, and so may your notes
(text-items.md). Each source cites a record:

- `page`: a Document page;
- `field`: a record field, for CRM records, Dataset records, projects, ToDos and org-chart records;
- or the record's exact revision alone.

```json
"sources": [
  { "id": "blau", "record": { "kind": "document", "id": "…" }, "page": 4, "label": "Blau deck p. 4" },
  { "id": "rate", "record": { "kind": "dataset_record", "id": "…" }, "field": "day_rate" }
]
```

- Values cite a source by id. A value without a source is your estimate and is shown in italics; say so in the
  visual's note.
- A table cell whose source names a `field` takes its value from the record: leave its `text` out. The board refuses
  typed figures there.
- The board pins each record's revision. When a cited record changes, the visual shows "Changed since added".
- Readers who cannot read a source's record see no label for it. Still never copy restricted content into labels.

Ids are 1–40 lower-case letters, digits, `-` or `_`, unique within their list.

## The data visuals

The outline and the argument, which carry reasoning rather than data, are in text-items.md.

**Comparison table** (≤ 8 columns, ≤ 20 rows)

```json
{ "type": "table", "title": "Three pitches side by side",
  "columns": [ { "id": "blau", "label": "Blau" }, { "id": "nord", "label": "Nordlicht" } ],
  "rows": [ { "id": "fee", "label": "Fee", "cells": [ { "text": "€84k", "source": "blau" }, { "text": "€96k", "source": "nord" } ] },
            { "id": "media", "label": "Media", "cells": [ { "text": "~€30k" }, { "text": "~€24k" } ] } ],
  "sources": [ … ] }
```

**Chart** (`mark`: `bar`, `stacked_bar`, `line` or `donut`; ≤ 4 series, ≤ 24 categories; a donut has one series).
Each series has one value per category (`null` for a gap) and optional `sources`, one source id or `null` per value.
Mark a series without sources `"estimate": true`. `unit` formats values: `{ "prefix": "€", "suffix": "k" }`.

```json
{ "type": "chart", "title": "First-year cost", "mark": "stacked_bar", "categories": ["Blau", "Nordlicht", "Feld und Form"],
  "series": [ { "id": "fee", "label": "Fee, from the decks", "values": [84, 96, 71], "sources": ["blau", "nord", "feld"] },
              { "id": "media", "label": "Media, agent's estimate", "values": [30, 24, 38], "estimate": true } ],
  "unit": { "prefix": "€", "suffix": "k" }, "sources": [ … ] }
```

**2×2 matrix** (≤ 8 points; `x` and `y` from 0 to 1)

```json
{ "type": "matrix", "title": "Cost against fit", "xAxis": "Cost", "yAxis": "Fit",
  "quadrants": { "topLeft": "Fits, affordable", "topRight": "Fits, costly" },
  "points": [ { "id": "nord", "label": "Nordlicht", "x": 0.68, "y": 0.8, "source": "nord" } ],
  "note": "Cost from the decks · fit is the agent's estimate from the selection criteria", "sources": [ … ] }
```

**Timeline** (≤ 12 entries). A `date` is `2027`, `2027-01` or `2027-04-14`, with an optional short `dateLabel`.
Entries citing a calendar event show as calendar entries, entries citing other records as record entries, and
entries without a source as your own (dashed).

**Diagram** (≤ 16 nodes, ≤ 24 links; `layout` `flow`, `tree` or `mindmap`). A node may have the `state` `done`,
`current` or `alternative`. Links join two node ids, with an optional label. The board lays it out.

**Quotes** (≤ 6 passages from any records). Send `quotes`; the board verifies each against the exact revision:

```json
{ "type": "quotes", "title": "On team stability",
  "quotes": [ { "record": { "kind": "document", "id": "…" }, "quote": "A named replacement joins two weeks before any hand-over.", "page": 12 } ] }
```

Give `prefix`, `suffix` or `occurrence` when a quote appears more than once, and `page` or `block` for long
Documents. People open each passage from its numbered bookmark.

**Checklist** (≤ 12 steps, each ≤ 200 characters). A step can be `done` and can cite one line (`lineId`, "from Clara's
line") or one item (`itemId`). Turn an open step into a ToDo suggestion when the group agrees on it.

**Summary** (≤ 6 themes and ≤ 6 open points, and a `summary` of at most 140 characters that the board shows; Read
opens the themes). Each theme has a short title, a sentence of at most 400 characters, and
`items`, the items it draws on (≤ 12), each with a short name for its chip (`label`, at most 40 characters, such as
"Budget note": a note has no title of its own). The summary replaces a written-up distillation: keep it current with
`update_visual` as the board grows.

```json
{ "type": "summary", "title": "What the board says so far", "summary": "Blau fits best; media cost is open at all three.",
  "themes": [ { "id": "cost", "title": "Cost", "text": "Feld und Form is cheapest, but media is extra at all three.", "items": [ { "itemId": "…", "label": "Feld und Form pitch" }, { "itemId": "…", "label": "Media cost note" } ] } ],
  "open": [ { "id": "cap", "title": "Open", "text": "Is media inside the €90k cap?", "items": [ { "itemId": "…", "label": "Budget note" } ] } ] }
```

**Image**: upload it and add it with `add_image` (assets-and-ingestion.md). It is not a `visual`.

`title` (≤ 120 characters) is optional on every visual. Labels and values hold at most 80 and 120 characters; notes
hold at most 300.
