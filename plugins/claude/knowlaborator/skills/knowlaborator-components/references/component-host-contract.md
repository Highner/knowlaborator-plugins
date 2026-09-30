# Component authoring contract 1.0

Read this reference when creating or revising a Component. It documents the
host protocol and bundle format. Use the exposed MCP tool schemas for exact
request fields and server validation for current constraints. No separate
schema-discovery tool, web page, or frontend JavaScript download is needed.
If the installed skill describes a field absent from the exposed tool schema,
report the mismatch and the affected feature; do not send undeclared fields
or bypass MCP through HTTP/browser calls.

## Manifest and bundle

Pass the manifest as `begin_component_upload.request.manifest`; it is not a
replacement for either HTML entry point. The core fields are:

| Field | Meaning |
| --- | --- |
| `name` | Nonblank name, at most 200 characters. |
| `description` | Nonblank description, at most 2,000 characters. |
| `tags` | Array of strings; normalized, deduplicated, and limited to 20. |
| `useWhen` | Nonblank usage guidance, at most 1,000 characters. |
| `accessibleName` | Nonblank accessible name, at most 200 characters. |
| `hostContractVersion` | `"1.0"`. |
| `inputMode` | `"agent_input"`, `"okf_query"`, or `"dataset_query"`. |
| `inputSchemaJson` | JSON object encoded as a string; validates agent input. |
| `resultSchemaJson` | JSON object encoded as a string; validates resolved query data. |
| `dataScope` | `"workspace"` (default), `"caller_personal"`, or `"caller_visible"`. Dataset queries require `"workspace"`. |
| `queryJson` | Bounded query encoded as a string; `null` for `agent_input`. See [component-queries.md](component-queries.md). |
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

The upload request also carries the owning `workspaceId`, a `componentId`
(`null` for a new Component), an idempotency key of at most 200 characters,
the ZIP's exact `sizeBytes`, and its 64-character hexadecimal `sha256`.
Use the returned upload method, URL, and headers only for those exact bytes,
then complete using the returned `revisionId` and the same checksum.

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
GET requests and deliver JSON through `initialize.externalSources`. The
bundle interprets that JSON and renders it. There is no bundle-side network
access or background polling. Data is resolved when the host loads the view;
show its source date and distinguish a daily close from a real-time quote.

A revision can declare up to four connections and four external sources.
Each source names one declared connection and uses its public HTTPS origin.
Supported authentication is `api_key` in a safe named header or `oauth2`.
Unauthenticated requests, query-string API keys, POST requests, and non-JSON
responses are not supported. Never invent a credential or use a dummy key.

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
Today requires a supported saved `okf_query` or `dataset_query` binding.
External-source declarations do not add a new input mode or remove that
requirement. If the requested standalone live-data tile has no suitable saved
binding or supported provider, explain the exact limitation instead of adding
an unrelated query, claiming automatic refresh, or switching to a browser.
