# OKF knowledge authoring

Use this flow for an explicit concept create, revision, reorganization, or
strict local OKF validation. The requested non-destructive write needs no second
confirmation.

## Apply guidance progressively

1. Call `get_workspace_knowledge_guidance` with the exact target Workspace ID only while authoring or
   reorganizing knowledge. A not-configured result means to use generic open
   OKF.
2. Choose candidate types from the task and compact catalog. Load
   `get_workspace_concept_guidance` with the same Workspace ID only for types or stable guidance IDs
   that could shape the concept.
3. Treat guidance as advisory metadata, not organization facts, authorization,
   or a concept instance. Never change the guidance profile as a side effect.

## Validate and write

When authoring from a local OKF bundle, or when strict validation is requested,
run:

```text
python3 <skill-dir>/scripts/okf/validate.py <bundle-directory> --strict
```

Resolve `<skill-dir>` to the directory containing this skill's `SKILL.md`. The
validator is self-contained; never install PyYAML, create a virtual environment,
or modify the user's project to run it.

1. Resolve the stable subject, scope, sources, and epistemic status. Default a
   new concept to private; an update retains its existing access settings.
2. Search concise projections before writing. Retrieve complete candidates only
   when needed to decide create, material revision, no-op, ambiguity, or lack of
   edit permission.
3. Preserve authorized provenance, claim-level citations, unknown OKF fields,
   producer extensions, and unrelated content. Label estimates, hypotheses, and
   open questions honestly. New knowledge is `active` by default. Use `draft`
   only when the user explicitly asks to store it as a draft; do not use draft
   merely because the knowledge may be revised later.
4. Call `create_knowledge` with complete fields and one idempotency key, or call
   `update_knowledge` with complete fields and the current version precondition.
   On ambiguity, validation failure, missing permission, or conflict, do not
   overwrite, duplicate, change access, or edit guidance.
5. Report the stable ID and revision, an unchanged subject, or the exact safe
   reason no synchronization occurred.
