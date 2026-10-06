---
name: knowlaborator-people
description: Read or maintain shared CRM accounts, contacts and interactions, or the company org chart of people, units, positions and reporting lines.
---

# Knowlaborator People

Two optional modules hold people. Their tools are listed only where the module is
installed.

## Contacts & accounts (CRM, module `crm`)

An account is a CRM relationship, not a login or tenant. Use `search_crm` (kind
`account` or `contact`) for discovery, `get_crm_account` and `get_crm_contact` for exact
reads, and `list_crm_interactions`, `get_crm_interaction` and `list_crm_resources`
within an authorized record. Preserve revisions, archived state and redacted
references. Personal mail locators remain owner-private. A read never triggers a write.

Read [crm.md](references/crm.md) for record, interaction or link changes, including
high-confidence contacts routed from Document ingestion. Follow-ups are ordinary ToDos;
CRM has no separate task model.

## Org Chart (module `org-chart`)

People in the org chart exist independently of accounts; an optional membership link
identifies an account without granting any permission. Full active members read and
Full organization administrators write. Use `list_org_chart_entries` (kind `person`,
`unit` or `position`), `get_org_chart_entry` and `get_org_chart`. Read
[company-chart.md](references/company-chart.md) before a change or when completeness matters.
