# Workspace discovery and selection

Reuse retained organization and active-Workspace context and never guess
selection.

For an unbound direct client reporting ORG_SELECTION_REQUIRED, use
`list_organizations` and `set_active_organization` only if exposed, selecting the
exact user-intended organization. Ask when ambiguous. Organization-bound
connections cannot change organizations.

Use `list_workspaces` for bounded authorized discovery and `get_workspace` for
one exact read. Preserve Workspace IDs, kinds, effective roles, revisions,
cursors, and correlation IDs. Call `set_active_workspace` only after resolving
an accessible exact Workspace and only where the tool is exposed. A Realm can
be a focus filter or default, but it does not cap ordinary interactive access.
Active selection is a UX default, never proof of authorization.

Workspace discovery includes a bounded preview of authorized Dataset names and
IDs. `datasets.hasMore` means additional Datasets may exist. For a request about
Workspace contents or data, inspect this preview and use `list_datasets` with the
exact Workspace ID when more names or Dataset descriptions are needed. Use the
Datasets skill before querying records. An empty Knowledge search or Document
list does not establish that a Workspace has no structured data.

`Viewer` permits Dataset and other Workspace-owned reads. `Contributor` adds
ordinary writes including Dataset records and imports. `Manager` adds Dataset
metadata, schema, lifecycle, links, and confirmed permanent removal, plus other
domain management subject to unchanged rules. These roles explain returned
access; the Knowlaborator plugin does not create or update Workspaces and does
not list or mutate memberships. Workspace creation, metadata, and membership
administration are browser-managed.

Personal Workspaces have one owner who is their only Manager. Collaborative
Workspaces use explicit membership. Organization administrators may administer
any Workspace in the browser control plane, but must add themselves there
before reading restricted content.

Never infer access from names, tags, OKF concepts, references, active
selection, or administrator status. Domain writes still send an exact
`workspaceId` or inherit one from an exact authorized parent. Cross-Workspace
references never grant access; omit or redact targets that cannot be resolved
under current Workspace access.
