# Suggesting records and outcomes

You never place a record on a board and never create one. You suggest, and a person decides. Open suggestions appear
in the board's suggestion column, under Outcomes and Records, with your note and "Anna's agent". A board holds at
most 20 open suggestions; withdraw stale ones with `withdraw_suggestion`. A person sees a suggestion only if they
can read everything it references.

## A record

```json
{ "op": "suggest_record", "suggestionId": "0193c0a1-…", "record": { "kind": "document", "id": "…" },
  "note": "p. 5 shows the launch needs €12k more",
  "quotes": [ { "record": { "kind": "document", "id": "…" }, "quote": "The launch phase adds €12,000 for production.", "page": 5 } ] }
```

- `note`: why the record matters here, at most 280 characters. "Place" puts the record on the board with your note
  as its first line.
- `quotes`: up to six exact passages of the record itself. They are verified now and become its numbered bookmarks.
- Find records with `search_content`, `search_mail`, `search_crm` and the other read tools.
- An email of the person's mailbox cannot be suggested. The person adds it with `@` and shares it with the board.

## An outcome

```json
{ "op": "suggest_outcome", "suggestionId": "0193c0a2-…", "kind": "todo", "title": "Ask Blau for two references",
  "fields": { "kind": "todo", "workspaceId": "…", "assigneeIds": ["…"], "dueDate": "2026-10-28", "notes": "From Clara's line on the Blau pitch deck." },
  "cited": [ { "itemId": "…", "label": "Blau pitch deck" } ], "note": "From Clara's line on the Blau pitch deck" }
```

- **Common:** `note` is your reason (at most 500 characters). `cited` names the board items it draws on (at most
  24), each with a short name for its "Draws on" chip (`label`, at most 40 characters). `fields.workspaceId` is a Workspace where your person is a Contributor or Manager.
- **The kinds and their fields:**
  - `decision`: `statement` (what is decided), `situation` (`needed`, `discussing`, `waiting`, `agreed` or
    `documented`), `waitingFor` (when waiting) and `reason`. A decision needs at least one cited record item, which
    becomes its source. The decision is proposed; review follows the Workspace's rules.
  - `todo`: `notes`, `assigneeIds` (membership ids from `people`) and `dueDate`.
  - `issue`: `context`, `consequence` (what happens while it stays open), `settledWhen`, `followUpMembershipId` and
    `reviewOn`. Cited records become its evidence.
  - `knowledge`: `type` (a type of the Workspace's type list) and `body` (Markdown).
- **Creating:** the person creates the outcome in the browser, after editing the fields if they want. It is created
  through its own feature, links back to the board, and appears as a card with your reason as its first line. Create
  can't be undone on the board.
- **Missing fields:** you may leave a required field empty when you cannot know it; the person's Edit dialog asks
  for it.
