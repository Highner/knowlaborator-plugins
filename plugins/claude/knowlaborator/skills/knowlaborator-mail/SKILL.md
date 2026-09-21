---
name: knowlaborator-mail
description: Read connected mail and attachments, preserve an exact message, or create an unsent provider draft.
---

# Knowlaborator Mail

Connected accounts and provider references are owner-private. Keep every opaque
message, thread and attachment reference paired with the account that returned
it. Use only tools exposed by the current binding; Realm mail requires its
owner-scoped opt-in. Reads do not change provider read state.

- [reading.md](references/reading.md): account resolution, search and exact reads.
- [attachments.md](references/attachments.md): inspect one selected attachment.
- [drafts.md](references/drafts.md): an explicit draft or reply request.
- [message-ingestion.md](references/message-ingestion.md): explicit preservation
  of one message and requested attachments.

No MCP operation sends, deletes, moves, archives, labels or flags provider mail.
Triage stages proposals through its own workflow; a proposal is not execution.
