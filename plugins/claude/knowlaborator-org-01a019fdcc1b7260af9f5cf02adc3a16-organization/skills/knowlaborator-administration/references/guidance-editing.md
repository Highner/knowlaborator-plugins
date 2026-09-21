# Create or replace knowledge guidance

The guidance profile is a complete-replacement document. Use the live schema for
field validation and preserve all valid loaded fields unless the administrator
asks to change them.

## Load the baseline

1. Call `get_workspace_knowledge_guidance` with the exact target Workspace ID. Create only when `configured` is
   false.
2. Before an update, call `get_workspace_concept_guidance` with the same Workspace ID for every catalog
   entry in order. If any detail is missing or has a different revision, reload
   the compact profile and every detail.
3. Ask only about gaps that change boundaries, applicability, ordering,
   recommendations, or intentional removal.

## Shape the profile

- `workspaceDescription`: one required paragraph, 1–1,000 characters.
- `generalGuidance`: 1–4,000 characters covering durable concept boundaries,
  relationships, lifecycle, organization, and reuse.
- `concepts`: zero to 50 complete ordered entries; submitted order replaces the
  previous order.

For each entry:

- Omit `id` on create. On update, preserve each retained stable ID and omit it
  only for a genuinely new entry.
- Use a unique one-line `type`, a one-sentence `definition`, and concrete
  `useWhen` and `doNotUseWhen` boundaries.
- Decide every optional field deliberately. Populate it when one stable rule
  applies to nearly every concept of this type; otherwise use `null` for an
  optional scalar or `[]` for an optional collection. Do not add filler, repeat
  the definition, or encode a current organization fact.

## Protect the durable-form boundary

This profile describes reusable OKF types, not real organization facts or a
schema for all data. Apply [Work's durable-form rules](knowlaborator-skill://knowlaborator-work/references/durable-form.md).
Keep current people, products, projects, records, IDs and source bodies out of
the profile. Inspect Dataset structure only when needed and exposed; otherwise
request selection through the appropriate same-organization binding. Do not
query records, create schemas or persist facts merely to design guidance.

### Choose `pathPrefix`

Treat `pathPrefix` as the literal common directory under which future concepts
of this type should be placed. Derive it from the durable concept family, not
from a current instance, Workspace, team, project, date, or person.

1. Start with a short plural or collective directory in lower-kebab-case, such
   as `products`, `ingredients`, or `decisions`.
2. Add a stable parent only when it improves long-term navigation, such as
   `governance/decisions` or `research/findings`.
3. Store only the common directory prefix. Do not include a concept slug,
   placeholder segment, generated ID, filename, or `.md`; concept authoring
   chooses the unique suffix later.
4. Use `null` when the type legitimately spans unrelated directories and no
   useful common prefix exists. Do not force all types into one generic folder.

Keep the value bundle-relative and at most 500 characters, without a leading or
trailing slash, empty, `.`, or `..` segments, backslashes, controls, or a `.md`
suffix. For example, use `governance/decisions`, not
`/governance/decisions/`, `governance/decisions/<decision>`, or
`governance/decisions/example.md`.

### Fill the other optional fields

- `recommendedTags`: Recommend zero to 20 literal, reusable tags that improve
  cross-type grouping or retrieval. Prefer a small controlled vocabulary. Do
  not put instance names, IDs, dates, confidential facts, or a redundant copy
  of `type` in the list. Each tag is one line of at most 100 characters.
- `recommendedFrontmatter`: Recommend zero to 20 type-specific metadata fields.
  Each item is `{ "field": "...", "guidance": "..." }`: `field` is the exact
  YAML key; `guidance` explains the value shape, controlled vocabulary,
  applicability, and when to omit it. Use a field name matching
  `[A-Za-z_][A-Za-z0-9_.-]*`, at most 100 characters, and guidance of at most
  500 characters. Do not repeat generic fields merely because OKF supports
  them. Never recommend Knowlaborator-managed identity, organization,
  Workspace, membership, permission, creator, timestamp, revision, audit, or
  indexing fields.
- `recommendedBodySections`: Recommend zero to 20 ordered, plain heading labels
  that give nearly every concept of the type a useful body structure. Use one
  unique heading per item, without Markdown heading markers, each at most 200
  characters. Omit sections that would invite empty boilerplate.
- `relationshipGuidance`: Use up to 2,000 characters to explain durable link
  choices: which concept types may be linked, why, in which direction, and
  under what condition. Describe relationship semantics, not current targets;
  never invent IDs, assert inaccessible targets, or imply that links grant
  access. Use `null` when no type-wide relationship rule is useful.

Use the live field limits if they differ. Keep every collection unique
case-insensitively.

### Check the complete shape

A metadata-only example for a `Product` type could use this shape:

```json
{
  "type": "Product",
  "definition": "A product offering that the organization develops or maintains.",
  "useWhen": "Use for durable knowledge about one product offering as a whole.",
  "doNotUseWhen": "Do not use for an ingredient, a one-time decision, or a specific research finding.",
  "pathPrefix": "products",
  "recommendedTags": ["offering"],
  "recommendedFrontmatter": [
    {
      "field": "lifecycle_stage",
      "guidance": "Use one approved lifecycle-stage value; omit it when the stage is unknown."
    }
  ],
  "recommendedBodySections": ["Overview", "Audience", "Evidence", "Open questions"],
  "relationshipGuidance": "Link to Ingredient concepts only when they are part of the product, and to Decision concepts when they record a decision that materially affects it."
}
```

This example is structural guidance, not organization knowledge or a default
taxonomy. Adapt or omit every value based on the reviewed organization profile.

Follow exact live limits and field errors for all optional recommendation
collections.

## Review and save

Before create, present the complete proposal. Before update, present a complete
diff including reordered and omitted entries. A current instruction to save
that reviewed proposal authorizes the mutation; otherwise ask after the review.

Call `create_workspace_knowledge_guidance` without concept IDs, or
`update_workspace_knowledge_guidance` with the exact Workspace ID, complete ordered profile, and
exact loaded revision. Use one fresh idempotency key and reuse it only for an
identical uncertain retry.

On configuration-state or revision conflicts, reload the complete baseline and
reconcile explicitly. Never switch mutation paths blindly, overwrite a newer
revision, or drop an entry implicitly.
