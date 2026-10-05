# Move an exact message to Trash

Use `delete_mail_message` only when the user requests deletion. Keep the exact
protected `messageReference` as returned by `search_mail` or
`get_mail_message`. An unambiguous search result is sufficient; read the message
only when needed to resolve the user's target. If the target is ambiguous, ask
which message the user means before deleting.

The tool moves one owner-visible message to the provider's Trash or Deleted
Items folder. It never permanently deletes mail, empties Trash, or deletes a
thread or mailbox. Gmail drafts are moved using their underlying message.
An IMAP message already in Trash is left there without marking it for deletion.
If IMAP has no usable Trash folder, report `MAIL_TRASH_UNAVAILABLE`; the message
is left untouched. Never substitute deleted flags, expunge, or permanent deletion.

Report success only after the tool succeeds. Provider errors retain the shared
safe error codes and retry guidance. Existing ingested snapshots and Documents
are retained; the live source is marked unavailable after successful deletion.
