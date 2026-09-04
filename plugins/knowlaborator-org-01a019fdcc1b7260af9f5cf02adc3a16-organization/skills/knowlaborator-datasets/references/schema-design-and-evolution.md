# Schema design and evolution

Use at most 100 ordered fields. Give every field a stable ID, unique machine
key, label, type, and required flag; use `displayFieldId` only for an existing
field. Supported types are `short_text`, `long_text`, `integer`, `decimal`,
`boolean`, `date`, `date_time`, `single_select`, `multi_select`, and
`record_reference`.

Select options use stable `id`, `key`, `label`, and `retired`. A record reference
declares one exact `targetDatasetId` and multiplicity. It may also declare a
`referenceDisplayFieldId` for a current non-reference field in that target
Dataset. This setting belongs to the referencing field's schema: it controls
the label shown for that relationship while the referenced record ID remains
the stored value. Without an override, the relationship inherits the target
Dataset's `displayFieldId` when that field is displayable. Inspect the target's
current schema and preserve the exact target field ID; do not infer one from
record values. Verify current Viewer access to a target Dataset independently;
never reveal an inaccessible target, record ID, or label.

Before creation or revision:

1. Read the current Dataset and schema when they exist.
2. Preserve stable field and option IDs.
3. Call `validate_dataset_schema` and resolve every field error.
4. Create with `create_dataset`, or revise with `revise_dataset_schema` using
   the Dataset's expected revision.

Labels, descriptions, keys, ordering, and optionality relaxation may change.
Field type, relationship target, and multiplicity are immutable. Retire fields
or options used by history rather than erasing them. Adding a required field or
tightening optional to required is allowed only when there are no active
records. A relationship display field may change, but a target field used by a
current inbound relationship must remain active until that relationship is
reconfigured. Schema activation never rewrites historical records.

Exclude formulas, rollups, lookups, attachments, arbitrary JSON fields, field
ACLs, schema inference, and user-defined uniqueness.
