# CORRELATIONS: the checks that tie the numbers together

A correlation check compares two numbers that must agree (a stub against a deposit, a card statement against a balance). It runs in code, never by eye. Result per check: pass, needs (data missing, no number is guessed) or fail. A fail names the rows involved and becomes a task.

Design text of every check, with formulas and executors: ARCHITECTURE.md section 16. This file lists the checks the page runs today (generated from the code), the ones that live elsewhere, the data they read, and one planned dataset.

## Checks run by the page (27 at this version)

| Check | Name | Calc that runs it |
|---|---|---|
| C1 | Net worth identity | checks_calc |
| C2 | Holdings sum | checks_calc |
| C3 | Retirement start balance | checks_calc |
| C4 | Stub arithmetic | checks_calc |
| C5 | Stub to contributions | checks_calc |
| C6 | Stub to income | checks_calc |
| C7 | Stub to cash | checks_calc |
| C9 | DTI inputs | checks_calc |
| C22 | Mortgage to statement | checks_calc |
| C23 | Form 1098 to statements | checks_calc |
| C10 | LTV inputs | checks_calc |
| C11 | Bills to cash | checks_calc |
| C12 | Drift identity | checks_calc |
| C13 | Goals | checks_calc |
| C14 | Dates | checks_calc |
| C15 | Dashes and privacy (datasets) | checks_calc |
| C16 | Reward rows | checks_calc |
| C17 | Payoff simulation | checks_calc |
| C18 | Benchmark rows | checks_calc |
| C19 | Score inputs | checks_calc |
| C21 | Plan-year tile | checks_calc |
| C24 | Statement requests in sync | checks_calc |
| C25 | Bills and debts not double counted | checks_more_calc |
| C26 | Totals identity | checks_more_calc |
| C27 | Bill statements belong to a bill | checks_more_calc |
| C28 | Nothing assumed to exist | checks_more_calc |
| C29 | Card statements belong to a card | checks_more_calc |

The page checks are two calcs: `checks_calc` and `checks_more_calc` (CALCS.md). Each returns one row per check with `status`, a plain sentence, and the rows or values that disagree. The Data and backup page and the Home "Needs" count read them.

Numbering note. The ids C1 to C29 match ARCHITECTURE.md section 16 except where the build changed a meaning: in the code C23 ties the year end interest form to the mortgage statements, C28 is "nothing assumed to exist" (a debt, bill or account the user answered no to must have no rows), and C29 ties card statements to a card. Checks C8, C20 and C28 of the design (card to liability at ingestion, regression, runtime shape) are run by Claude at ingestion or by the test suite, not by the page.

## Checks that live outside the page

| Check | Where it runs | How to run it |
|---|---|---|
| Runtime shape (every dataset loads, size, titles, no test globals, no calc errors) | tests/data_rules.js, tools/engine.js | `node tests/data_rules.js` |
| Page invariants (34 rules on the data, the fixture, and every dataset emptied one at a time) | tests/inv_run.js, page/invariants.js (also run inside the page self test) | `node tests/inv_run.js` |
| Backup file integrity (hashes, counts, schema, no dashes, no account numbers) | tools/verify_backup.js | `node tools/verify_backup.js <backup.json or kit.zip>` |
| Privacy and dash scan | tests/static_scan.js | `node tests/static_scan.js` |
| Docs against code (every rendered tile in TILES.md, every dataset and calc named) | tools/check_docs.js | `node tools/check_docs.js` |

## Relationship map in one table

| If this changes | These must be rechecked |
|---|---|
| liabilities (balance, payment, rate) | C9, C17, C22, C25, C26, DTI, payoff plan, scores |
| bills | C11, C25, C26, C27, obligations, runway |
| paystubs | C4, C5, C6, C7, C21, take-home |
| balance_log, cash_events, bank_flows | C12 (drift), C7, C11, runway |
| assets_other, holding_snapshots | C1, C2, C3, net worth, retirement start balance |
| contribution_plan, contribution_limits | C21, the 401(k) percent card |
| statements (card, bank, bill, mortgage) | C8, C24, C27, C29 |
| profile_answers | C28, the Needs list, the interview |

## PLANNED: metric_snapshots

Status: PLANNED. Not in the 39 datasets of stage 6i, no page reads it. A template with one all-null row is in templates/metric_snapshots.json so a future build can adopt it without a schema change.

Purpose: a monthly record of the headline numbers so the dashboard can draw a trend that does not depend on rebuilding history from every statement. It is a history dataset (dated rows are allowed here, as with scores_history and networth_history).

| Field | Type | Unit | Meaning |
|---|---|---|---|
| snapshot_id | text | id | S1, S2, ... never reused |
| as_of_date | text | date YYYY-MM-DD | the day the numbers were read |
| net_worth | number | USD | from nw_rows |
| total_assets | number | USD | |
| total_debt | number | USD | |
| dti_pct | number | percent | headline debt to income |
| monthly_obligations | number | USD | bills plus debt payments |
| cash_runway_months | number | months | |
| financial_score | number | points 0 to 100 | |
| risk_score | number | points 0 to 100 | |
| coverage_pct | number | percent | data coverage from coverage_calc |
| source | text | text | "month end snapshot" or the statement it came from |
| note | text | text | optional |

Rules when it is built: one row per month at most; written by Claude after the monthly check-in, never by the page; a number that is Needs on that day is null, never zero; a correction replaces the row in place; add the calc `snapshot_trend_calc`, a trend chart on Progress, and a check that net_worth on the latest row equals nw_rows within a cent on the day it was written. Add it to the backup table and the catalogue in the same change (BACKUP_FORMAT.md, 8 steps in PUBLISH.md).
