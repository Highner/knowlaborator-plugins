# Outlines, arguments and notes with details

The board shows a text item as a compact card: its kind line, its title, a summary of at most 140 characters (an
argument shows the balance of its grounds and your reading instead), one line of counts such as "3 sections · 5
sources", and **Read**. Read opens the whole item in a dialog, full screen on a phone. Write for that: the summary
carries the answer, and the rest is for the people who read on.

Text items are the outline, the argument, the summary visual and your notes with details. Everything in them is
visible to every reader of the board.

## Outline

Use an outline for structured reasoning: the routes out of a contract, the steps of an analysis, a brief. Add one
outline instead of several notes.

- `title` (≤ 120) and `summary` (≤ 140) are required. Write the summary as the answer: "Three routes: notice now ends
  it on 30 June 2027, good cause needs a warning first, or a negotiated buy-out."
- `sections`: 1–8, each with a `heading` (≤ 80) and 1–8 `points` (≤ 300 each). A point may cite one `source`; a point
  without one is your own structure and shows as plain text.
- `open`: up to 4 open points (≤ 200 each), without sources.
- `sources`: the records the points cite, as for the data visuals (a Document `page`, or the record's exact revision;
  no `field`).

```json
{ "type": "outline", "title": "Ways out of the Blau agreement",
  "summary": "Three routes: notice now ends it on 30 June 2027, good cause needs a warning first, or a negotiated buy-out.",
  "sections": [
    { "heading": "Ordinary notice", "points": [
      { "text": "Six months' notice to the end of a contract year.", "source": "deal" },
      { "text": "Given now, it ends the agreement on 30 June 2027, after the campaign starts." } ] },
    { "heading": "Good cause", "points": [
      { "text": "Two missed delivery dates, in May and June.", "source": "clara" },
      { "text": "Clause 9.3 asks for a written warning with a deadline first.", "source": "deal" } ] },
    { "heading": "Negotiated exit", "points": [ { "text": "Blau may accept a buy-out of the remaining minimum volume." } ] } ],
  "open": [ "Does the June delay count as the second miss, or was the date moved?" ],
  "sources": [
    { "id": "deal", "record": { "kind": "document", "id": "…" }, "page": 9, "label": "Blau framework agreement, p. 9" },
    { "id": "clara", "record": { "kind": "email", "id": "…" } } ] }
```

## Argument

Use an argument for a position and the grounds for and against it.

- `position` (≤ 160) is required and is the card's title. Leave `title` out.
- `for` and `against`: up to 6 grounds each, at least one in total. A ground has `text` (≤ 300), a `strength` you
  assign (`strong`, `medium` or `weak`) and may cite one `source`. A ground without a source is your own reading and
  shows in italics.
- `open`: up to 4 open questions (≤ 200 each).
- `reading` (≤ 300, optional): your view, always labelled "Agent's reading", never stated as a fact.

```json
{ "type": "argument", "position": "We can end the agreement for good cause now",
  "for": [ { "text": "Blau missed both delivery dates, in May and in June.", "strength": "strong", "source": "clara" },
           { "text": "Clause 9.3 makes the delivery dates binding.", "strength": "medium", "source": "deal" } ],
  "against": [ { "text": "We have not sent a written warning yet.", "strength": "strong" },
               { "text": "Our briefing for the June assets was late.", "strength": "medium", "source": "ben" } ],
  "open": [ "Was the June date moved in writing?" ],
  "reading": "Not yet. Send a written warning that names both dates and sets two weeks.",
  "sources": [ … ] }
```

## Summary visual

The summary visual (charts-and-sources.md) shows only its `summary` (≤ 140) on the board, or its first theme when it
has none. Give it one.

## Notes with details

Your note is a white card marked with your person's agent; people's notes are yellow stickies.

- `text` is what the board shows. Keep it to one thought of at most 140 characters; a longer text is cut to three
  lines.
- When the thought needs more, add `title` (≤ 120) for the dialog, `details` (≤ 4,000) and up to 6 `sources`. With
  more than a few points of structure, use an outline instead.
- `details` use the board's small text format: paragraphs separated by a blank line, `**bold**`, lines starting with
  `- ` or `1. ` as lists, and `[^id]` markers that cite one of the note's sources and show as numbered references.
  Links, headings, HTML, images and code are not part of it and show as plain text.
- `sources` are `{ id, record, page?, label? }`, as for visuals. Every marker names one of them.

```json
{ "op": "add_note", "itemId": "…", "near": "…", "side": "below",
  "text": "A written warning needs three things before we can end it for good cause.",
  "title": "Before we end it for good cause",
  "details": "Clause 9.3 asks for a written warning first.[^deal]\n\n1. Name both missed dates.\n2. Set a deadline of two weeks.\n3. Send it by registered letter.\n\n**If Blau misses again**, end the agreement for good cause.",
  "sources": [ { "id": "deal", "record": { "kind": "document", "id": "…" }, "page": 9 } ] }
```

`update_note` takes the same fields with the note's `expectedRevision`; send the whole note again.
