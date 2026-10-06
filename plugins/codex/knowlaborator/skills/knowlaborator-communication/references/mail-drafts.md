# Create an unsent provider draft

1. Require an explicit draft request. For a new draft, resolve one active account from retained
   search_mail context or its accounts metadata, which is returned even when no
   messages match. Resolve by label and address; if several accounts match the
   intended sender, ask which is intended. Check that its capabilities include `create_draft`. Preserve
   the intended `to`, `cc`, `bcc`, subject, and plain-text body.
2. For reply or reply-all, omit `accountId` and supply the protected source
   message reference as `replyToMessageReference`. It selects the exact account.
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
