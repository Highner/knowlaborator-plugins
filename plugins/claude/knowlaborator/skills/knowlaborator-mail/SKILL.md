---
name: knowlaborator-mail
description: Read connected mail and attachments, preserve an exact message, create an unsent provider draft, or move an exact message to Trash.
---

# Knowlaborator Mail

Connected accounts and provider references are owner-private. Preserve every
protected message, thread and attachment reference exactly as returned. Message
and thread references already identify their account; attachment references also
bind their exact parent message. Exact mail tools do not accept account IDs.
Start with search_mail across all connected accounts; it automatically hides
messages dismissed for the caller's active organization and returns account
metadata even when no messages match. Use only tools exposed by the current
binding. Reads do not change provider read state.

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
