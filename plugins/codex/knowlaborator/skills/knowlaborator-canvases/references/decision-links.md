# Canonical decision links

`link_canvas_decision` binds one current Canvas decision item to an exact current
Knowledge ID and revision in the same Workspace. Read both objects and expected
versions first. `unlink_canvas_decision` removes that exact link. Neither operation
records a final decision or grants access. Reuse idempotency keys for identical
retries. Geometry preserves the link; changed decision text detaches it.

`list_canvas_linked_decisions` independently reads current authorized canonical
standing for the exact stored references. Scene and delta projections may carry
linked metadata, but a Canvas's local proposed/confirmed payload never establishes
Knowledge approval. A changed, moved, deleted or redacted reference must not fall
back to a newer or different Knowledge revision.

Linked browser confirmation records the canonical Knowledge decision with trusted
recorder/source and the decision date when the person enters one (an empty date means
unknown); it cannot bypass pending required review and creates no extra
Canvas item revision or local confirmer signature. Agents cannot confirm Canvas
items or submit human reviewer responses. To record a decision already made at the
user's explicit direction, use the Knowledge decision operation and read its
provenance. Unlinked Canvas decisions retain their existing browser-only behavior.
