---
name: knowlaborator-notices-and-todos
description: Read or maintain notices, ToDo assignments, deadlines and completion state.
---

# Knowlaborator Notices and ToDos

Use list_notices and get_notice for authorized notices, and list_todos for
bounded ToDo reads. Preserve Workspace, complete assignee set, deadline type,
status, Case ownership and cursors. For a daily snapshot use get_today and
respect each section's moreAvailable and failures.

Read [notices-and-todos.md](references/notices-and-todos.md) only for changes or
when deadline, mention, pinning, ownership or transition semantics matter.
Report the saved header, description, assignees, deadline and state after writes.
