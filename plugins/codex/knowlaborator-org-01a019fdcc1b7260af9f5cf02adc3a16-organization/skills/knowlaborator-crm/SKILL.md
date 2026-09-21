---
name: knowlaborator-crm
description: Read or maintain shared CRM Accounts, contacts, interactions and resource associations.
---

# Knowlaborator CRM

An Account is a CRM relationship, not a login or tenant. Use search_crm_accounts
and search_crm_contacts for discovery; get_crm_account and get_crm_contact for
exact reads. Use list_crm_interactions, get_crm_interaction and list_crm_resources
within an authorized record. Preserve revisions, archived state and redacted
references. Personal mail locators remain owner-private.

Read [changes.md](references/changes.md) only for record or association changes,
including high-confidence contacts routed by Document ingestion. A read never
triggers those writes. ToDos owns follow-ups; CRM has no separate task model.
