# Create an unsent provider draft

1. Require an explicit draft request and resolve one active account from retained
   search_mail context or its accounts metadata, which is returned even when no
   messages match. Resolve by label and address; if several accounts match the
   intended sender, ask which is intended. Check the create_draft capability. Preserve
   the intended `to`, `cc`, `bcc`, subject, and plain-text body.
2. For reply or reply-all, use a source message reference from the same account.
3. When the draft is an output of the Playbook being executed for a persisted
   Case, pass that exact `caseId` to `create_mail_draft`; the operation links the
   resulting provider draft to the Case. Omit `caseId` for a draft that is not
   part of persisted Case work. This runtime rule does not need to be repeated
   in each Playbook's instructions.
4. Generate one idempotency key and reuse it only for an identical retry. After
   an unknown outcome, direct the user to inspect Drafts before using a new key.
5. Report the returned account, draft reference, state, and Case link when one
   was requested. State that the message was saved as a draft and was not sent.

When the user requests a signed Document attachment, discover its exact copy with
`list_document_signed_copies`. Pass `storedSignedCopies` containing the selected
`documentId` and `signedCopyId` to `create_mail_draft`. This uses the immutable
stored artifact, including all remarks, checkmarks, signature images and evidence;
do not attach the unsigned source version or reconstruct a PDF. Normal attachment
count/size limits and Document permissions apply. The operation may retain a known
draft while an attachment or Case link needs retrying; reuse the same idempotency
key for identical retries. Never send the draft through another tool implicitly.
