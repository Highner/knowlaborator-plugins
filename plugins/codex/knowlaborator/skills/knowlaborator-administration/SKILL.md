---
name: knowlaborator-administration
description: Administer membership, organization settings, audit, optional modules, Workspace knowledge guidance and the knowledge policy.
---

# Knowlaborator Administration

Organization administration tools are listed only to Full administrators and stay
server-enforced for the active membership. Reuse retained organization context and
never guess selection.

Use them for invitations, members and roles, organization settings, usage and audit.
Explain the last-administrator safeguard before a role change or removal that may
encounter it. Organization administration never grants content reads in a restricted
Workspace; administrators add themselves through the audited browser Workspace flow
when they need data access. Treat `revoke_invitation` and `remove_member` as
destructive: reload and name the exact target, explain the effect, and obtain current
confirmation immediately before the call.

For Workspace knowledge guidance, read
[guidance-editing.md](references/guidance-editing.md). Load every ordered entry at the
same revision before replacing a complete profile. For removal, reload the exact
profile, explain that Documents and Knowledge remain, and obtain fresh confirmation
before `remove_workspace_knowledge_guidance`.

For the knowledge policy or semantic retrieval, read
[semantic-retrieval.md](references/semantic-retrieval.md). Embedding providers,
profiles and credentials are managed in the browser only.

## Optional modules

Use `list_organization_modules` when installation or availability matters. Modules such
as `org-chart`, `projects`, `crm`, `banking` and `datasets` are installed independently.
Installation, deactivation and reactivation belong to administrators in
**Organization Settings -> Modules**; the MCP query reports state and never changes it.

Installing a module never grants access, changes Workspace roles or gives an
administrator private content access. Deactivation keeps authorized module records and
history readable while module writes stop; linked core ToDos, Documents, Knowledge and
decisions continue under their usual rules. Never propose permanent purge as a
deactivation step. For a request returning `MODULE_NOT_INSTALLED` or
`MODULE_DEACTIVATED`, reload availability, explain the state and route an explicit
lifecycle request to the browser.
