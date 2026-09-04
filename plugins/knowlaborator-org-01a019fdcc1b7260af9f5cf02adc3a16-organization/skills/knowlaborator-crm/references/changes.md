# Change CRM records

Treat an Account as a shared CRM relationship record, never a login account or
tenant organization. Search before creating. Preserve exact record IDs and
revisions, and reload after a revision conflict. Restore an archived Account or
contact before adding activity, links, or follow-ups.

When `$knowlaborator-documents` routes a high-confidence person from an
explicitly ingested ordinary Document, treat that ingestion request as
authorization for the contact write; do not request separate per-contact
confirmation or expand extraction to other named people. Search with the
strongest source-exact discriminator, preferring email or telephone and then an
exact name with unambiguous affiliation or role. Reuse an exact active match,
restore an exact archived match rather than creating a duplicate, and skip and
report ambiguous matches. Create a contact only when no match exists. Preserve
only source-explicit fields and do not overwrite conflicting fields on an
existing contact. Assign an Account only when an exact active Account already
exists; never create an Account as a side effect of contact extraction. Ensure
each created or reused contact is linked to the exact source Document through
an eligible target returned by `search_crm_link_targets`.

Record only the intended shared interaction summary and occurrence time. A
mail locator is the caller-owned connected-mail account ID plus an opaque
message reference; never copy mail content into it or expose it to another
member.

Before recording an interaction, identify every named organization resource
that is material to it, including a vessel, policy, document, or Case. Search
the live eligible CRM targets with the most specific retained name: search
business objects such as vessels and policies as `knowledge`, files as
`document`, and Cases as `workflow_case`. Retrieve the exact candidate when
needed to disambiguate. After recording, link the interaction itself to every
unambiguous intended target; do not substitute its Account or contact as the
link subject. Treat the interaction write as incomplete until these links
succeed and report both the interaction and the linked targets. If candidates
are ambiguous, ask the user which exact target to use. If no exact target
exists, say so rather than silently omitting the link or linking a merely
related record. Link only targets returned by `search_crm_link_targets`.

When the user explicitly requested knowledge capture as part of the same task,
route that part through `$knowlaborator-knowledge`, reconcile or create the
canonical knowledge subject there, and then link the CRM interaction to the
returned exact knowledge ID. CRM activity by itself never authorizes creating
that knowledge subject.

Route follow-up creation to `$knowlaborator-notices-and-todos`; CRM has no
separate task model.

Archive and restore are reversible. Permanent Account, contact, or interaction
removal is destructive: load the current revision, explain the effect, and
obtain current confirmation. Confirm the exact association before unlinking a
resource or removing a personal mail locator. Report the returned record and
revision or the exact association removed.
