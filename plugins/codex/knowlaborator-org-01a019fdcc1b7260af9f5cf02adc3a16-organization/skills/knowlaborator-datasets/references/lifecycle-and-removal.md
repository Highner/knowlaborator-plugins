# Lifecycle and removal

Use `update_dataset` for bounded metadata changes and
`set_dataset_status` for archive or restore. Read the current Dataset first and
carry its expected revision. Archiving does not bypass record, relationship, or
link protections.

Treat permanent removal as destructive:

1. Read the exact Dataset, current schema, and relevant lifecycle state.
2. Explain that removal is irreversible and identify the exact Dataset and
   owning Workspace.
3. Obtain explicit confirmation for that exact target.
4. Call `remove_dataset` only with Manager access and the required confirmation.

Removal must remain blocked while the Dataset has records, inbound schema
references, or resource links. Do not unlink, archive, or remove dependent data
implicitly to force deletion. On a revision conflict, reread and reconcile;
never retry blindly.
