---
name: knowlaborator-maintenance
description: Do the knowledge maintenance in a Knowlaborator organization ("Do the maintenance in {organization}"), also as a scheduled or unattended run. Claims what Workspaces have filed, reads the changed passages and their context, keeps typed relations and the support of existing records with exact citations, marks records a newer source contradicts, discovers explicitly raised and inferred issues across sources and updates existing issues and records evidence and drafts for decisions across sources. Preserves discussion and waiting states without finalizing a choice or duplicating a clearly documented decision.
---

# Knowlaborator Maintenance

This plugin connects to `/mcp/maintenance`, the maintenance catalog of the organization's
Knowlaborator connection (ADR 0183). It lists only what knowledge maintenance needs: the
maintenance tools; reads of knowledge, relations, Documents, issues, decisions, contacts
and Datasets; and the evidence tools for issues and decisions. Its knowledge
creation operations are supported decision drafts and New issues through leased maintenance changes. General knowledge, CRM,
Dataset and Document authoring tools remain outside this catalog.

Install only this plugin for a scheduled or unattended run, so the run cannot reach the
authoring tools of the Knowlaborator plugin. The server holds every connection to the same
boundary; the catalog only keeps the run from trying.

1. Choose the organization. Read `whoami`; when the prompt names another organization,
   find it with `list_organizations` and select it with `set_active_organization`. A
   connection bound to one organization stays there.
2. Follow [the maintenance protocol](references/maintenance.md) for every requested
   Workspace.
3. For decisions, follow the protocol's five situations and use the draft change kind:
   retain all contributing sources, reuse matching decisions, and keep waiting dependencies
   explicit. A clearly documented decision needs no copy without a coordination purpose.
   Reuse attaches no new sources; follow the protocol's evidence flow for that decision's
   standing and verify any needed writes before reporting new sources preserved.
   Other missing records or corrections go into the page review assessment and your report.
   Find explicit and inferred issues, compare all existing states across readable Workspaces,
   and add new evidence to a matching issue. Create New only after a complete candidate check.
4. Email is restricted to already indexed snapshots and exact persisted Process selections,
   with or without optional instructions. Use list_marked_inputs/prepare_marked_input for
   selected items. Never browse/search the live inbox, expand threads or read unselected mail.
   Missing evidence remains a gap for the person to select. No provider fallback.

A person working with an agent uses the Knowlaborator plugin instead: it keeps every
authoring tool, and its Incoming run completes the items a person marked with this same
protocol.
