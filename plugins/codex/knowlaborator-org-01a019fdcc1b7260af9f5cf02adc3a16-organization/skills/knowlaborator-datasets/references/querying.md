# Query Datasets

1. Use `list_datasets` for bounded authorized discovery.
2. Before choosing one Dataset, apply this skill's `description` and `useWhen`
   inspection rule, then call `get_dataset` for the exact selected candidate.
3. Read `get_dataset_schema` before constructing a query or write. Use schema
   revision tools only when exact historical context is needed.
4. Call `validate_dataset_query` before a new or materially changed query, then
   call `query_dataset_records`. Preserve the signed cursor unchanged; every
   page is reauthorized and a cursor grants nothing.
5. Use exact record and record-revision tools for detail or history. Missing
   fields in older revisions are null under the current schema.

Project only the needed stable field IDs. Use at most the supported bounded filters,
scalar sorts, and page size. Sorts are deterministic with record ID last. Do not
send SQL, local paths, arbitrary JSON-field expressions, or organization IDs.
