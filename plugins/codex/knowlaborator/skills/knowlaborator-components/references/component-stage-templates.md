# Stage templates (stage.json)

Read this reference when a Component should also appear on the person's Stage.
A Stage template is one optional file, `stage.json`, at the root of the bundle
beside `index.html` and `preview.html`. The Stage renders it natively with its
own primitives, theme and motion. Nothing in it executes, it has no network
access, and every value it shows is a field of a record the Stage agent binds
and the viewer can already read.

Upload validates the template. A bundle with an invalid `stage.json` is
rejected with `STAGE_TEMPLATE_INVALID` and the first problem, for example
`blocks[2].value starts with a declared input; 'deal' is not one.` A revision
without `stage.json` is not offered to the Stage. Governance is unchanged: the
creator can use a draft on their own Stage, and everyone else sees the
published revision.

## Shape

```json
{
  "inputs": {
    "account": { "kinds": ["crm_account"], "label": "Account" },
    "contract": { "kinds": ["dataset_record"] },
    "events": { "kinds": ["calendar_event"], "many": true, "optional": true }
  },
  "minimumSpan": { "columns": 1, "rows": 2 },
  "blocks": [
    { "type": "gauge", "anchor": "health", "label": "Health", "value": "contract.health", "max": 100 },
    { "type": "bars", "max": 100, "items": [
      { "anchor": "price", "label": "Price sensitivity", "value": "contract.price_sensitivity", "tone": "warn" },
      { "anchor": "engagement", "value": "contract.engagement" }
    ] },
    { "type": "fields", "items": [
      { "value": "account.relationship_type" },
      { "label": "Term ends", "value": "contract.end_date", "format": "date" }
    ] },
    { "type": "avatars", "input": "account", "label": "People" },
    { "type": "list", "input": "events", "detail": "start" },
    { "type": "badge", "label": "At risk", "tone": "risk", "when": { "value": "contract.health", "below": 50 } }
  ]
}
```

- `inputs`: up to 4 named inputs (`a-z`, digits, `_`). `kinds` lists the record
  kinds the agent may bind: knowledge, document, dataset_record, crm_account,
  crm_contact, project, todo, org_chart_person, org_chart_unit,
  org_chart_position, calendar_event, dataset, decision. `many` accepts up to 8
  records; `optional` may stay unbound.
- Values are field paths `input.field_key`. Field keys are the ones `stage_read`
  returns for that record kind (Dataset records use their schema keys; CRM
  accounts expose name, relationship_type, status, website, tags and
  contact_count). `input.$title` is the record's title. A path into a `many`
  input reads its first record. A missing value renders as a dash.
- `minimumSpan`: the smallest cell span the card may take (columns 1-3, rows 1-4).
- Up to 12 blocks, 8 items per `bars` or `fields`, 16 anchors, 16 KiB in total.
  Unknown properties are rejected.

## Primitives

| Type | Properties | Shows |
| --- | --- | --- |
| `stat` | `value`, `label`, `format` (number, percent, date, text), `unit`, `tone` | One large value. |
| `gauge` | `value`, `max` (number or path, default 100), `label`, `tone` | A value against a maximum. |
| `progress` | `value`, `max`, `label`, `tone` | A share as a bar. |
| `bars` | `items` of `{ value, label, tone, anchor }`, `max` | Up to 8 bars on one scale. |
| `fields` | `items` of `{ value, label, format, anchor }` | Label and value rows. |
| `list` | `input`, `detail` (a field key), `label` | The records of one input. |
| `avatars` | `input`, `label` | People: an account's contacts, a unit's positions, or bound contacts. |
| `badge` | `label`, `tone`, `when: { value, below \| above \| equals }` | A label shown only while the condition holds. |

Tones are neutral, good, warn and risk. Labels default to the field's label.

## Anchors

Any block or item may name an `anchor` (lower-case letters, digits, hyphens).
The Stage agent highlights it with `{"op": "highlight", "card": "…", "anchor":
"price"}` and can end connections on it. Name the anchors an explanation will
point at; a template without anchors can still be placed and connected as a
whole card.

## Testing a template

Upload the revision, then on your own Stage ask the Stage agent to place the
draft component (`stage_find` with kind `component`, `stage_read` for its
inputs, `stage_apply` with `component` and `inputs`). Submit it for publication
when it reads well; reviewers see the same file.
