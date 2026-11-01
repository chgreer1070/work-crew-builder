# Crew Status — STATUS.md

> Source of truth for mutable crew task and event state.

## Race-Aware Editing Contract

1. Use stable Task IDs from AGENTS.md. Do not create task configuration here.
2. Treat State History, Recently Completed, prior Weekly Summary rows, and automated notification events as append-only history. Never rewrite, reorder, or delete an existing historical row during an agent or builder write.
3. An ordinary agent may append its events and update only current In Progress or Pending rows whose Agent or Owner field is its own slug. A state change appends its transition event in the same delta before updating or removing the owned active row. It never changes another owner's row.
4. An explicitly approved `/crew-builder` modification is the only automated exception: it may update affected active ownership fields across source and target agents exactly as listed in the approved preview and mutation manifest. It may append the previewed transition event, but history remains append-only and every unpreviewed field is preserved.
5. For every write, read the latest file bytes and capture their SHA-256 hash or an equivalent version token. Build the smallest permitted row-level delta and apply it only with that hash or version as the unchanged-base precondition.
6. If the precondition fails or the file drifts, do not overwrite it. Re-read the newest content, merge only the permitted delta, capture a new hash or version, and retry. If conditional writes are unavailable, return the delta and base hash for an approved writer instead of making a blind write. For `/crew-builder`, any drift voids the approved preview; merge the intent in memory, issue a revised exact preview, and obtain new approval before retry.
7. After a successful write, re-read and verify the intended delta, current active ownership, and that every prior historical entry remains byte-for-byte unchanged and in order. Never restore a stale snapshot over a newer file.
8. Agents append notification resolution events rather than rewriting prior notification events. The human may clear reviewed notifications as permitted by crew-sop.
9. Only the designated weekly-summary owner, executive-intelligence, updates the current week's aggregate row. All other agents update only their task-owned rows.

## Notifications

| Event ID | Timestamp | Agent | Task ID | Type | Message | Action needed | State |
|---|---|---|---|---|---|---|---|

No entries yet.

## Recently Completed

Append completion records; never rewrite or delete prior records.

| Completion ID | Completed | Agent | Task ID | Result | Output location | Evidence |
|---|---|---|---|---|---|---|

No entries yet.

## In Progress

| Task ID | Agent | Started | Last updated | Current step | Next step |
|---|---|---|---|---|---|

No entries yet.

## Pending

Use for blocked work or input needed. The owning agent updates only its row.

| Task ID | Agent | Since | State | Needed from | Exact need or blocker | Priority |
|---|---|---|---|---|---|---|

No entries yet.

## Weekly Summary

Append a new row for each week; do not replace earlier weeks.

| Week of | Prepared by | Completed | In progress | Pending | Outcomes and risks |
|---|---|---:|---:|---:|---|

No entries yet.

## State History

This table is append-only and records every task-state transition, including changes caused by crew modifications.

| Event ID | Timestamp | Agent | Task ID | From | To | Reason or evidence |
|---|---|---|---|---|---|---|

No entries yet.
