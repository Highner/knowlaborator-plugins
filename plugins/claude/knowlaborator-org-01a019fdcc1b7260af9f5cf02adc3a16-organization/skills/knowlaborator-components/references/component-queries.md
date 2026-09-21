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

Each immutable `dataset_query` Component revision binds one exact Dataset ID,
schema revision ID, stable projected field IDs, bounded filters and scalar
sorts, and page size. Its scope is always `workspace`, and the Dataset must
share the Component's owning Workspace. A binding contains no SQL, credentials,
organization ID, local path, or executable instruction.

Invocation reauthorizes the Component and Dataset independently. It validates
that projected, filtered, and sorted fields remain current and type-compatible,
then returns the deterministic Dataset/schema/columns/records/cursor envelope.
`COMPONENT_DATASET_BINDING_STALE` requires an author to revise the binding.
An inaccessible Dataset is safely unavailable; never probe it through a leaked
ID or reveal its label, schema, field types, counts, or lifecycle.
