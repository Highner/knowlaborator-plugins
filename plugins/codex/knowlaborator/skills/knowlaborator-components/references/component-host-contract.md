# Component authoring contract 1.0

Read this reference when creating or revising a Component. It documents the
host protocol and bundle format. Use the exposed MCP tool schemas for exact
request fields and server validation for current constraints. No separate
schema-discovery tool, web page, or frontend JavaScript download is needed.
If the installed skill describes a field absent from the exposed tool schema,
report the mismatch and the affected feature; do not send undeclared fields
or bypass MCP through HTTP/browser calls.

## Manifest and bundle

Pass the manifest as `create_component_from_source.request.manifest` (or
`begin_component_upload.request.manifest` for a direct ZIP upload); it is not
a replacement for either HTML entry point. The core fields are:

| Field | Meaning |
| --- | --- |
| `name` | Nonblank name, at most 200 characters. |
| `description` | Nonblank description, at most 2,000 characters. |
| `tags` | Array of strings; normalized, deduplicated, and limited to 20. |
| `useWhen` | Nonblank usage guidance, at most 1,000 characters. |
| `accessibleName` | Nonblank accessible name, at most 200 characters. |
| `hostContractVersion` | `"1.0"`. |
| `inputMode` | `"agent_input"`, `"okf_query"`, `"dataset_query"`, or `"external_only"`. |
| `inputSchemaJson` | JSON object encoded as a string; validates agent input. |
| `resultSchemaJson` | JSON object encoded as a string; validates resolved query data. |
| `dataScope` | `"workspace"` (default), `"caller_personal"`, or `"caller_visible"`. Dataset queries require `"workspace"`. |
| `queryJson` | Bounded query encoded as a string; `null` for `agent_input` and `external_only`. See [component-queries.md](component-queries.md). |
| `dependencies` | Optional exact published revision references: `componentId`, `revisionId`, `alias`. |
| `imageDocumentVersionIds` | Optional exact image Document version IDs; see the [authoring reference](component-authoring-and-governance.md). |
| `connections`, `externalSources` | Optional declarations described below, when exposed by the upload tool. |

The schema strings support `type`, `required`, `properties`, and `items` for
value validation. Do not assume that every JSON Schema keyword is enforced.
`resultSchemaJson` describes query data, not the separate external-source map.

For example, this manifest displays a caller-supplied value. It is a snapshot
example, not a live-price integration or an eligible Today tile:

```json
{
  "name": "Value summary",
  "description": "Displays a value supplied for this invocation.",
  "tags": ["summary"],
  "useWhen": "Show a compact summary of a supplied value.",
  "accessibleName": "Value summary",
  "hostContractVersion": "1.0",
  "inputMode": "agent_input",
  "inputSchemaJson": "{\"type\":\"object\",\"required\":[\"value\"],\"properties\":{\"value\":{\"type\":\"number\"}}}",
  "resultSchemaJson": "{}",
  "dataScope": "workspace",
  "queryJson": null,
  "dependencies": []
}
```

Upload a ZIP of at most 5 MiB. It must contain `index.html` and `preview.html`
at the root. Additional files may use `.html`, `.css`, `.js`, or `.json`.
Paths must be unique, relative, and contained; do not include empty, `.` or
`..` path segments. Use local scripts/styles or inline code, with no CDN
dependencies or remote assets. Both entry points receive the same bridge data.
Bundle dependencies into self-contained JavaScript and CSS: the inline host
inlines local script and stylesheet references, but runtime module imports,
CSS imports, and fetching local JSON are not a supported loading mechanism.

The iframe is sandboxed with scripts enabled and no network access. Do not
use `fetch`, XMLHttpRequest, WebSocket, cookies, local/session storage,
popups, geolocation, or top-level navigation. Render untrusted strings as
text, not executable HTML. Images come from the host's authorized data URLs.

Prefer `create_component_from_source` for agent-authored bundles. Its request
contains the manifest, owning `workspaceId`, `componentId` (`null` for a new
Component), idempotency key of at most 200 characters, optional
`originatingClient` and `changeMetadataJson`, and `files`:

```json
[
  { "path": "index.html", "text": "<main>Full view</main>" },
  { "path": "preview.html", "text": "<main>Compact preview</main>" }
]
```

Source creation accepts at most 32 files, paths of at most 200 characters,
and at most 512 KiB of combined UTF-8 text. It uses the same entry-point,
path, file-type, manifest, ownership, and capability validation as ZIP upload.
The server computes the deterministic ZIP, exact byte size, and SHA-256,
writes private storage, and returns the completed validation result in one
MCP call. This does not require client-side storage network access. Repeat
only the identical request under the same idempotency key; changed content
or metadata requires a new key. A successful retry returns the same revision.
Validation does not execute the source or prove rendering or provider access.

For a prebuilt ZIP or larger source, the direct upload request carries the
owning `workspaceId`, a `componentId` (`null` for a new Component), an
idempotency key of at most 200 characters, the ZIP's exact `sizeBytes`, and
its 64-character hexadecimal `sha256`. Use the returned upload method, URL,
and headers only for those exact bytes, then complete using the returned
`revisionId` and the same checksum. The client must be able to reach the
signed storage URL. Never pass ZIP bytes or base64 bundles through MCP.

## Host bridge

The full view and compact preview use `window.postMessage`. Register the
listener before sending `ready`. All messages have this envelope:

```json
{
  "version": 1,
  "instanceId": "<host-supplied-instance>",
  "revision": 1,
  "type": "ready"
}
```

Use the instance and numeric revision supplied by the host, never hardcoded
values. The web host supplies URL parameters `instance` and `revision`.
The inline MCP host also supplies JSON in `window.name` with `instanceId` and
`revision`. Support both. Send to `window.parent` with target origin `"*"`
because the sandbox has an opaque origin; accept messages only from that
parent with the matching envelope.

| Direction / type | Payload and behavior |
| --- | --- |
| Component → host: `ready` | Requests initialization after the listener is installed. |
| Host → component: `initialize` | `data`: caller input or resolved query result; `images`: array of `{ documentVersionId, dataUrl }`; `externalSources`: map of source names to provider JSON or `null`. |
| Component → host: `height` | Numeric `height` from 160 through 1,200 for the full view. The Today tile's viewport remains compact. |
| Component → host: `status` | Plain-text `status`, at most 180 characters. |
| Component → host: `navigateKnowledge` | `knowledgeId`, at most 100 characters; the host permits only targets present in authorized invocation data. |
| Component → host: `setViewerArguments` | `requestId`, declared `connectionName`, and `values` map of up to four declared names to strings of at most 128 characters. The browser host saves these for the current viewer and fetches fresh external sources. The inline MCP Apps host opens the Component in Knowlaborator for the viewer to finish selection. |
| Host → component: `viewerArgumentsResult` | Matching `requestId` and `ok` boolean after a `setViewerArguments` attempt. A successful browser save is followed by another `initialize`. The inline host returns `ok: false, requiresBrowser: true`. |

Component-to-host messages are limited to 32,768 serialized characters.
Keep data in the initialization payload rather than echoing it in status or
height messages. A minimal bridge for either entry point is:

```js
const parameters = new URLSearchParams(window.location.search);
let inlineContext = {};
try { inlineContext = JSON.parse(window.name || "{}"); } catch {}
const context = {
  version: 1,
  instanceId: parameters.get("instance") || inlineContext.instanceId,
  revision: Number(parameters.get("revision") || inlineContext.revision)
};
function send(type, payload = {}) {
  window.parent.postMessage({ ...payload, ...context, type }, "*");
}
window.addEventListener("message", (event) => {
  const message = event.data;
  if (event.source !== window.parent || !message || message.type !== "initialize"
      || message.version !== context.version || message.instanceId !== context.instanceId
      || message.revision !== context.revision) return;
  // Implement render for this entry point, including empty/error states.
  render(message.data, message.images ?? [], message.externalSources ?? {});
});
send("ready");
```

## External sources and freshness

For live prices or other provider data, the server can perform bounded HTTPS
GET requests or remote MCP read-tool calls and deliver JSON through `initialize.externalSources`. The
bundle interprets that JSON and renders it. There is no bundle-side network
access or background polling. Data is resolved when the host loads the view;
show its source date and distinguish a daily close from a real-time quote.

A revision can declare up to four connections and four external sources.
Each source names one declared connection and uses its public HTTPS origin.
Supported authentication is `api_key` in a safe named header, `oauth2` for GET
sources, or `mcp_oauth` for remote MCP sources. Raw arbitrary POST requests and
query-string API keys are not supported. Never invent a credential or use a
dummy key.

This illustrates the declaration shape, not a working provider endpoint:

```json
{
  "connections": [{
    "name": "market",
    "label": "Market prices",
    "destinationOrigin": "https://api.example.com",
    "authKind": "api_key",
    "apiKeyHeaderName": "X-API-Key"
  }],
  "externalSources": [{
    "name": "prices",
    "connectionName": "market",
    "urlTemplate": "https://api.example.com/prices?period=1y"
  }]
}
```

Choose a verified provider and supported endpoint. Public provider-documentation
research is separate from Knowlaborator schema discovery and does not require
opening the Knowlaborator web application. Use a provider-supported relative
period for a rolling window when available; a hardcoded date range will age.
Do not silently replace an index with an ETF or substitute sample prices.

Static source URLs may omit `datasetFields`. Dynamic placeholders are limited
to query-string values from exact projected Dataset fields. Declare each as
`{ placeholder, sourceAlias, fieldId }`; `sourceAlias` is `"dataset"` for a
single Dataset or the saved join's alias. The server URL-encodes values. An
empty field set skips the request and yields `null`. Arbitrary expressions,
date macros, and agent-supplied URL parameters are not supported.

OAuth declarations use `authorizationEndpoint`, `tokenEndpoint`, `clientId`,
and `scopes` instead of `apiKeyHeaderName`. Each viewer configures their own
connection in Knowlaborator. Credentials never belong in MCP arguments,
the manifest, ZIP, or initialization payload. If provider setup is required,
report it and present the returned Component link for the user to complete;
do not automate credential entry. Missing connections return
`connection_required`; failed provider requests return `external_source_failed`.

`agent_input` takes a caller-supplied snapshot and is unavailable on Today.
`external_only` is available on Today with at least one declared external
source, `queryJson: null`, `dataScope: "workspace"`, and `{}` input and result
schemas. The host resolves sources on load and explicit refresh without an
agent invocation. The bundle should show empty, loading, stale, and provider
error states and the provider's valuation or quote timestamp.

For an MCP source, declare a connection with the provider HTTPS origin,
`authKind: "mcp_oauth"`, exact `mcpServerUrl`, optional `scopes`, and optional
`viewerArguments` declarations. The source uses the same URL as `urlTemplate`,
`transport: "mcp"`, and one or two `mcpCalls` of `{ toolName, arguments }`.
Supply `arguments` as a JSON object, never a JSON-encoded string. For example,
use `"arguments": { "ticker": "{{viewer.index_code}}", "limit": 30 }`.
Only exact projected Dataset fields (`{{field_name}}`) and exact declared viewer
fields (`{{viewer.portfolio_id}}`) can fill arguments. Tool results are keyed
by tool name under the external source name. Missing viewer fields skip the
dependent source and yield `null`; the component can render a separate
account-list source and ask the viewer to select an account. Each viewer
authorizes their own account in a browser tab, and their credential never
enters the bundle or initialization data. The browser host also supports a
bounded `setViewerArguments` message for the bundle to persist that selection;
the standard connection screen can collect it as well. In an inline MCP Apps
view, the message opens the Component's browser page; select the portfolio
there, then invoke or refresh the inline view again.
The current `mcp_oauth` flow requires the provider to advertise dynamic client
registration and token authentication method `none` or `client_secret_post`.

Scalable Capital's MCP endpoint is `https://mcp.scalable.capital/mcp`. Its
`list_accessible_portfolios` and `get_portfolio_overview` tools can support a
portfolio valuation tile. Render `get_portfolio_overview.valuation.total` as
portfolio valuation with `timestamps.valuationTimestampUtc`. Cash is exposed
separately; do not call the valuation total account wealth. Verify the exact
provider result shape during an authorized viewer session before publishing
the bundle.
