# Change CRM records

Treat an account as a shared CRM relationship record, never a login account or tenant
organization. Search with `search_crm` before creating. Preserve exact record IDs and
revisions, and reload after a revision conflict. Restore an archived account or contact
with `set_crm_status` before adding activity, links or follow-ups.

## Contacts from Document ingestion

When Knowledge's [document ingestion](../../knowlaborator-knowledge/references/document-ingestion.md)
routes a high-confidence person from an explicitly ingested ordinary Document, treat
that ingestion request as authorization for the contact write; do not request separate
per-contact confirmation or expand extraction to other named people. Search with the
strongest source-exact discriminator, preferring email or telephone and then an exact
name with unambiguous affiliation or role. Reuse an exact active match, restore an exact
archived match rather than creating a duplicate, and skip and report ambiguous matches.
Create a contact only when no match exists. Preserve only source-explicit fields and do
not overwrite conflicting fields on an existing contact. Assign an account only when an
exact active account already exists; never create an account as a side effect of
contact extraction. Link each created or reused contact to the exact source Document
through an eligible target returned by `search_crm_link_targets`.

## Bank details

Accounts and individual contacts may each own optional `bankDetails` with `recipient`,
`iban` and `bic`. They are shared with everyone authorized to read that record. Use only
user-supplied bank details; do not infer them from affiliation. Recipient and IBAN are
required together; BIC is required outside the EEA. Include the complete saved
`bankDetails` in full record updates to retain it; null removes it. Never store a
payment amount or payment reference in the CRM record.

## Interactions and links

Record only the intended shared interaction summary and occurrence time. A mail locator
is set with `set_crm_interaction_mail_source` from the protected message reference of an
authorized mail read; never copy mail content into it or expose it to another member.

Before recording an interaction, identify every named organization resource that is
material to it, such as a vessel, policy or document. Search the live eligible targets
with `search_crm_link_targets` using the most specific retained name: business objects
such as vessels and policies as `knowledge`, files as `document`. After recording, link
the interaction itself to every unambiguous intended target with `link_crm_resource`; do
not substitute its account or contact as the link subject. The interaction write is
incomplete until these links succeed; report both the interaction and the linked
targets. If candidates are ambiguous, ask which exact target to use. If no exact target
exists, say so rather than silently omitting the link or linking a merely related record.

When the user explicitly requested knowledge capture in the same task, create or
reconcile the knowledge subject through `$knowlaborator-knowledge`, then link the
interaction to the returned exact knowledge ID. CRM activity by itself never authorizes
creating knowledge. Create follow-ups as ordinary ToDos through `$knowlaborator-work`.

## Archive and removal

Archive and restore (`set_crm_status`) are reversible. Permanent removal with
`remove_crm_record` is destructive and administrator-only; accounts and contacts must be
archived first. Load the current revision, explain the effect, and obtain current
confirmation. Confirm the exact association before `unlink_crm_resource` or removing a
personal mail locator. Report the returned record and revision or the exact association
removed.
