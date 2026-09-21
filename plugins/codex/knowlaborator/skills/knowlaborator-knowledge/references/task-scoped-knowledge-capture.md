# Task-scoped knowledge capture

Use this flow only when the current Knowlaborator Work request is explicitly
knowledge-producing: research, analysis, comparison, brainstorming, concept
development, decision support, direct save, or import. That request authorizes
curation of relevant durable results, not capture of the raw conversation.

Do not inspect unrelated turns for reusable facts. Do not run this flow for a
calendar change, mail draft, message, ToDo update, CRM mutation, Case,
Component invocation, or other ordinary operation merely because Work is
available in the shared plugin. Skip capture when the user opts out.

Never retain credentials, authentication material, raw personal
communications, prompts, response transcripts, hidden reasoning,
conversational filler, discarded alternatives, or transient task state.
Require explicit authoring intent for highly sensitive personal information.

## Curate and reconcile

1. Build a bounded candidate packet containing the stable subject, proposed
   claim, future-use reason, status, relevant dates, volatility, scope, and
   authorized provenance. Keep subjects atomic and preserve evidence and
   uncertainty.
2. Search for an equivalent visible record before writing. Retrieve complete
   candidates only as needed to decide create, material revision, no-op,
   ambiguity, or missing edit permission.
3. Create only when no equivalent visible concept exists. Update only an
   editable stable-subject match with a material change. Preserve its Workspace,
   access, creator, unknown fields, producer extensions, unrelated content, and
   authorized citations.
4. Validate candidates independently. Use one stable idempotency key for each
   create and the freshest current-version precondition for each update. Never
   duplicate after ambiguity, missing permission, validation failure, conflict,
   or uncertain outcome.
5. Report only the records created, materially revised, already current, or
   safely left unchanged, plus any exact reason a candidate could not be
   curated.

The knowledge-producing request authorizes these scoped non-destructive writes;
do not interrupt it with a generic save prompt. Preserve every confirmation
boundary for lifecycle, access-changing, or destructive operations.
