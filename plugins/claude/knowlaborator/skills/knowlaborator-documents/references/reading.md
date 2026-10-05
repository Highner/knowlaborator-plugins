# Read Documents and Templates

Use list_documents for bounded discovery; filter purpose=document or template
when relevant. Use get_document for logical metadata and the current version ID,
and get_document_version for exact current or historical version metadata and
processing state. Preserve purpose and description separately from documentType.

Use search_content for discovery, then get_document_file with the exact version
ID when the original must be read or verified. Indexed chunks are never a
substitute for the actual file. Follow the shared
[file inspection](../../knowlaborator-work/references/file-inspection.md)
flow: use native staging for the exact resource link on the same MCP connection,
verify the saved original, then inspect it. The tool result contains metadata
and a resource link; neither is the original file.

A Template's current ready file is an example for local work, not generated
output. Its general description explains what it is; a Playbook reference's
guidance explains how that procedure uses it. Neither grants access or consent.

For a signed copy, use `list_document_signed_copies` with the exact Document ID.
Select the intended copy and retain its ID, source revision, filename, size and
SHA-256. Use `get_document_signed_copy_file(documentId, signedCopyId)` and native
resource staging to retrieve that exact artifact. Remarks, checkmarks, placed
signatures and evidence are already in the stored PDF. The source version is a
separate file and does not contain these signing additions. Human consent and
private reusable signature images remain browser-only.

To attach the copy to an explicitly requested email draft, pass
`storedSignedCopies: [{documentId, signedCopyId}]` to `create_mail_draft`; do not
fetch, recreate or upload a second attachment. Follow Mail's draft workflow.
