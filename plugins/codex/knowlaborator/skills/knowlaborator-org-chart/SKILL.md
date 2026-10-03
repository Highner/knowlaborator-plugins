---
name: knowlaborator-org-chart
description: Read and maintain whole-company people, organizational units, positions, vacancies, and primary reporting in the optional Org Chart module.
---

# Knowlaborator Org Chart

Use Org Chart for company structure. People exist independently of accounts; an
optional organization-membership link identifies an account without granting any
permission. A person can occupy several positions, and a position without an
occupant is vacant. Units nest; primary reporting runs between positions and can
cross unit boundaries. Reporting never grants Workspace, content, management, or
decision approval authority.

Use retained active-organization context. When availability is relevant, call
`list_organization_modules`; the module ID is `org-chart`. Full active members
read; Full organization administrators write. Guests and realm-bound connectors
cannot read the company directory. A reader-scoped connector cannot perform
mutations, even when the account is an administrator. Do not switch organization,
credentials, or transports to bypass a denied read or write.

Discover records with `list_org_chart_people`, `list_org_chart_units`, or
`list_org_chart_positions` and their returned cursors. Use
`get_org_chart_person`, `get_org_chart_unit`, or `get_org_chart_position` for a
known record. `get_org_chart` accepts a unit or reporting-position focus. A
bounded chart can be truncated; do not present it as the whole company. Follow
the list pages or focus/filter the chart when the user requests a complete view.
Names can repeat, so preserve exact IDs and distinguish ambiguous candidates.

For a user-authorized change, read the affected records, preserve current
revisions, and carry the expected organization and a stable idempotency key.
Use the matching `create_org_chart_*`, `update_org_chart_*`, and
`set_org_chart_*_status` tools for people, units, and positions.
Reuse that key only when retrying the same exact intent. Reconcile revision or
idempotency conflicts instead of overwriting another editor. Membership links
must address an active member of the same organization; revoked links remain
historical and never reactivate or recreate an account. No account email or
personnel secret is inferred or copied into a person record.

Use explicit fields to assign/vacate a position, change its unit or reporting
parent, or move a unit. Do not infer those links from titles or chart geometry.
Self-links and unit/reporting cycles are rejected. Archiving retains stable IDs
and requires explicit reassignment of active dependents; do not recursively
archive, reparent, or vacate records as an unrequested side effect.

Project references use only authorized unit/position IDs, labels, and status.
Use `search_org_chart_references` to discover candidates and
`resolve_org_chart_reference` for an exact retained reference.
They grant no directory or Workspace access. Missing or unavailable references
reveal no target identity. Retained references survive Org Chart deactivation;
new or replaced links require active enabled targets. Removing a Project link
is a Projects operation.

Deactivation preserves authorized reads and history while pausing Org Chart
writes. On `MODULE_NOT_INSTALLED` or `MODULE_DEACTIVATED`, inspect availability
and explain the state. Installation/reactivation belongs to Full administrators
in **Organization Settings -> Modules**. Core ToDos, Documents, Knowledge, and
decisions retain their usual access and behavior.
