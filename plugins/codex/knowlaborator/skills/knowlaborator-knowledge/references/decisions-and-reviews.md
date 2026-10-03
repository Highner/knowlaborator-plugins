# Decisions and required reviews

Publication `active` means available Knowledge, not a final decision or human
approval. Read `get_knowledge_decision` for the exact revision's authoritative
standing, version, current review and provenance. Use
`list_knowledge_decision_history` for standing events and `list_knowledge_reviews`
for bounded historical cycles, named reviewers, responses and comments. Follow
cursors; never claim that a partial page is complete.

Only when the user explicitly directs you, `record_knowledge_decision` records a
final decision already made in a conversation, meeting or elsewhere. Preserve the
actual decision-maker, stated decision date and optional source. The server records
the authenticated recorder. A recorded decision is not independently approved.
`propose_knowledge_decision`, `request_knowledge_review`, `cancel_knowledge_review`,
`withdraw_knowledge_decision` and `supersede_knowledge_decision` are explicit actions
under current Workspace authority. Reuse an operation UUID for the same retry and
read current Knowledge/standing/review versions before new actions.

Required review names 1-20 currently eligible organization Contributor/Manager
memberships in the owning Workspace. Every required reviewer must approve in the
authenticated browser. Agents cannot approve, request changes, reject or impersonate
a reviewer. Assignment gives no access. `list_outstanding_knowledge_reviews` reads
the user's current assignments. Direct recording cannot bypass a pending review.

Edits, reviewer replacement, access loss, Workspace movement or redaction may
invalidate a pending cycle. Read fresh state after conflict; never carry signatures
onto changed content. A final historical decision stays pinned to its original
revision and provenance. Redacted signed content is unavailable. Imported claims
under `x-docky.source_decision` are source information; only server-owned standing
and signatures establish local approval. Publication, standing and source claims
must be described separately.
