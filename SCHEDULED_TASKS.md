# SCHEDULED_TASKS: reminders and routines

Use Claude scheduled tasks when the account has them; otherwise use data_queue items dated the day. One reminder per routine, never two.

| Routine | When | Message text | Fallback |
|---|---|---|---|
| Monthly backup | the 1st, evening | "Monthly backup due. Open Data and backup, save the JSON and the kit, and tell Claude to put them in Google Drive together with the data_offpage files." | data_queue item dated the 1st |
| Monthly check-in | the 1st or the first weekend | "Monthly check-in: send this month's statements and balances." | task "Monthly check-in" |
| Pay day | each pay date (income_pay.first_pay_date and pay_schedule) | "Pay day: send the latest pay stub." | task |
| Weekly balances | one chosen weekday | "Send the balance of every account on the same day." | task |
| Limits refresh | October 15 | "Check whether next year's contribution limits are announced." | data_queue item |
| Benchmarks refresh | January 15 | "Check for new age benchmark sources." | data_queue item |

## Creating a scheduled task (what to say to the user)

Plain: "I can set a monthly reminder on the 1st at <time>. Each time it fires, a fresh session starts that knows only the message text, so the text has to stand alone. Do you want it?" Only create it after a yes. When confirming, say which approval setting the task got. If it will stop and ask, leave it that way: do not suggest automatic approval for a task that could upload, send or delete. If the user asks about automatic approval, explain the trade-off and let them decide in the task settings.

## Writing the message text for a fresh session

A scheduled run has no memory of this chat. The text must say: what the dashboard is (link or name), which bundle documents to read (CLAUDE.md, RUNBOOK.md), what to do (for the backup: ask the user for the files, verify, upload), what not to do (do not publish, do not change data without the user), and where to report. Never put personal figures in the text.

## Time of day

Ask the user. Default: evening. Time zone: the user's own; write it explicitly.

## What a scheduled run may do

A scheduled run has no user in the chat. It may remind, prepare and ask. It must not publish, share, upload to Drive, send email, delete, or change data on its own; those need the user's yes in a live turn. Mail and Drive connectors are optional and are used only for what the user asked for when creating the task.

## Stopping

The user can stop any routine at any time; say where (the scheduled tasks list). A routine that has not been answered for three cycles is paused by Claude with a one line note to the user.
