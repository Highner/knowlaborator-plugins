# Knowlaborator plugins for Codex and Claude Code

Add this one Git repository as a marketplace in either client:

https://github.com/Highner/knowlaborator-plugins

```sh
codex plugin marketplace add https://github.com/Highner/knowlaborator-plugins
claude plugin marketplace add https://github.com/Highner/knowlaborator-plugins
```

Install Knowlaborator and sign in when prompted. The plugin discovers only the
organizations available to the signed-in user. If more than one is available,
use `list_organizations` and `set_active_organization` to select the active context.

The repository contains generated skills, client-specific manifests, and public MCP
URLs. It contains no credentials, installation keys, documents, or organization data.
Installing a plugin grants no access: Knowlaborator checks the signed-in account,
membership, and Workspace permissions on every request.

Membership changes are reflected by Knowlaborator without republishing this marketplace.
For help, contact info@appligator.de or visit https://orgapp-production-1b56.up.railway.app/plugin/support.