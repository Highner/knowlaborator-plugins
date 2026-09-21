# Read one connected-mail attachment

Call `get_mail_message` first and use its exact account, message, and attachment
references. Call `get_mail_attachment` only for the one attachment the user
selected. It returns an embedded binary resource; never open siblings
speculatively or access the underlying provider directly. Follow the shared MCP
file handoff instructions. Account resolution and partial failures follow
[reading.md](reading.md).
