# Conversations and Workspace channels

- To start a conversation, call `list_messaging_members`, resolve exact active
  membership IDs, and call `start_conversation` with the first message. Never select a
  recipient from a display name alone.
- To reply, preserve the exact conversation ID and call `send_message`.
- Channels belong to Collaborative Workspaces, and Workspace membership is the access
  boundary. Subscription controls inbox inclusion, unread delivery and posting
  participation but never grants access. Call `set_channel_subscription` only for an
  explicit subscribe or unsubscribe request; a resumed subscription starts at the
  current channel head.
- To post, preserve the exact channel ID and call `send_channel_message`; the caller
  must be subscribed and have Contributor access. Delivery is committed synchronously.
- Reads never change read state. Call `mark_messages_read` (kind `conversation` or
  `channel`) only after reviewing the messages for the user or on an explicit mark-read
  request, never for a Teams source.
- Sender names are display snapshots; identify people by membership ID.

Administrators gain no special content access to conversations or channels. Messaging
has no user-facing edit, delete, participant-change or forwarding operation.
