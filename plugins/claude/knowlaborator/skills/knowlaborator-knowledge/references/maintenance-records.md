# CRM, Dataset rows and Document descriptions

Read when a passage supplies identifiable contact/account fields, values for a keyed Dataset, or the identity of an undescribed Document. Use the leased submit/apply and checkpoint flow in [the maintenance protocol](maintenance.md).

- **Contacts and accounts.** `create_crm_contact` needs the person's name and an email
  address or phone number in the quoted passage; a sender or recipient line counts.
  An active contact that already has the address, or the number, is refused with its
  `existingContactId`: update that contact instead. `create_crm_account` needs the
  account's name in the passage and no account with that name or website. Updates
  (`update_crm_contact`, `update_crm_account`) carry the record's `expectedRevision`
  and set only typed values: a contact's job title, phone number or account, an
  account's website while it is empty. Names and email
  addresses identify records and never change. A source in a personal Workspace
  writes only that Workspace's records.
  Company roles come from dated typed relationships. Read `list_crm_account_roles`,
  including roles on readable profiles linked by `about`; reuse the exact company.
  No fixed Prospect/Customer/Partner/Supplier/Other field remains. Tags and descriptions
  are context, never role evidence. Keep former and future periods; unknown dates stay
  unknown. Cite the source when adding or ending a relation. An announced end and a
  proposed settlement date are separate facts: keep the settlement waiting until accepted.
  A relationship ending already recorded there needs no duplicate decision unless that
  record adds distinct coordination or follow-up.
- **Dataset rows.** Only Datasets of the Workspace that declare key fields take rows.
  `create_dataset_record` names a value for every key field; `update_dataset_record`
  names only the fields it changes, never a key field or long text, with
  `expectedRecordRevision`. A row with the same key values is refused with its
  `existingRecordId`. Quote text values as the passage writes them.
- **Newer sources.** An empty field fills from any source. A value changes only with a
  source dated after the record's last change: an email by when it was sent, an
  attachment by its email; filed messages and notes have no date and only fill empty
  fields. When the record changed later, leave the value and name it in the
  page review assessment.
- **Document descriptions.** `describe_document` gives a Document without a
  description one line, at most 500 characters, naming what it is (kind, parties,
  date, subject), not what it says, with the Document's `expectedVersion`. A
  description someone wrote is never replaced. A Document needs no knowledge card.
- These are change kinds of `submit_maintenance_change`, never calls of tools that
  share their names. They rest on asserted or reported claims citing current source
  revisions and never wait for a conflict review.
- Name a missing record or a correction only for a lasting fact about one specific
  subject. Forecasts, KPIs, prices, balances, status snapshots and dated summaries of
  one source ("Stand …") stay in their source, which stays readable and searchable.
