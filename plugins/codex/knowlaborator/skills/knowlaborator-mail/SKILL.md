---
name: knowlaborator-mail
description: Read connected mail and attachments, preserve an exact message, create an unsent provider draft, or move an exact message to Trash.
---

# Knowlaborator Mail

Connected accounts and provider references are owner-private. Keep every opaque
message, thread and attachment reference paired with the account that returned
it. Use only tools exposed by the current binding; Realm mail requires its
owner-scoped opt-in. Reads do not change provider read state.

- [reading.md](references/reading.md): account resolution, search and exact reads.
- [attachments.md](references/attachments.md): inspect one selected attachment.
- [drafts.md](references/drafts.md): an explicit draft or reply request.
- [message-deletion.md](references/message-deletion.md): user-requested deletion
  of an exact message by moving it to Trash or Deleted Items.
- [message-ingestion.md](references/message-ingestion.md): explicit preservation
  of one message and requested attachments.

`delete_mail_message` moves only the requested exact message to the provider's
Trash or Deleted Items folder. No MCP operation permanently deletes mail or
sends, archives, generally moves, labels or flags provider mail.
