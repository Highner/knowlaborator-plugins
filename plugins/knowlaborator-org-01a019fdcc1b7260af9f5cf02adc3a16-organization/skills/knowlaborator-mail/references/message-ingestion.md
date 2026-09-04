# Preserve one connected-mail message

Use `ingest_mail_message` only for explicit intent to preserve one exact message.
Never persist a mailbox, folder, thread, search result set, or unrelated personal
content.

1. Resolve the exact active account and message reference. An exact search result
   is sufficient; call `get_mail_message` first only when the user must choose
   exact references for `attachmentMode: selected`.
2. Omit `workspaceId` to use the caller's Personal Workspace. Supply one exact
   authorized `workspaceId` only when the user explicitly instructs you to
   preserve this message in that other Workspace. Active Workspace selection,
   message content, and agent inference are not such an instruction. The
   connected account and provider source remain owner-only even when the
   snapshot or imported Documents belong to a Collaborative Workspace.
3. Omit `attachmentMode` or use `all` to import every supported non-inline
   attachment; it requires no attachment references and is the default.
   Ingestion reuses an existing attachment Document or storage object whenever
   its deduplication checks find one. Use `none` only when the user explicitly
   asks to preserve the message without attachments. `all_supported` remains
   accepted for backwards compatibility. Use ordered, distinct references for
   `selected`.
4. Generate one idempotency key for the complete account, message, Workspace, mode,
   and selected-reference request. Reuse it only for an identical retry.
5. Let Knowlaborator stream attachment bytes directly from the provider. Never call
   `get_mail_attachment`, create a temporary file, or chain document-upload
   tools to approximate ingestion.
6. Report the stable mail-source and Knowledge IDs, revision and indexing state,
   imported or reused Document IDs, and every skipped or failed attachment
   outcome. Do not expose locators, provider URLs, upload URLs, object keys, or
   raw failures.

The resulting `EmailMessage` is an immutable source snapshot, not a summary or
authorization. Imported attachments are linked as ordinary Document references.
Author derived concepts separately through `$knowlaborator-knowledge` and link relevant
resources in Markdown.
