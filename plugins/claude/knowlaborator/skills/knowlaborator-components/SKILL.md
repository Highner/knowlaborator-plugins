---
name: knowlaborator-components
description: Find and invoke approved Components, or author and govern Component revisions where permitted.
---

# Knowlaborator Components

Use the Component MCP tools for discovery, authoring, upload, and governance.
Prefer `create_component_from_source` for agent-authored revisions so the
server packages and stores the source over the existing MCP connection.
Do not open a browser, inspect the web application, or download its JavaScript
assets to discover schemas or create a Component. The authoring reference below
contains the host contract; the exposed tool schemas define request fields.

For discovery, use `search_components` and `get_component` for a relevant exact
revision. Invoke a requested approved shared Component or the creator's
validated private revision with its declared input schema, host contract,
owning Workspace, and data scope. Prefer inline rendering through
`invoke_component`. Present the returned web fallback only when inline
rendering is unsupported or a viewer must configure a connection; open it only
when requested.

Read [component-queries.md](references/component-queries.md) when working with
okf_query or dataset_query bindings. Never execute bundle code locally, invent
SQL, broaden declared scope or reveal inaccessible targets.

Read [component-authoring-and-governance.md](references/component-authoring-and-governance.md)
only for authoring, upload, revision, fork, publication review or removal, and
only where the corresponding tools are exposed. For creation or revision, also
read [component-host-contract.md](references/component-host-contract.md) for
the manifest, ZIP requirements, bridge example, and external-data limits.
When the Component should also appear on the Stage, read
[component-stage-templates.md](references/component-stage-templates.md) and add
a `stage.json` template to the bundle.
Loading this skill grants no authority; permissions still apply independently
to the Component, its owning
Workspace, and its Dataset.
