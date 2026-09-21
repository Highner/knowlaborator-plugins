# Change Dataset records

For create or update, map values by stable field ID using the current schema:
dates are `YYYY-MM-DD`, timestamps are UTC ISO-8601, selects use option IDs, and
relationships use exact record IDs. An update replaces the complete values map;
carry the current expected record revision and schema revision ID automatically.

Use `set_dataset_record_status` for archive or restore. Before permanent
`remove_dataset_record`, show the exact record and irreversible consequence and
obtain explicit confirmation. Never retry a conflict blindly; reread and
reconcile against the current record and schema revisions.
