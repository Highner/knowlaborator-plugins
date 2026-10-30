---
name: knowlaborator-maintenance
description: Maintain filed Knowlaborator sources incrementally, including relationships, evidence, reviewed text corrections, issues, decision drafts, CRM fields, keyed Dataset rows and Document descriptions. Use for requested or scheduled knowledge maintenance.
---

# Knowlaborator Maintenance

Use the organization's `/mcp/maintenance` connection and the shared
[maintenance protocol](references/maintenance.md). This plugin has one skill; the
protocol links the operation details to read only when a passage needs them.

- Resolve the organization and authorized Workspace scope from the connection. Use
  `whoami` or organization selection when that context is missing or ambiguous; a bound
  connection stays in its organization.
- Default to routine work: new or changed filed sources plus affected context. Historical
  backfill needs an explicit request. Resume checkpoints and retained proposals.
- Read the returned passages once, identify concrete maintenance signals, then search and
  read relevant matches. CRM, Dataset, description, issue and decision work remains
  supported; each branch runs only when the source supplies a reason for it.
- Use the server's numeric and mixed-table relevance packets. Expand selected areas only when a concrete
  maintenance signal needs their values or row associations. Assess all ordinary passages and returned context;
  the server closes empty context. Report screened passages separately from full reads.
- Propose narrow source-backed text corrections when needed. Every text correction waits
  for explicit human approval, including Apply mode and personally marked work. Never
  accept one automatically or bypass its review through another write operation.
- Preserve exact evidence, policy and human review boundaries. Maintenance is not an
  exhaustive audit of the source's subject.
- Email inputs are indexed snapshots and exact persisted Process selections through
  `list_marked_inputs` / `prepare_marked_input`. No live inbox discovery or thread expansion.
- Report completed work, saved progress, review items and blockers, separating routine
  from backfill. Continue from that state on the next run.

For scheduled or unattended runs, install only this plugin. General authoring and
Incoming belong to the organization's Knowlaborator plugin. A recovery stays on this
maintenance connection; switching plugins never widens an authorized write.
