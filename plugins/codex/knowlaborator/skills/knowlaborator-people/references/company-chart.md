# Org Chart

Use Org Chart for company structure. People exist independently of accounts; an optional
organization-membership link identifies an account without granting any permission. A
person can occupy several positions, and a position without an occupant is vacant. Units
nest; primary reporting runs between positions and can cross unit boundaries. Reporting
never grants Workspace, content, management or decision approval authority.

Full active members read; Full organization administrators write. Guests and realm-bound
connectors cannot read the company directory, and a reader-scoped connector cannot
change it even for an administrator. Do not switch organization, credentials or
transports to bypass a denied read or write.

## Read

Discover records with `list_org_chart_entries` (kind `person`, `unit` or `position`) and
its returned cursors, and read a known record with `get_org_chart_entry`. `get_org_chart`
accepts a unit or reporting-position focus. A bounded chart can be truncated; do not
present it as the whole company, and follow the list pages or focus the chart when the
user wants a complete view. Names can repeat, so preserve exact IDs and distinguish
ambiguous candidates.

## Change

For a user-authorized change, read the affected records and preserve current revisions.
Use `create_org_chart_person`, `create_org_chart_unit` or `create_org_chart_position`,
the matching `update_org_chart_person`, `update_org_chart_unit` or
`update_org_chart_position`, and `set_org_chart_status` to archive or restore.
Membership links must address an active member of the same organization; revoked links
remain historical and never reactivate or recreate an account. Never infer an account
email or personnel secret into a person record.

Use explicit fields to assign or vacate a position, change its unit or reporting parent,
or move a unit; do not infer those links from titles or chart geometry. Self-links and
unit or reporting cycles are rejected. Archiving keeps stable IDs and requires explicit
reassignment of active dependents; do not recursively archive, reparent or vacate
records as an unrequested side effect.

## Project references

Projects reference units or positions only by authorized ID, label and status. Use
`search_org_chart_references` to discover candidates and `resolve_org_chart_reference`
for an exact retained reference; neither grants directory or Workspace access. Missing
or unavailable references reveal no target identity. Retained references survive Org
Chart deactivation; new or replaced links need active targets. Setting or removing a
project's reference is a Projects operation.
