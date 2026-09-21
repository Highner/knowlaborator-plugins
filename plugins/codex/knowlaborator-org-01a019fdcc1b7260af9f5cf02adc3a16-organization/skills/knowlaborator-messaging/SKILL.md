---
name: knowlaborator-messaging
description: Read conversations and channels, manage native read or subscription state, or send supported messages.
---

# Knowlaborator Messaging

For native reads, use list_conversations and get_conversation for an exact
participating conversation; use list_channels and get_channel for Workspace
channels. These reads never change read or subscription state. Preserve cursors,
sourceKind, safe sender metadata and delivery state. pending is queued;
delivery_unknown is not confirmation and must not be automatically resubmitted.

Read [teams.md](references/teams.md) for subscribed Microsoft Teams channels.
Read [operations.md](references/operations.md) only for native state changes,
conversation creation, sending or external-channel setup and delivery details.
For a selected external-channel attachment, use get_channel_attachment with the
exact channel and attachment IDs returned together, then follow the shared MCP
file instructions. External sender names are not OrgApp membership proof.
