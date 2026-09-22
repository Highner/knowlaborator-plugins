# Read one connected-mail attachment

Call `get_mail_message` first and use its exact account, message, and attachment
references. Call `get_mail_attachment` only for the one attachment the user
selected. It returns a resource link; read that exact link with the client's MCP
resource reader before inspecting or extracting the file. Never open siblings
speculatively or access the underlying provider directly. Follow the shared MCP
file handoff instructions. Account resolution and partial failures follow
[reading.md](reading.md).
