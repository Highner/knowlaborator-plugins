# Stage and inspect an original file

All exact-file tools use one flow for images, PDFs, Office documents, attachments,
canvas files, and OKF exports:

`exact-file tool -> resource_link -> resources/read -> native staged file -> inspection`

1. Retain the exact resource URI, filename, original media type, and byte size
   from the tool result, plus SHA-256 when supplied. The link's `size` is the
   original byte count before Base64 encoding. A link or metadata alone is not
   a completed download or inspection.
2. Use the client's native MCP resource/file support to stage that resource
   into its execution workspace or file store. The host must route
   `resources/read` through the authenticated MCP connection that returned the
   link and save the binary contents outside the model's text context. Use the
   connection identity supplied by the client; do not guess a server from the
   URI scheme or choose an unrelated aggregate reader. Keep the exact URI,
   including any revision or digest. It is not an HTTP download URL.
3. Obtain the actual staged path or native file reference. With the client's
   local-file tools, verify the complete file's byte count and any supplied
   SHA-256 before inspection. A mismatch or an incomplete file is a failed
   transfer; do not inspect it as the original.
4. Inspect that file using the appropriate image viewer or document parser.
   Reading metadata, search chunks, preview text, or a thumbnail does not
   inspect the original. Report the content and only the checks actually run.
   Originals are read-only; make a working copy before editing.

ChatGPT web can perform this native handoff for original files; it is not
limited to images. The host owns staging and parser availability. On Codex,
Claude, or another client, the same MCP contract applies, but an ordinary text
resource response is not a substitute for native binary staging.

If the client returns `Unknown resource`, cannot produce a staged file, truncates
a serialized blob, or repeats staging approvals without progress, report the
exact client limitation. Do not reconstruct Base64 through model text, invent
a public URL or local path, export extracted content as the original, switch
credentials/endpoints to widen access, or keep retrying the same failed handoff.

OrgApp retains its 100 MiB maximum original-file bound. Provider operations may
have smaller limits, such as Teams' 25 MiB attachment limit. A client may impose
its own transfer or inspection limits; do not infer support from size metadata
or claim that every client can inspect every file.
