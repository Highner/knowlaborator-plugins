# Distill a canvas

A distillation is a structured reading of an exact canvas revision with
item-level citations. The artifact is immutable and answers a person's frozen
request. It is not Knowledge; publication is a separate browser action.

## Distillation flow

1. The person freezes a distillation request in the browser with scope
   `canvas`, `lens` (plus the lens) or `selection` (plus item IDs). The
   request records `requestId`, `sourceCanvasRevision`, `sourceDigest` and the
   frozen item revisions. It expires after 24 hours and is consumed once.
2. Read `get_canvas` and `get_canvas_scene` with `revision` equal to the
   frozen `sourceCanvasRevision`, paging to the end. Read cited assets with
   `get_canvas_asset_file` when their content matters. Respect the scope: a
   `lens` or `selection` request covers only the frozen items.
3. Call `save_canvas_distillation`:

```json
{
  "requestId": "0193b2a0-…",
  "sourceCanvasRevision": 41,
  "agentClient": "claude-code",
  "idempotencyKey": "canvas-0193a9f1-distill-0193b2a0-01",
  "content": {
    "executive_summary": "…",
    "context_and_evidence": "…",
    "open_questions": "…",
    "confirmed_decisions": "…",
    "proposed_next_actions": "…",
    "conflicts_uncertainty_and_missing_information": "…",
    "citations": [
      { "itemId": "0193b0a2-…", "itemRevision": 3, "section": "context_and_evidence", "claimKey": "latency-doubled" },
      { "itemId": "0193b0c4-…", "itemRevision": 1, "section": "confirmed_decisions" }
    ]
  }
}
```

- All six text sections are required strings of at most 20,000 characters
  without control characters; `executive_summary` must not be blank. Write
  "None recorded." rather than omitting a section.
- `citations` is required (possibly empty). Each names an `itemId`, the exact
  `itemRevision`, one of the six section keys and an optional `claimKey`
  (≤ 200). At most 100 distinct items per section.
- Every citation must be inside the frozen source: the exact item and
  revision the request froze. A citation to a newer revision or an item
  outside the scope is rejected with `CANVAS_DISTILLATION_CITATION_INVALID`.
- `sourceCanvasRevision` must equal the request's revision.
  `CANVAS_DISTILLATION_REQUEST_CONSUMED` or `_EXPIRED` means a new request
  must be frozen in the browser; do not fabricate one.

Only `confirmed` decisions belong under confirmed decisions; proposed ones go
under open questions or next actions, labeled as proposed. Record disagreement,
missing evidence and unanswered questions honestly; do not resolve them in prose.

`Keep as canvas item` creates an editable `distillation` item derived from a
saved revision and remains a browser action.

## Prepare Knowledge publication

Publishing is a human browser action. An interactive agent may only store one
validated OKF candidate for a saved distillation revision with
`prepare_canvas_knowledge_publication` (canvas ID, distillation ID, request):

```json
{
  "distillationRevisionId": "0193b2c8-…",
  "targetMode": "create",
  "targetKnowledgeId": null,
  "targetKnowledgeVersion": null,
  "idempotencyKey": "canvas-0193a9f1-publish-0193b2c8-01",
  "okf": {
    "type": "decision_record",
    "title": "Roll back release 3.2 in the EU region",
    "bodyMarkdown": "…\n\nProvenance: orgapp://canvases/0193a9f1-…",
    "schemaVersion": "1.0",
    "status": "active",
    "metadata": { "canvasRevision": "41" }
  }
}
```

- `targetMode` is `create` for a new Knowledge record or `revise` with the
  exact `targetKnowledgeId` and current `targetKnowledgeVersion` the person
  selected. Never choose a revise target yourself.
- `okf.bodyMarkdown` must contain the exact provenance URI
  `orgapp://canvases/{canvasId}`; the candidate is rejected without it.
- Author the candidate with `$knowlaborator-knowledge` rules: Workspace
  guidance, stable subject, provenance, claim-level citations and local
  validation where available. Only confirmed decisions become claims; keep
  proposed decisions, conflicts and open questions labeled as such.

The response reports `valid`, `validation`, `validationDigest`, `sourceCurrent`
and `targetCurrent`; when `valid` is false, correct the candidate and prepare
again. The browser shows the exact candidate, target and provenance, and the
person publishes. Later canvas or draft changes never update published Knowledge.
