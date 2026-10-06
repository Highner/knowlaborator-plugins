# Payments

Payments belong to the optional Banking module (`banking`). OrgApp prepares and tracks
payments; it never transfers money.

## Prepare a payment

- Read `list_payments` (follow its pagination) and any existing ToDo PaymentId before
  preparing a payment, and reuse the same payment for the same obligation.
- `create_payment` saves source-grounded recipient, IBAN, amount, reference, optional
  BIC, a supported Currency and optional ScheduledDate in an authorized Workspace.
  Reuse the same UUID and fields on retries.
- An explicitly authorized `create_todo` can use PaymentId in the same Workspace. For
  an existing task use `set_todo_payment` with ExpectedPaymentId from the read;
  occurrence is the default, future also needs ExpectedSeriesVersion and changes the
  recurring template. Future tasks receive separate unpaid payments.
- For an agenda item, `add_agenda_items` and `update_agenda_item` accept PaymentId or
  partial PaymentDraft metadata, never both. Unknown details stay absent; the person
  completes Add payment in the browser. Agenda maintenance cannot link or write ToDos.
- Read `get_payment` for the current paid status. Payment confirmation, task completion
  and agenda processing are separate. EUR supports the SEPA QR handoff and explicit
  Kontoflux matches; other supported currencies expose details and manual paid status.

## Shared payments

Resolve shared obligations with `resolve_shared_work` in the owning Collaborative
Workspace and reuse the returned IDs and targets across members. Before a shared payment
handoff, obtain the user's authorization and take responsibility with `claim_payment`,
using the current version and one retry OperationId. Another member's claim blocks the
attempt. Record awaiting_confirmation after an external attempt; claims never expire.
Release only on explicit confirmation that no transfer occurred, and reconcile an
uncertain attempt before another payment. A QR or a saved PaymentId never proves a
completed transfer.
