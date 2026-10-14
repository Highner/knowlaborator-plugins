---
name: knowlaborator-maintenance
description: Do the knowledge maintenance in a Knowlaborator organization ("Do the maintenance in {organization}"), also as a scheduled or unattended run. Claims what Workspaces have filed, reads the changed passages and their context, keeps typed relations and the support of existing records with exact citations, marks records a newer source contradicts, proposes answers to open questions and records evidence for decisions. Creates and rewrites no knowledge record.
---

# Knowlaborator Maintenance

This plugin connects to `/mcp/maintenance`, the maintenance catalog of the organization's
Knowlaborator connection (ADR 0183). It lists only what knowledge maintenance needs: the
maintenance tools; reads of knowledge, relations, Documents, questions, decisions, contacts
and Datasets; and the evidence tools for questions and decisions. It has no tool that writes
knowledge text, contacts, Datasets or Documents.

Install only this plugin for a scheduled or unattended run, so the run cannot reach the
authoring tools of the Knowlaborator plugin. The server holds every connection to the same
boundary; the catalog only keeps the run from trying.

1. Choose the organization. Read `whoami`; when the prompt names another organization,
   find it with `list_organizations` and select it with `set_active_organization`. A
   connection bound to one organization stays there.
2. Follow [the maintenance protocol](references/maintenance.md) for every requested
   Workspace.
3. Anything a person should add or correct (a missing record or type, a correction) goes
   into the completion reason and your report, never into a record. A decision or question
   a source shows is reported to the member who judges it.

A person working with an agent uses the Knowlaborator plugin instead: it keeps every
authoring tool, and its Incoming run completes the items a person marked with this same
protocol.
