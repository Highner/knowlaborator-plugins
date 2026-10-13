# Download and inspect an original file

All exact-file tools use one flow for images, PDFs, Office documents, attachments,
canvas files, and OKF exports:

`exact-file tool -> expiring HTTPS resource_link -> saved file -> inspection`

1. Retain the exact returned HTTPS URI, filename, original media type, byte size
   and SHA-256 when supplied. The link's `size` is the original byte count. A
   link or metadata alone is not a completed download or inspection.
2. Save that link into the execution workspace using the host's file-download
   or HTTP support. In ChatGPT Work, download the HTTPS URL directly with its
   execution tools; do not send it to an unrelated aggregate resource reader.
   A host with native MCP file staging can stage the same URI through
   `resources/read` on the authenticated connection that returned it. Both
   consume the exact same link. Do not alter its path or query or print its
   access ticket. It expires after five minutes; request a fresh link from the
   same exact-file tool if it has expired.
3. Obtain the actual saved path or native file reference. Verify the complete
   file's byte count and supplied SHA-256 with local tools before inspection.
   A mismatch or incomplete file is a failed transfer. Do not inspect it as
   the original or reconstruct a truncated Base64 response.
4. Inspect the saved file using the appropriate image viewer or document
   parser. Reading metadata, search chunks, preview text or a thumbnail does
   not inspect the original. Report only the checks actually run. Originals
   are read-only; make a working copy before editing.

The client owns its download and parser capabilities. If it cannot save the
HTTPS link or stage it natively, report the exact limitation. Do not invent
URLs or paths, export extracted content as the original, switch credentials
or organizations to widen access, or repeat a known failing reader. Never
place file bytes in model text. Native upload file parameters are unchanged.

OrgApp retains its 100 MiB maximum original-file bound and smaller provider
limits, including Teams' 25 MiB limit. Client transfer and inspection limits
may be smaller. A successful tool call is not proof of a complete download.
