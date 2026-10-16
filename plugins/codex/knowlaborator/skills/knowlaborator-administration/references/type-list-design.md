# Design the type list with the administrator

The organization's concept types and relation types form one type list. Every
new type has a place in schema.org's hierarchy: it adopts a schema.org type (it
*is* that type), extends one (a narrower kind of it), or extends another listed
type. Only organization administrators change the list, in the browser
(Settings → Concept types). You never change it: you submit one proposal that an
administrator reviews, edits and applies there change by change. Until then it
is a draft; say so.

Both tools below are listed only to organization administrators.

- `search_schema_org_terms` reads the schema.org release OrgApp ships. Work only
  from its results, never from what you remember of schema.org: a term the
  release does not contain is refused.
- `propose_type_list_changes` submits the proposal. It replaces the
  organization's open proposal, if there is one.

## 1. Ask about the organization

Ask the administrator what the organization does, what it needs to keep track
of, and which modules it uses: Contacts, Org Chart, Projects, Datasets. Read
installed modules with `list_organization_modules`. Ask only about gaps that
change which types or relations the organization needs.

## 2. Read the current list

Call `get_workspace_knowledge_guidance` for each Workspace the design touches.
Its catalog lists the listed types with their aliases, base kind and `schemaOrg`
place (`term`, `relation`, the chain's term, own parents and the types it
inherits from); a type without `schemaOrg` has no place yet. `relations` lists
the relation types with their schema.org property and ends. Read a type's full
guidance, including its inherited fields, with `get_workspace_knowledge_guidance`
and its `type`.

## 3. Look up candidates

Call `search_schema_org_terms` with `query` for words the organization uses
(in English: schema.org's terms are English), or without one for the starter
list. Read promising ones with `term`: a type comes back with its ancestors,
the base kind it gives, the listed types that already adopt or extend it, its
data properties for fields and the properties that relate it to the listed
types. Keep the `typeListVersion` the call returns; the proposal needs it.

## 4. Propose types

For each thing the organization keeps track of:

- **Adopt** a schema.org type when it is exactly that type (`Organization`,
  `Product`, `Place`). Only one listed type per organization can adopt a term.
- **Extend** a schema.org type when it is a narrower kind of it: `Rohstoff`
  extends `Product`, `Wettbewerber` extends `Brand`. Several types may extend
  the same term.
- **Extend a listed or proposed type** (a `parent`) where a chain helps:
  `Wirkstoff` extends `Rohstoff`. A chain has at most three own levels.
- Reuse a listed type, or an alias of one, when it already covers the meaning;
  do not propose a second type for it.

Name each type in the organization's language. Give a one-sentence definition
that decides edge cases, plus use-when and do-not-use-when texts: agents
classify by the definition, and the schema.org place only says what the type is
in general. Never propose a base kind: the server derives it from the chain
(`Person` → person, `Project` → project while Projects is installed, otherwise
organization, `Organization` → organization, `Event` → event, anything else →
other).

## 5. Propose relations and fields

- **Relations.** Prefer a schema.org property that connects the types
  (`memberOf`, `worksFor`, `isPartOf`): the `term` read of a type lists them.
  Name the relation and its inverse in the organization's language. Prefer
  schema.org type ends (`schemaOrgTerm` in `sources` and `targets`), which
  accept every listed type below that term, including later ones, the CRM
  contacts, CRM accounts or Documents it covers, and the rows of Datasets that
  declare that term or one below it. An end schema.org does not list
  is allowed and shown as outside schema.org. One relation type per property:
  when one already adopts it, propose widening its ends in Settings instead.
  Choose cardinality `one` or `many`, and symmetric or hierarchical where they
  apply: schema.org states none of them. Ends, cardinality and flags cannot
  change after creation. When no property fits, propose an own relation type.
  Never ask agents to keep a reference at both ends.
- **Fields.** Recommend only the data properties the organization fills for
  nearly every record of the type, named by the property (`foundingDate`) with
  `schemaOrgProperty` set to it; own fields remain possible. A type inherits its
  ancestors' fields and cannot remove them. Never recommend what a linked
  Contacts or Org Chart record already holds (email, telephone, address, job
  title): records of `person` and `organization` types name that record in
  `about` instead.

## 6. Keep roles and rows out of the type list

- A role is a relation, not a type: "Lieferant" or "Kunde" is an `Organization`
  related to the organization by a dated relation, not a second kind of
  organization.
- A thing described mainly by fields (a product with GTIN, price and
  dimensions; an offer; a property with area and address) is a Dataset row, not
  knowledge. Recommend a Dataset that declares its schema.org type in the
  proposal's summary instead of a type; the proposal itself cannot create one.
  The administrator chooses *Use as a Dataset* for that type in Settings →
  Concept types, or a Workspace Manager creates it with `create_dataset` and
  `schemaOrgType`. Its fields come from the type's properties, and its rows
  count as instances of the type: schema.org type ends accept them, and the
  `term` read lists the Dataset under `declaredByDatasets` and among the
  relations it can take part in.

## 7. Place existing types

For an organization whose list predates schema.org places, propose a place for
each active type that has none (`place_type`). The place must agree with the
type's base kind: `person`, `organization`, `project` and `event` types need a
term that gives that kind, `other` and `document` types a term that gives
`other`. Name every collision the administrator has to resolve first, such as a
listed type whose name or alias is the term another type should adopt. Nothing
is placed automatically, and no record changes.

## Submit

Present the complete proposal to the administrator first: each type with its
place, definition and fields, each relation with its ends, and why. A current
instruction to submit that reviewed proposal authorizes the call; otherwise ask
after the review.

Call `propose_type_list_changes` with the changes in order (at most 100), the
`typeListVersion` you read, a new `operationId` and a short `summary` (at most
1,000 characters). The kinds are:

- `create_type`: `key` (unique in the proposal), `name`, `definition`,
  `useWhen`, `doNotUseWhen`, optional `fields`, and a place: `schemaOrgTerm` with
  `schemaOrgRelation` `adopts` or `extends`, or `parent` (a listed type's
  `typeId`, or a proposed type's `key`);
- `place_type`: `type` (a listed type's `typeId`) and a place;
- `recommend_fields`: `type` and `fields`;
- `create_relation_type`: `name`, `inverseName`, `definition`, `cardinality`,
  `symmetric`, `hierarchical`, `sources`, `targets` and, where one fits,
  `schemaOrgProperty`; an end names exactly one of `conceptTypeId`, `key`,
  `schemaOrgTerm`, `baseKind` or `recordKind`, and a source may be `required`;
- `adopt_property`: an own relation type's `relationTypeId` and the
  `schemaOrgProperty` it is.

Every change is checked as the browser checks it, and the first invalid one
refuses the whole proposal with the browser's code, such as
`KNOWLEDGE_SCHEMA_ORG_TERM_UNKNOWN`, `_TERM_CONFLICT` (the term is another
type's or relation type's name or alias), `_TERM_IN_USE` (another type or
relation type already adopts it), `_KIND_MISMATCH`,
`KNOWLEDGE_TYPE_PARENT_INVALID` (a cycle, or more than three own levels) or
`KNOWLEDGE_TYPE_LIST_PROPOSAL_INVALID`. Nothing is saved then. Correct the
change and resubmit with a new `operationId`; resending the same one with the
same changes returns the same proposal.

Then tell the administrator to review it in Settings → Concept types, where
they accept, edit or reject each change and apply the accepted ones in one
action. A change the list has since made impossible is shown as a conflict
there and is not applied.
