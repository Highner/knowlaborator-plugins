# Create or replace knowledge guidance

A Workspace's guidance profile holds only its description and general
guidance. Concept types live on the organization's one type list, which only
organization administrators change in the browser (Settings → Concept types).
No tool can add, rename, map, assign or retire a type.

## Load the baseline

1. Call `get_workspace_knowledge_guidance` with the exact target Workspace ID.
   Create only when `configured` is false. Its catalog lists the types usable
   in that Workspace, read-only.
2. Ask only about gaps that change the Workspace's purpose, concept
   boundaries, relationships, lifecycle or reuse.

## Shape the profile

- `workspaceDescription`: one required paragraph, 1–1,000 characters.
- `generalGuidance`: 1–4,000 characters covering durable concept boundaries,
  relationships, lifecycle, organization, and reuse across the listed types.
  Refer to types by their listed names; do not define new ones here.

## Propose a concept type

When the administrator asks for a new type, or a definition for a listed type
that has none, draft it for them to enter in Settings → Concept types. Say
that it is a draft until they save it there. First check the catalog and its
aliases: if a listed type already covers the meaning, propose an alias or a
sharper definition instead of a second type.

For each proposed type:

- Use a short, unique name in the organization's language and a
  one-sentence `definition` with concrete `useWhen` and `doNotUseWhen`
  boundaries. Agents classify by the definition, so make it decide edge cases.
- Name the base kind: `person` or `organization` for people and parties
  (say whether every record must link to a Contacts or Org Chart record),
  `project`, `document` for cards about one Document, `event` for things
  with a date, otherwise `other`. The base kind cannot change later.
- Decide every optional field deliberately. Populate it when one stable rule
  applies to nearly every concept of this type; otherwise leave it empty. Do
  not add filler, repeat the definition, or encode a current organization
  fact.

## Relation types

Relation types (how records of the listed types relate, such as Produkt
`enthält` Rohstoff) are on the same list. Organization administrators maintain
them in the browser only, in the Relation types section of Settings → Concept
types; no tool creates, renames or retires one. `get_workspace_knowledge_guidance`
returns the ones a Workspace can use.

When asked, draft one for the administrator to enter there: a name read from
source to target, an inverse name read back, a one-sentence definition with
use-when text, the allowed source and target types (concept types, base kinds or
any), cardinality `one` or `many`, symmetric or hierarchical if it applies, and
which source types require it. Say that ends, cardinality and flags cannot change
after creation. Guidance never asks agents to keep a reference at both ends.

Promoting existing links or ID fields into a relation type, and marking a field
superseded on a type, are done by organization administrators in the browser
only ("Promote links" in the relation type's editor). Agents may propose which
links or fields mean which relation, for the administrator to review there, but
never promote. Once a field is superseded, propose dropping it from the type's
recommended frontmatter.

## Protect the durable-form boundary

Guidance and type proposals describe reusable OKF types, not real
organization facts or a schema for all data. Apply [Work's durable-form rules](../../knowlaborator-work/references/durable-form.md).
Keep current people, products, projects, records, IDs and source bodies out of
the profile and the proposals. Inspect Dataset structure only when needed and exposed; otherwise
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
  indexing fields, nor the reserved fields `about`, `candidate_type`,
  `starts_on` and `ends_on`.
- `recommendedBodySections`: Recommend zero to 20 ordered, plain heading labels
  that give nearly every concept of the type a useful body structure. Use one
  unique heading per item, without Markdown heading markers, each at most 200
  characters. Omit sections that would invite empty boilerplate.
- `relationshipGuidance`: Use up to 2,000 characters to explain durable link
  choices: which concept types may be linked, why, in which direction, and
  under what condition. Describe relationship semantics, not current targets;
  never invent IDs, assert inaccessible targets, or imply that links grant
  access. Use `null` when no type-wide relationship rule is useful. Point to a
  listed relation type where one exists.

Use the live field limits if they differ. Keep every collection unique
case-insensitively.

### Check the complete shape

A metadata-only proposal for a `Product` type could use this shape:

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
  "recommendedBodySections": ["Overview", "Audience", "Evidence", "Related questions"],
  "relationshipGuidance": "Link to Ingredient concepts only when they are part of the product, and to Decision concepts when they record a decision that materially affects it."
}
```

This example is structural guidance, not organization knowledge or a default
taxonomy. Adapt or omit every value based on the reviewed organization profile.
Related questions must link to independent question records; never prescribe an
inline list that duplicates their question text, status or answer.

Follow exact live limits and field errors for all optional recommendation
collections.

## Review and save

Before create, present the complete proposed profile. Before update, present a
complete diff. A current instruction to save that reviewed proposal authorizes
the mutation; otherwise ask after the review.

Call `save_workspace_knowledge_guidance` with the exact Workspace ID,
`workspaceDescription` and `generalGuidance`: without `expectedRevision` to
create the first profile, or with the exact loaded revision to replace it.

On configuration-state or revision conflicts, reload the baseline and reconcile
explicitly. Never overwrite a newer revision.
