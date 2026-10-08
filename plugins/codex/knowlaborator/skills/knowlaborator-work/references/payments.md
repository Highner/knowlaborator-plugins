# Payments

OrgApp prepares payments; it never transfers money. There are two levels:

- **Payment suggestions** are core and work in every organization (ADR 0125). A
  suggestion holds only known, source-grounded details: recipient, IBAN, optional BIC,
  amount, Currency, reference and ScheduledDate (the due date). Complete EUR details show
  the person a SEPA QR code to scan in their banking app; other currencies show the
  details only. Nothing records whether a suggestion was paid.
- **Saved payments** belong to the optional Banking module (`banking`). They add paid
  status, Kontoflux matching and shared responsibility, and their tools are listed only
  where Banking is installed.

## Suggest a payment

- For an agenda item, `add_agenda_items` and `update_agenda_item` accept `PaymentDraft`.
  A ToDo created from the item keeps the suggestion.
- An explicitly authorized `create_todo` accepts `PaymentDraft`. For an existing ToDo,
  `set_todo_payment_draft` adds or replaces the suggestion; PaymentDraft=null removes it.
  A ToDo with a saved PaymentId refuses a suggestion.
- Never invent missing details; leave them absent. The person completes them in the app.
- In a Collaborative Workspace, everyone with access sees the same suggestion. Name who
  pays in the ToDo, or use Banking's shared payments below.

## Save a payment (Banking)

- Read `list_payments` (follow its pagination) and any existing ToDo PaymentId before
  preparing a payment, and reuse the same payment for the same obligation.
- `create_payment` saves source-grounded recipient, IBAN, amount, reference, optional
  BIC, a supported Currency and optional ScheduledDate in an authorized Workspace.
  Reuse the same UUID and fields on retries.
- An explicitly authorized `create_todo` can use PaymentId in the same Workspace. For
  an existing task use `set_todo_payment` with ExpectedPaymentId from the read;
  occurrence is the default, future also needs ExpectedSeriesVersion and changes the
  recurring template. Future tasks receive separate unpaid payments.
- For an agenda item, `add_agenda_items` and `update_agenda_item` accept PaymentId
  instead of PaymentDraft, never both. With Banking, the person saves a suggestion through
  Add payment in the browser. Agenda maintenance cannot link or write ToDos.
- Read `get_payment` for the current paid status. Payment confirmation, task completion
  and agenda processing are separate. EUR supports the SEPA QR handoff and explicit
  Kontoflux matches; other supported currencies expose details and manual paid status.

## Shared payments

Invoice number is optional. With an invoice, creation reuses the payment for the same
resolved obligation. Without one, separate new payment UUIDs create separate saved
payments; reuse the same UUID on retries and reuse an existing PaymentId or explicitly
resolved SharedWorkId when colleagues are coordinating the same commitment. Do not
invent an invoice number to satisfy validation. Validation failures name their fields.

Resolve shared obligations with `resolve_shared_work` in the owning Collaborative
Workspace and reuse the returned IDs and targets across members. Before a shared payment
handoff, obtain the user's authorization and take responsibility with `claim_payment`,
using the current version and one retry OperationId. Another member's claim blocks the
attempt. Record awaiting_confirmation after an external attempt; claims never expire.
Release only on explicit confirmation that no transfer occurred, and reconcile an
uncertain attempt before another payment. A QR or a saved PaymentId never proves a
completed transfer.

The browser can select privately saved bank accounts from profile settings to fill the
payer's holder name, or accept payer text. These personal account details are not exposed
to MCP. Use the same holder identity across accounts and colleagues. For create_payment,
an empty Obligation.SupplierReference defaults to the recipient; use a distinct supplier
when they differ. resolve_shared_work still requires explicit supplier identity. Choosing
a payer account does not select or authorize a debit account in an external banking app.
