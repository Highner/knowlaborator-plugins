---
name: knowlaborator-calendar
description: Read local or connected calendars, or create, change and remove authorized events.
---

# Knowlaborator Calendar

Local calendars follow Workspace access; connected accounts, calendars and
events remain owner-private, including from administrators. Use only exposed
tools; Realm calendar availability depends on its owner-scoped opt-in.

For reads, resolve sources with list_calendar_sources, then use
list_calendar_events with an end-exclusive range of at most 62 days.
Preserve each source's failure and moreAvailable. Use get_calendar_event for
an exact detail, keeping opaque external references with their source.
get_today includes sources whose IncludeInToday preference is enabled.

Read [event-changes.md](references/event-changes.md) only for an event mutation.
Account setup and private ICS subscriptions are browser-only.
