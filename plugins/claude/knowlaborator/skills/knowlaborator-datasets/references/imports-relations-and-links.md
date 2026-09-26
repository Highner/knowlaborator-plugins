# Imports, relations, and links

## Imports

Accept normalized JSON rows only, never a local file path. Pin every request to
the exact current schema revision, keep it within 100 rows and 2 MiB, and use
caller row keys when supplied. Call `validate_dataset_import` first. Call
`import_dataset_records` only after validation succeeds and the user intends an
atomic create-only import. Do not infer a schema, upsert, or partially apply an
invalid batch.

## Record relationships

A `record_reference` value must use an active record in the field's declared
target Dataset. Resolve current Viewer access to the target independently. A
same-organization cross-Workspace reference is usable only while both sides are
authorized; inaccessible targets remain `available:false` without a label.

A `resource_reference` value identifies a Knowledge object or Document by
`{kind,id}`. A multiple field accepts up to 100 distinct ordered pairs, at most
200 resource-reference entries may occur across one record, and
many records may reference the same target. Validate each target's current
visibility before writing. Current record-level backlinks derive from the
record revision and are independently authorized; historical revisions remain
unchanged. References grant no access and an unavailable target reveals no
label or ID in expanded reads.
Use `list_dataset_record_backlinks` for current inbound record references to an
exact Knowledge object or Document.

## Dataset-level links

Use `list_dataset_links`, `list_dataset_backlinks`, and
`search_dataset_link_targets` for reads. Links belong to the Dataset, never an
individual record, and the owning Workspace is not duplicated as a link.

For `link_dataset_resource`, require Manager access to the source Dataset and
current visibility of the exact target. For `unlink_dataset_resource`, confirm
the exact existing link. Links grant no access; lost target access must not
reveal a label, lifecycle, or existence.
