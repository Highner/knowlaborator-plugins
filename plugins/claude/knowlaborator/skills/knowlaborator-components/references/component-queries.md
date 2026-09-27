# Declarative Component data bindings

## Embedded content

Each immutable `okf_query` Component revision owns one bounded declarative
search. Supply a non-empty `query`; optionally narrow `kinds` to
`knowledge_object` and/or `document_chunk`, add string equality values under
`metadata`, choose bounded knowledge lifecycle statuses, set changed-time
bounds, and request current or historical document chunks. `limit` is 1 through
100. The immutable revision scope remains `shared`, `caller_private`, or
`caller_visible`.

Do not generate SQL, URLs, MCP calls, AI instructions, executable expressions,
legacy paths, trust tiers, source edges, producer extensions, or explicit IDs
from the discarded content store. A stored revision containing a legacy content
ID returns `COMPONENT_QUERY_UNAVAILABLE` and must be revised. Correct
`COMPONENT_QUERY_INVALID` using its field errors without broadening scope.

```json
{
  "query": "retention policy",
  "kinds": ["knowledge_object"],
  "metadata": { "owner.team": "governance" },
  "knowledgeStatuses": ["active"],
  "includeHistorical": false,
  "limit": 25,
  "scope": "shared"
}
```

## Datasets

An immutable `dataset_query` Component revision can use the original single
Dataset binding or a saved two-to-four-source join plan. Every source declares
an exact Dataset ID, schema revision ID, stable projected field IDs, bounded
filters and scalar sorts, and page size. All sources share the Component's
owning Workspace and use `dataScope=workspace`. A binding contains no SQL,
credentials, organization ID, local path, or executable instruction.

For a joined plan, list the root source first. Each later source has one join
to an earlier source. The `referenceFieldId` must be a projected, current
`record_reference` field on `referenceAlias`, targeting the other Dataset.
`kind` is `left` (the default) or `inner`. The server follows bounded Dataset
query pages, joins by visible record IDs, and returns `rootAlias`, source
metadata, and rows keyed by source alias. An unmatched left-joined source is
`null`. Fanout, total records, and payload size are bounded; narrow source
filters if the query is too broad. The bundle receives the current resolved
result whenever the Component is opened; an agent is not involved in display.

```json
{
  "sources": [
    { "alias": "assets", "datasetId": "<exact-id>", "schemaRevisionId": "<exact-id>", "query": { "projectedFieldIds": ["<field-id>"], "pageSize": 50 } },
    { "alias": "rentPeriods", "datasetId": "<exact-id>", "schemaRevisionId": "<exact-id>", "query": { "projectedFieldIds": ["<asset-reference-field-id>", "<rent-field-id>"], "pageSize": 50 } }
  ],
  "joins": [
    { "parentAlias": "assets", "childAlias": "rentPeriods", "referenceAlias": "rentPeriods", "referenceFieldId": "<asset-reference-field-id>", "kind": "left" }
  ]
}
```

Invocation reauthorizes the Component and every source independently. It
validates that projected, filtered, sorted, and joined fields remain current
and type-compatible. Original single-Dataset bindings continue to return the
Dataset/schema/columns/records/cursor envelope.
`COMPONENT_DATASET_BINDING_STALE` requires an author to revise the binding.
An inaccessible source is safely unavailable; never probe it through a leaked
ID or reveal its label, schema, field types, counts, or lifecycle.
