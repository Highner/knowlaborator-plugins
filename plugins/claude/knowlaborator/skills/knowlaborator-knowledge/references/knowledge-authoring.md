# OKF knowledge authoring

Use this flow for an explicit concept create, revision, reorganization, or
strict local OKF validation. The requested non-destructive write needs no second
confirmation.

For a decision OKF record, also read the title, body and record-reference guidance
in [decisions-and-reviews.md](decisions-and-reviews.md).

## OKF timestamp baseline

Use [OKF v0.2 at revision ad30107c](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/ad30107c31c06aec8a7d5636e0d1058118604e6f/SPEC.md).
When present, timestamp fields (`generated.at`, `verified[].at`, `stale_after`,
`sources[].last_modified`, and `usage_window.from`/`.to`) use ISO 8601 datetimes
with explicit offsets, such as `2026-09-23T00:00:00Z`. Do not invent timestamps.
Knowledge exchange exports review dates at midnight UTC. It accepts legacy
date-only `stale_after` with a compatibility warning and maps offset datetimes
to their UTC calendar date, warning when it discards time of day.
The local validator checks structure and producer lints; a successful check
does not certify optional timestamp semantics.

## Choose the type from the list

Each organization has one concept type list, maintained only by its
administrators in the browser. Choose from it; never invent a type.

1. Call `get_workspace_knowledge_guidance` with the exact target Workspace ID
   when creating, reorganizing, or changing the type of a concept. Its catalog
   lists the types usable in that Workspace, with aliases and one-sentence
   definitions, whether or not the Workspace has its own guidance. A narrow
   edit to an exact existing record with unchanged type needs no lookup.
2. Choose by definition, not by name. Load `get_workspace_concept_guidance`
   with the same Workspace ID only for types that could shape the concept, and
   apply their boundary, path prefix and structural recommendations.
3. When no listed type fits, use the closest listed type or the built-in
   `note` and write the type you wanted in the `candidate_type` metadata field.
   Tell the user which type was missing. Only an organization administrator
   adds types; a candidate on three or more records is shown to them.
4. Read `typeWarnings` in the write result. `type_alias_resolved` means the
   server stored the listed name for an alias; nothing to do.
   `type_unknown` means the type is not usable in that Workspace: if one of the
   suggestions fits, correct the type with `update_knowledge`; otherwise report
   it. `type_deprecated` means the type is retired: choose another for new
   records. `candidate_matches_existing_type` names a listed type to use
   instead of the candidate.
   Organizations that refuse instead of warning answer with the same reasons
   as errors: `KNOWLEDGE_TYPE_UNKNOWN` (with suggestions),
   `KNOWLEDGE_TYPE_DEPRECATED`, `KNOWLEDGE_BASE_KIND_RULE`,
   `KNOWLEDGE_SUBJECT_EXISTS` and `KNOWLEDGE_DUPLICATE_SUBJECT`. Correct the
   write and retry once with a new idempotency key; do not retry unchanged.
5. Follow the type's base kind. For a `person`, `organization` or `project`
   type, name the record the concept describes in the `about` metadata field:
   `orgapp://crm/contacts/{id}` or `orgapp://org-chart/people/{id}` for a
   person, `orgapp://crm/accounts/{id}` for an organization,
   `orgapp://projects/{id}` for a project. Resolve the exact record first; a
   value that names no record you can read is refused. People and
   organizations outside the organization's records need no `about` unless the
   type requires a link. Put a person's `email` and an organization's
   `website` in the metadata when known: a matching record must then be named
   in `about`. A `document` type links its source as
   `orgapp://documents/{id}`; an `event` type gives `starts_on` as
   `YYYY-MM-DD` or a date and time with an offset, and optionally `ends_on`.
6. Base-kind warnings arrive in `typeWarnings` too. `subject_exists` names the
   record to put in `about`; `duplicate_subject` names the existing concept to
   revise instead of keeping two; `base_kind_rule` names the missing or invalid
   field. Fix them with `update_knowledge` when you have the facts; otherwise
   report them.
7. Treat guidance and the list as advisory metadata, not organization facts,
   authorization, or a concept instance. Never change guidance as a side effect.

## Relate records

A typed relation states how two records relate, such as Produkt `enthält`
Rohstoff. Only administrators define relation types; agents never add one.

1. Choose only from the `relations` that `get_workspace_knowledge_guidance`
   returns for the target Workspace. Pick by definition and use-when, and
   check the allowed source and target types. When none fits, keep a plain
   Markdown link and tell the user which relation was missing.
2. Store each relation once, from its source: the record the name reads from.
   The other record shows it under the inverse name. Never write it again from
   the other end and never keep a reference at both ends.
   `KNOWLEDGE_RELATION_REVERSED` names the right direction.
3. Send new relations with the record in `relations` of `create_knowledge` or
   `update_knowledge` (`relationType`, `targetKnowledgeId`, optional `from` and
   `until` dates). A new record of a type that requires a relation must bring
   it. For existing records use `change_knowledge_relations` with `add` and
   `remove` (relation IDs from `get_knowledge`); it creates no revision.
4. Read relations with `list_knowledge_relations`, or find related records with
   `list_knowledge` and `relatedKnowledgeId` plus `relationType` (the name for
   outgoing, the inverse name for incoming). Check what exists before adding.
5. Read `relation_type_mismatch` and `relation_required` in a write's
   `typeWarnings` or `change_knowledge_relations`' `warnings`; in
   organizations that refuse they arrive as `KNOWLEDGE_RELATION_TYPE_MISMATCH`
   and `KNOWLEDGE_RELATION_REQUIRED`. `KNOWLEDGE_RELATION_DUPLICATE`,
   `_CARDINALITY` (a `one` relation already has a current edge: remove it
   first; edges cannot be edited) and `_CYCLE` are always refused. Correct the
   request and retry once with a new idempotency key.
6. `get_workspace_concept_guidance` lists in `supersededFields` the fields a
   relation replaced on that type, such as `artist_profile_id` replaced by
   `von`. Never set or change such a field; send its relation instead. A write
   that does gets `field_superseded`, or `KNOWLEDGE_FIELD_SUPERSEDED` in
   organizations that refuse. A value the record already has may stay.
7. Keep plain Markdown links for mentions, evidence and anything no relation
   type covers. A link creates no relation, and a relation needs no link.

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
2. Before creating a concept or resolving an ambiguous target, search concise
   projections and retrieve complete candidates only as needed. For an exact
   existing record, read its current version once to decide material revision,
   no-op, or lack of edit permission; do not repeat discovery.
3. Preserve authorized provenance, claim-level citations, unknown OKF fields,
   producer extensions, and unrelated content. Label estimates, hypotheses, and
   uncertainty honestly. Every actionable open question belongs in its own `question`
   record; follow [questions.md](questions.md), then replace embedded questions with
   links. Do not use decision plans for this. New knowledge is `active` by default. Use `draft`
   only when the user explicitly asks to store it as a draft; do not use draft
   merely because the knowledge may be revised later.
4. Call `create_knowledge` with complete fields and one idempotency key, or call
   `update_knowledge` with complete fields and the current version precondition.
   On ambiguity, validation failure, missing permission, or conflict, do not
   overwrite, duplicate, change access, or edit guidance.
5. Report the stable ID and revision, an unchanged subject, or the exact safe
   reason no synchronization occurred.
