# Read one connected-mail attachment

Call `get_mail_message` first and use its protected message and attachment
references without an account ID. An attachment belongs to that exact message
and cannot be combined with another message's reference.
Call `get_mail_attachment` only for the one attachment the user
selected. It returns a resource link; use the shared
[file inspection](../../knowlaborator-work/references/file-inspection.md) flow
to stage and verify the exact original before inspecting it. Never open siblings
speculatively or access the underlying provider directly. Account resolution and partial failures follow
[reading.md](reading.md).
