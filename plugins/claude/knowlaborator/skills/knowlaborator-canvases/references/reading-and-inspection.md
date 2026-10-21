# Reading a board

## Find the board

- **Shared by the person:** `get_active_context` (surface `canvas`, state `ACTIVE`) names the board, the revision the
  person sees and up to 25 selected item ids with their revisions. Its only perspective is `all`.
- **Picked in Today:** pass this chat's latest `localSelection` to `get_active_context`. An empty or unavailable local
  selection never falls back to the shared browser view.
- **Discovery:** `list_canvases` pages the boards of the person's Workspaces by title, with `query`, `workspaceId`
  and `state` (`active` or `archived`). Ids never grant access.

## `read_canvas`

`read_canvas` returns the whole board as the person may see it, on pages of up to 200 items (`nextCursor`
continues).

**The board:** its title, goal, Workspace, revision and the person's capabilities.

**Items** (`items`), each with:

- its `id` and its content `revision`, which you send back as `expectedRevision`;
- `kind`, `x`, `y`, `width`, `z`, `author` (a person, or `kind: agent` for a person's agent: "Anna's agent") and
  `can` (what *you* may move, edit and remove);
- its content by kind:
  - `note`: `{text}`;
  - `image`: asset id, title, caption, alt text and provenance (`source`: upload, paste, phone, generated or
    downloaded; `sourceUrl`);
  - `record`: the record card, with `ref` (kind, id, the revision it was added at), title, subtitle (a Knowledge
    type, a dataset), a body by family (excerpt, document page count, fields, person, email, event, ToDo), `status`,
    the verified `quotes`, and `changedSinceAdded` when the record has a newer revision;
  - `locked`: a record the person cannot read. It shows the kind and who added it, never a title. Do not ask what it
    is and do not look it up;
  - `visual`: its data as the board draws it, with resolved sources (see charts-and-sources.md).
- `lines` under the item, oldest first: text, author, an optional linked record, and `canRemove`. `pinned` names the
  pinned line, shown first.
- `likes`: count and who liked it. Likes are people's. Many likes say what the group favors; they decide nothing.
- `suggestedBy`, when a person placed the item from an agent's suggestion.
- `near`: up to four nearby item ids. They are derived from positions, never a fact.

**Suggestions** (`suggestions`): open ones and created outcomes, each with its agent, note and state. Your own are
those whose `by.membershipId` is your person's and whose kind is `agent`.

**Agents and people:** `recentAgents` are the agents active in the last ten minutes; `people` are the Workspace's
members (ids for outcome fields).

## Only what changed

Pass `afterRevision` with the revision you hold. The answer lists the items, removed ones included (`deleted`), and
the suggestions changed since, plus the board's metadata.

- `requiresFullReload: true` means more than 200 items changed: read the board again without `afterRevision`.
- Keep the last board you read and its revision.
- Read again only for an unknown board, a conflict, a newer revision you noticed, or a request that depends on what
  the person is doing now.

## Overview and images

- `includeOverview: true` adds a link to an inert SVG overview of the board as the person sees it.
- To look at an image people added, pass its `assetRevisionId` to `get_canvas_asset_file`.
- Stage and verify the overview and every image with the shared
  [file inspection](../../knowlaborator-work/references/file-inspection.md) flow, then inspect the saved file.
- Alt text and captions are authored descriptions, not verified content.
