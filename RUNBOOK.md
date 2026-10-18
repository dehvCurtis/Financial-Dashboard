# RUNBOOK: routine work

## Every pay day

Ask for the pay stub. Add a row to paystubs (all lines, including year to date), check C4 (arithmetic), C7 (deposit matches), update income_pay and contributions if the percent changed. If the stub shows a new year to date, the plan year tile moves.

## Every week (optional but it sharpens drift)

Ask for the balance of every account on the same day, plus card balances. Add rows to balance_log (history). The drift calc (C12) compares reported with expected and shows unexplained spending per account. Expect rough drift for the first 2 to 6 months; show counts, not trends, until there are 3 check points.

## Monthly check-in (the 1st)

1. Statements: card, bank, mortgage, utilities, retirement. Intake each (INTAKE_GUIDE.md).
2. Balances: replace liabilities.balance with the statement balance, add balance_log rows.
3. Run the correlation checks (CORRELATIONS.md). Each failure becomes a task.
4. Tasks: close the ones whose data arrived (Done only after the checks pass), snooze or re-date the rest.
5. History: add a scores_history row, a networth_history row, and a metric_snapshots row once that dataset exists.
6. Backup: save the JSON and the kit on Data and backup, verify, copy to Google Drive (BACKUP_GOOGLE_DRIVE.md). The page says "Saved" with the date.
7. Tell the user the next three moves.

## Every October or November

Contribution limits for the next year are announced. Add the row to contribution_limits with status published and the source; until then the page labels the prior year as an estimate. Refresh age_benchmarks and the other reference datasets when a new survey publishes (SOURCES_AND_BENCHMARKS.md).

## Every year

Review assumptions (growth rates, inflation, high rate cutoff), the profile answers, insurance, the milestones.

## Fix a wrong number

Do not edit blindly. Find the tile in TILES.md, read its source and as-of date, find the dataset row, and decide whether the row, a newer statement or the calc is wrong. Replace the row in place with the value the user gives (the user's amount stands until a statement proves otherwise), list every calc output that changed (tools/numbers.js and tools/calcdiff.js do this for code runs), run the checks, and publish only when the user says go.

## When something looks wrong

| Symptom | First look |
|---|---|
| A card says "Needs: X" | the Needs list on Data and backup; the dataset field named in the message |
| A number differs from a statement | the as_of_date and source of the row; checks C8, C12, C26 |
| Net worth moved a lot | the Where it changed step list on Home; assets_other as_of_date |
| A calc says "didn't run" | an input calc errored; run `node tests/inv_run.js` and read the first failure; RUNTIME_CONTRACT.md facts 1 to 5 |
| A dataset will not load | empty array, a non-object row, or over 256 KB (RUNTIME_CONTRACT.md) |
| The page is blank after a publish | publish order: datasets, documents, parts, shell last; read the debug panel |

## Before each code change

Work on a branch or a copy of src/ before large edits (the repo is under version control, but never commit a backup or a statement); run `node tools/depmap.js` to see which tests a change affects; keep the approved inventories (tests/lib/*_approved.json) in step with intended changes.
