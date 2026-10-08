# Create an unsent provider draft

1. Require an explicit draft request. For a new draft, resolve one active account from retained
   search_mail context or its accounts metadata, which is returned even when no
   messages match. Resolve by label and address; if several accounts match the
   intended sender, ask which is intended. Check that its capabilities include `create_draft`. Preserve
   the intended `to`, `cc`, `bcc`, subject, and plain-text body.
2. For `reply`, `reply_all`, or `forward`, omit `accountId` and supply the protected source
   message reference as `replyToMessageReference`. It selects the exact account.
   For `forward`, supply the intended new recipients and subject. `textBody` is
   the introduction; the original message and its attachments are included
   automatically, so do not duplicate them in the introduction. The draft preview
   and editor also show the source conversation. Forwarding creates an unsent
   draft and never sends the source message.
   For every agent-created draft, choose an appropriate writable Workspace and pass its
   exact ID as `suggestedWorkspaceId`. This is an advisory default for the person's
   post-send indexing or processing prompt. The person can change it, choose either
   action or skip both; draft creation never indexes or queues the email.
3. After an unknown outcome, direct the user to inspect Drafts before trying
   again with a new idempotency key.
4. Report the returned account, draft reference and state. State that the message
   was saved as a draft and was not sent.

When the user requests a signed Document attachment, discover its exact copy with
`list_document_signed_copies`. Pass `storedSignedCopies` containing the selected
`documentId` and `signedCopyId` to `create_mail_draft`. This uses the immutable
stored artifact, including all remarks, checkmarks, signature images and evidence;
do not attach the unsigned source version or reconstruct a PDF. Normal attachment
count/size limits and Document permissions apply. The operation may retain a known
draft while an attachment needs retrying; reuse the same idempotency key for
identical retries. Never send the draft through another tool implicitly.
