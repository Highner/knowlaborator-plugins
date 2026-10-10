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

Install Knowlaborator Stage as well to let your agent present records on the Stage page:
it places record cards, highlights exact passages and connects them while you watch. Its
connection follows the organization in which you opened the Stage.

Install Knowlaborator Stage Lab to try the experimental generative UI surface.
ChatGPT or Claude authors interactive charts, calculators and diagrams inside an
MCP Apps conversation. Its connector is https://orgapp-production-1b56.up.railway.app/mcp/stage-lab.
This prototype requires an MCP Apps host; terminal-only clients receive text.

The repository contains generated skills, client-specific manifests, and public MCP
URLs. It contains no credentials, installation keys, documents, or organization data.
Installing a plugin grants no access: Knowlaborator checks the signed-in account,
membership, and Workspace permissions on every request.

Membership changes are reflected by Knowlaborator without republishing this marketplace.
For help, contact info@appligator.de or visit https://orgapp-production-1b56.up.railway.app/plugin/support.