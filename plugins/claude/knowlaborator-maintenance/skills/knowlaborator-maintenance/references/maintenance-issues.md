# Issue findings and developments

Read when a passage raises a concrete unresolved situation or changes the evidence for an existing issue. Use the leased submit/apply and checkpoint flow in [the maintenance protocol](maintenance.md).

- Look for explicitly raised problems and inferred situations across sources: conflicting
  decisions and supplier terms, incompatible delivery dates, a handover without an owner,
  a vacant required approval role, or a removed capability underlying a decision. Require
  a concrete unresolved situation and plausible consequence, not a generic unknown.
- For a historical source, search for later developments on the same subject before describing its uncertainty as still open. Absence of a resolution in that source alone is not evidence of a current problem. Keep the period and any unresolved evidence gap explicit.
- Before any new issue call `check_issue_candidates` with its exact title, context,
  consequence, closure criteria, evidence and relatedKnowledgeIds. Review the returned
  bounded shortlist (at most 25 across readable Workspaces and all states), not the full
  history; there are no further candidate pages. Read the returned matches and evidence.
  If suppressedByIssueId is present, do not create or reword this dismissed finding.
  Apply a person's dismissal reason to similar irrelevant findings too. Only if it does
  not apply to a materially distinct situation, explain why in dismissalReasonDoesNotApply
  for that candidate. No reason still means the same situation stays dismissed.
  Same outcome, subject, scope and time period means
  the same issue, regardless of source, sender, wording or explicit/inferred origin.
- Reuse matches. Submit `record_issue_evidence` with the target, current version and
  development, or `propose_issue_resolution` when closure criteria may be met. Retain
  every distinct authorized contribution. Resolved/dismissed matches are not recreated;
  describe new evidence for a person to reconsider. Never reopen, merge or close yourself.
- If none matches, submit `create_issue` with an `issue` payload, completed candidate
  receipt and explicit assessments of returned matches. New is the human triage state;
  there is no separate report queue. Claims are exact source observations; their values
  become source contributions. Keep interpretation and remaining uncertainty explicit.
  Cite every material premise of an inference, including across sources. No assignments,
  Open flag or review dates: the server routes follow-up to the eligible marker/source
  owner, otherwise caller. An impossible route is blocked, never silently discarded.
- A changed relevant shortlist or candidate version requires a fresh comparison. Refresh only
  your own still-proposed issue change under its current lease/fence with
  replacesProposalId and expectedProposalVersion; no human confirmation is needed for
  that narrow refresh. It may become evidence on a newly found match. Applied changes
  and proposals awaiting a person's decision cannot use this refresh. Preserve checkpoints
  and stop after three contention retries with a blocked assessment.
- Additional structured premises may use issue payload evidence of kind org_position,
  org_unit, project or dataset_record with the current recordVersion and an attributed
  contribution. They supplement required exact source claims; they cannot smuggle extra
  mail, Knowledge or Document passages outside those claims. Include the same combined
  premises in candidate checks. Changed/unavailable structured records require another read.
- Use the same evidence in the candidate check and submission: an IssueEvidenceInput per
  exact claim citation, Source kind/id/revisionId/quote/startOffset/representationId and
  blockId (the block ordinal as text), Contribution equal to that claim's Value. The
  server refreshes the comparison at apply; stale receipts need another comparison.

An unresolved issue is an evidence target; a resolved issue may supply ordinary filed
context. Reading a source in Incoming and Maintenance does not justify another issue,
duplicate evidence or another notification.
