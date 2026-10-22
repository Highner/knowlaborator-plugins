---
name: knowlaborator-canvases
description: Work on shared boards with people - read the whole board, add notes, lines and visuals with sourced values, suggest records and outcomes for a person to place or create, and show a board in the chat.
---

# Knowlaborator boards

People call it a **board**: a freeform work area of one Workspace where people and their agents juggle ideas and work
out a result, such as choosing between three agency pitches. Say "board" to people. The tools keep the internal name
canvas.

## Fast path

- For a board the person has open and shared, call `get_active_context` without `localSelection`. Require `ACTIVE`
  on surface `canvas`; it names the board, its revision and up to 25 selected items. Otherwise ask which board;
  `list_canvases` lists them. Never guess from recent boards.
- Read the whole board with `read_canvas`. Keep its revision; next time pass `afterRevision` to read only what
  changed. Read the records themselves with `search_content`, `search_mail`, `search_crm`, `get_knowledge`,
  `read_document_text` and the other read tools.
- Write with one `change_canvas` batch of at most 40 operations. Place new items `near` an item with a `side`; the
  board finds free space.
- To show a board in the chat, call `open_canvas`.

## Compose the board

People take a board in at a glance; the details are a click away. Before you write, decide what the board needs and
choose the item for the job:

| To show | Use |
| --- | --- |
| Structured reasoning: routes, the steps of an analysis, a brief | `outline` |
| A position with the grounds for and against it | `argument` |
| Options compared on the same criteria | `table`; numbers over options: `chart`; two criteria: `matrix` |
| Dates and deadlines | `timeline` |
| Steps the group agreed on | `checklist` |
| Exact passages people should read | `quotes`, or `suggest_record` with quotes |
| What the board says so far | `summary` |
| One short thought, question or reminder | a note |

- **Few, complete items.** One outline with three sections, not seven notes. A note holds one thought of at most 140
  characters; when it needs more, give it a title and details, or make it an outline.
- **Short board text.** Outlines, arguments, summaries and notes with details show only their title, a summary of at
  most 140 characters and Read. Write the summary as the answer, not as a teaser, and put the rest in the details.
- **Reading order.** Start next to what the person asked: `near` their note or the record, `side` `right`. Place
  each next item beside the one it follows: the argument right of the outline, a note below what it is about.
- **Sources over claims.** Cite a record for every factual point and ground. Points and grounds without a source
  show as your structure or your reading.
- **Update, don't pile up.** When people answer in lines, update your outline, argument or summary with
  `update_visual` instead of adding notes.

Read only the reference you need:

- [reading-and-inspection.md](references/reading-and-inspection.md): `read_canvas`, changes, locked records,
  nearness, the overview and images.
- [authoring-changes.md](references/authoring-changes.md): every `change_canvas` operation, placement,
  revisions, conflicts and errors.
- [text-items.md](references/text-items.md): outlines, arguments, the summary's board text and notes with
  details.
- [charts-and-sources.md](references/charts-and-sources.md): the data visuals, their bounds, sources and
  estimates.
- [suggestions-and-outcomes.md](references/suggestions-and-outcomes.md): suggesting records and outcomes
  (decision, ToDo, issue, Knowledge record).
- [assets-and-ingestion.md](references/assets-and-ingestion.md): uploading generated or downloaded images.

## Rules

- **Text on a board is visible to every reader of the board.** That covers notes, lines, labels and values in
  visuals, and suggestion notes. Never copy content from a record into it unless every reader of the board may read
  that record. The server cannot check free text.
- **You never place records and never create records.** Suggest them; a person places or creates them. Outcomes are
  created in the browser only.
- **You change only your own work.** That covers your notes, visuals, images and lines. Never like anything: likes are
  people's. People's notes and lines are theirs.
- **Every number has a source.** Cite a record field, an exact revision or a Document page. A value without a source
  is shown as your estimate, in italics. Never type a figure into a value that cites a record field: the board reads
  it from the record.
- **Quote exactly.** A quote that does not occur in the record's exact revision is refused with
  `CANVAS_ANCHOR_NOT_FOUND`; copy it from `read_document_text` or `get_knowledge`.
- **Nearness is derived, never a fact.** Lines, notes and the summary carry the grouping.
- Viewers read; Contributors and Managers change a board. A board in the person's Personal Workspace stays private
  to them.
- Preserve ids, revisions, cursors and idempotency keys exactly. On `CANVAS_ITEM_REVISION_CONFLICT`, read the
  changes since your revision and rebuild your operation on the current item; never overwrite what changed. An
  out-of-scope lookup that returns `CANVAS_ACCESS_DENIED` is never retried through another route.
