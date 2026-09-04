# Canvas assets and ingestion

Canvas assets are durable, authorization-scoped files owned by one canvas.
They are not Documents, global-search results, Knowledge sources or Document
processing inputs until a person explicitly asks for ingestion. Supported
originals are PNG, JPEG, GIF, WebP, PDF and non-macro DOC, DOCX and PPTX up
to 104,857,600 bytes (100 MiB). Images render as `image` items; every other
format is a `file` item.

Asset bytes never pass through MCP arguments. Inspect the local file with the
client's ordinary local-file tools, but do not put its path, bytes, upload
URL, credentials or tokens in an MCP argument or summary.

## Upload

1. Confirm the exact canvas, that it is active, and that the user has
   Contributor or Manager access to its Workspace.
2. Determine the leaf file name, exact byte size and lowercase hex SHA-256 of
   the bytes locally. Call `begin_canvas_asset_upload` with the canvas ID and:

   ```json
   {
     "fileName": "latency-dashboard.png",
     "mediaType": "image/png",
     "byteSize": 482113,
     "sha256": "3f1a…64 lowercase hex characters…",
     "idempotencyKey": "canvas-0193a9f1-asset-latency-dashboard-01"
   }
   ```

   The response returns `uploadIntentId`, `url`, `method`, `headers`,
   `expiresAt` and `maximumByteSize`. The intent lives one hour. A byte size
   above the limit or an unsupported media type is rejected before any
   transfer instruction is issued.
3. Immediately send only the raw bytes to the returned URL with its exact
   method and headers. Do not send JSON, multipart form data, MCP
   authorization or cookies. Do not repeat the URL afterwards.
4. Call `complete_canvas_asset_upload` with the canvas ID and
   `uploadIntentId`. OrgApp reauthorizes, verifies actual size and SHA-256,
   inspects the format, promotes one immutable asset revision and removes
   staging. The response carries `assetId`, `assetRevisionId`,
   `revisionNumber`, `fileName`, `mediaType`, `byteSize`, `sha256`, `width`,
   `height`, `isImage` and `ingestion` (null until ingested).
5. Place the asset with `apply_canvas_changes`: a `create` of kind `image`
   with `{ "assetRevisionId": "…", "altText": "…", "caption": "…" }` or of
   kind `file` with `{ "assetRevisionId": "…", "caption": "…" }`. The asset
   revision becomes an explicit `asset` reference of the item.

If transfer or completion is interrupted, retry `begin_canvas_asset_upload`
with the same idempotency key and identical metadata while the intent is
valid; it returns the same intent. Then transfer the same bytes if needed and
retry `complete_canvas_asset_upload`. After `CANVAS_ASSET_UPLOAD_EXPIRED`,
begin a replacement intent with a new key. `CANVAS_ASSET_UPLOAD_MISMATCH`
means the stored bytes differ from the declared size or digest;
`CANVAS_ASSET_UPLOAD_INCOMPLETE` means nothing arrived;
`CANVAS_ASSET_UNSUPPORTED` means the bytes are not a safe supported original.
Never upload twice merely because the outcome is uncertain.

## Reading assets

Use `get_canvas_asset_file` from
[reading-and-inspection.md](reading-and-inspection.md) to inspect the exact
original with its authoritative SHA-256. Alt text and captions are authored
descriptions, not verified content.

## Ingest into a Document

`ingest_canvas_asset` promotes one exact asset revision into a normal
Document in the same Workspace through the ordinary Document pipeline. Call it
only when the current user explicitly requested that exact asset be ingested
in this conversation. Uploading, placing, describing or summarizing an asset
is not such a request; asking for a Document, searchability, OKF assessment or
"save this as a Document" is. Automation may never ingest.

```json
{ "title": "Latency dashboard, week 34", "idempotencyKey": "canvas-0193a9f1-ingest-0193b1e0-01" }
```

`title` is optional (≤ 500 characters; the file name is used otherwise). The
response returns the ingestion `id`, `assetRevisionId`, `documentId` and
`documentVersionId`; the asset revision then shows this relation in
`ingestion`. Ingesting the same revision again replays the existing result.
An archived canvas rejects ingestion with `CANVAS_ARCHIVED`.

From that point the Document behaves like any other: use
`$knowlaborator-documents` to check processing with `get_document_version`,
read the exact file with `get_document_file`, and run the separate OKF
assessment when the user asks for it. The canvas asset remains exactly as it
was; the Document and the asset are distinct identities even when bytes are
reused.

Uningestion is browser-only. It previews fresh blockers, purges the Document
only when it is unchanged and unreferenced, and otherwise detaches the
relation and preserves the Document. Do not attempt to emulate it with
Document archive or removal tools.
