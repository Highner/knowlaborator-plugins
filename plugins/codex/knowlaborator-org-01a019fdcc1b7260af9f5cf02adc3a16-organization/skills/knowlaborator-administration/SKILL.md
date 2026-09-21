---
name: knowlaborator-administration
description: Administer membership, organization settings, Workspace taxonomy guidance and semantic retrieval configuration.
---

# Knowlaborator Administration

Reuse retained organization context and never guess selection. Administration
capabilities remain server-enforced for the active membership.

Use administration tools for invitations, members and roles, organization
settings, usage, audit, and other control-plane operations. Explain the
last-administrator safeguard before a role change or removal that may encounter
it. Organization administration never grants content reads in a restricted
Workspace. Administrators add themselves through the ordinary audited browser
Workspace flow when data-plane access is required.

Telegram provider-account status, configuration, same-bot token rotation, and
confirmed removal are organization-admin-only but browser-owned in
**Organization Settings -> Telegram**. They are not MCP administration tools.
Never ask the user to paste a bot token into chat or pass one to a tool; never
claim that a token, webhook secret, credential route, or provider identifier can
be read back. Explain safe account state and route the user to the browser.
Connecting a Telegram group is separately browser-owned and still requires exact
Manager authority in the target Collaborative Workspace. No plugin tool name or
tool count changes for provider administration.

Treat `revoke_invitation` and `remove_member` as destructive: reload and name
the exact target, explain the effect, and obtain current confirmation
immediately before the call.

For Workspace taxonomy create or replacement, read
[guidance-editing.md](references/guidance-editing.md). Load every ordered entry
at the same revision before replacing a complete profile. For removal, reload
the exact Workspace profile, explain that Documents and Knowledge remain, and
obtain fresh confirmation before remove_workspace_knowledge_guidance.

For knowledge policy, providers, profiles, coverage or retrieval configuration,
read [semantic-retrieval.md](references/semantic-retrieval.md). Credentials stay
in the browser. Report only safe returned state and exact affected scope.

Use Playbooks for Playbook authoring. Domain work requires its functional skill
and an appropriate binding; Administration does not grant restricted content
access or permit a silent switch to another surface.
