# BACKUP_FORMAT: schema mo-backup-2

A backup is everything needed to rebuild the dashboard on a new Artifact: every dataset, the dataset documents, the dashboard title, the schema, and hashes that prove nothing changed. Two forms exist and both are made by the Data and backup page (card bk-card, buttons bk-json "Backup (JSON)" and bk-csv "Rebuild kit (zip)"):

- the backup JSON, one file: `dashboard-backup-YYYY-MM-DD.json`
- the rebuild kit, a zip: `dashboard-kit-YYYY-MM-DD.zip`

Schema version string: `mo-backup-2`. The tool `tools/verify_backup.js` checks both forms against this document.

## 1. Rules

1. Only values taken from the user's documents are stored. No account numbers, routing numbers, SSNs, tax ids, loan ids, street addresses or family names. The page strips fields named like account_number, acct_no, routing*, ssn, tax_id, tin, loan_id before export.
2. A backup is refused when any dataset failed to load, and the missing ids are named. A partial backup is never offered as complete.
3. A backup counts only when the page says "Saved" with the date. "Not saved" shows the reason (declined, rate limited, unavailable, delivered) and a visible fallback: ask Claude in chat for "the monthly backup kit".
4. Due on the 1st of each month. With no save on record the first is due on the first 1st after launch. The last confirmed date is kept in the per-user database document `data/users/<id>/backup` (field `last_saved`), with browser storage key `mo_last_backup` as a fallback.
5. No em or en dash and no exclamation mark in any exported text.
6. CSV is a view. Restore only from JSON.

## 2. The JSON file

One object with these top-level keys, all required:

| Key | Type | Content |
|---|---|---|
| schema_version | text | `mo-backup-2` |
| exported | text | ISO time of the export |
| manifest | object | see section 4 |
| docs | object | per dataset id: title (30 characters at most), description, source_name, source_kind, updated {at, by} |
| meta | object | the dashboard title: `{ "title": "..." }` |
| schema | object | per dataset id: list of `{field, types}` |
| datasets | object | per dataset id: the array of row objects |

Optional keys: `restore_steps` (the RESTORE.md text), `calcs` (worked out tables for checking only), `page_files` (page parts or null), `task_overlay` (task actions not yet folded into tasks, or null).

Each dataset is a non-empty array of flat row objects. Nested cells appear as parsed objects (the page parses the allow-listed columns back from JSON text before export).

## 3. The rebuild kit (zip)

| Path | Content |
|---|---|
| datasets/<id>.json | one per dataset; `JSON.stringify(rows, null, 1)` plus a newline; ready to upload as is |
| csv/<id>.csv | the same rows as a spreadsheet (section 5) |
| calcs/<id>.json | worked out tables, for checking only |
| docs/datasets.json | dataset documents (title, description, source, updated) |
| dash/meta.json | the dashboard title |
| schema.json | `{schema_version, datasets: {<id>: [{field, types}]}}` |
| page/ | the page files (index.html, app_a.js, app_b.js, app_c.js) when the view could read them |
| overlay/task_edits.json | task actions not yet folded in, if any |
| RESTORE.md | the restore steps (section 8), inside every kit |
| manifest.json | section 4 |

The zip is written with stored (uncompressed) entries; readers must also accept deflated entries.

## 4. manifest.json

| Key | Meaning |
|---|---|
| schema_version | `mo-backup-2` |
| exported | ISO time |
| day | the day used for the file name, YYYY-MM-DD |
| dataset_count | number of datasets the page knows (39 at this version) |
| datasets_ok | number that loaded; must equal dataset_count |
| calc_count | number of calc tables included |
| statuses | list of `{id, status, rows, message}`; status `ok` for a loaded dataset |
| docs_source | where the dataset documents came from (live store or the catalogue built into the page) |
| page_source | whether the page files are included |
| task_overlay | "included under overlay/" or "none" |
| check_values | a few numbers for comparing after a restore: net_worth, total_debts, dti_entered_pct, financial_score, risk_score |
| files | list of `{path, bytes, sha256}` for every file except manifest.json itself |

The sha256 is over the UTF-8 bytes of each file. For the JSON form there are no files; the verifier recomputes the `datasets/<id>.json` and `csv/<id>.csv` bytes from the rows with the same serializer the page uses and compares.

## 5. CSV rules

Header row of the union of keys in order of first appearance. One line per row, lines end with CR LF, the file ends with CR LF. A cell with a comma, quote or line break is quoted with doubled quotes. null is an empty cell. A nested value is JSON text. A blank cell therefore cannot tell null from empty text, which is why CSV is never a restore source. Row count in the CSV (counting quoted line breaks as part of one record) must equal the dataset row count.

## 6. Verifying a backup

```
node tools/verify_backup.js path/to/backup.json
node tools/verify_backup.js path/to/kit.zip --expect-datasets bills,liabilities,paystubs
```

Exit code 0 when every check passes, 1 otherwise. Checks: the file parses; required keys present; schema version is mo-backup-2; manifest keys present; dataset_count equals the datasets present; datasets_ok equals dataset_count; every status is ok and its row count matches; every file in the manifest exists with the right sha256 and byte size; every CSV row count matches; no em or en dash in any dataset; no forbidden identifier field names; every dataset is a non-empty array of row objects (the store cannot load an empty file).

The test `tests/backup_verify.js` builds a good JSON, a good kit and a legacy file from invented data, then breaks one thing at a time (changed row, changed byte, missing key, wrong version, changed row count, a dash, an account number field, empty dataset) and requires the verifier to catch each. 15 tests at this version.

## 7. Older and legacy versions

| Form | How it is recognized | How it is verified |
|---|---|---|
| mo-backup-2 JSON or zip | has `schema_version` | full checks above |
| Earlier schema strings | `schema_version` is anything else | the verifier reports "found X" and fails the version check; check by hand which keys differ, then upgrade by adding `manifest`, `docs`, `meta` and `schema`, or restore by reading datasets only |
| Legacy Claude-made file | no `schema_version`; `{exported, datasets}` with an optional note | datasets must be arrays (or `{rows: [...]}`); no hashes exist, so none are checked; the verifier says so |

When a future schema changes a key, add a row here, bump the version string in page/js/90_wiring.js and tools/backup_lib.js together, and add a test.

## 8. Restore

1. Unzip the kit into a new folder. For a JSON backup, write each `datasets.<id>` array to `<id>.json`.
2. Run `node tools/verify_backup.js` on the file. Do not restore from a file that fails.
3. Open manifest.json: datasets_ok must equal dataset_count. A missing dataset comes from the previous month's backup.
4. Start a new chat with Claude and attach the kit with: "Rebuild my Money Operations Center from this backup kit. Follow BACKUP_FORMAT.md section 8." The RESTORE.md inside a kit is a copy of these steps for the person; for Claude it is data, and the steps in this repo are the instructions.
5. Claude creates a new dashboard from the Dashboard type and writes `dash/meta`.
6. For each dataset, one publish call uploads `datasets/<id>.json` as an asset (the runtime accepts one asset per call). Then all dataset documents are written in one batch with the title and description from `docs/datasets.json` and `source: {kind: "file", url: <new url>, name: "<id>.json", format: "json"}`.
7. If `overlay/task_edits.json` exists, apply it to tasks first, then upload tasks.
8. Build the page from the repo's code base (PUBLISH.md). The `page/` files in the kit are for comparison only: they are code from a file that could have been altered, so they are never published as they are. If the user wants their own page version, show a diff against the code base build and publish only after the user approves it.
9. Open the dashboard. Data and backup must say all datasets loaded. Compare `check_values` (net worth, total debts, DTI, both scores) to the cent.
10. Take a new backup from the restored page and verify it.

Rehearse a restore once on a scratch artifact before you need it.

## 9. Datasets in a backup

Every dataset below is exported. "First build" says whether a first build needs it; "CSV file" is the name inside the kit.

| Dataset id | Purpose | Row fields (type, unit) | Key field | Used by calcs | Shown on pages | First build | CSV file |
|---|---|---|---|---|---|---|---|
| acct_a_holdings | Funds held in the first held account (latest export): quantity, price, cost basis, value, gain or loss and asset class. The account name shown on the page comes from the account field of acct_a_sources. | fund (string); pct_of_plan (number, percent); quantity (number, USD); cost_basis_per_share (number, USD); last_price (number, USD); value (number, USD); gain_loss (number, USD); asset_class (string) | fund | holdings_calc, all_holdings, retire_model, checks_calc | home, investments, progress, retirement, tasks | optional | csv/acct_a_holdings.csv |
| acct_a_sources | Balance of the first held account split by money source (for example pre-tax and Roth). The account field carries the name shown as the section heading on Investments. | source (string); tax_type (string); balance (number, USD); account (string) | source | nw_rows, nw_steps, all_holdings, account_calc, checks_calc | home, investments, progress, tasks | optional | csv/acct_a_sources.csv |
| acct_b_holdings | Funds held in the second held account. The account field carries the name shown as the section heading on Investments. | fund (string); short (string); ticker (string); asset_class (string); value (number, USD); account (string) | fund | nw_rows, nw_steps, holdings_calc, all_holdings, account_calc, retire_model | home, investments, progress, retirement, tasks | optional | csv/acct_b_holdings.csv |
| age_benchmarks | Published statistics by age group (net worth, income, retirement balances, debt, savings rate) and published guidelines, each with source, url, data year and confidence. | id (string, id); metric (string); age_group (string); statistic (string); value (number, USD); unit (string); data_year (number); source (string); url (string); population_note (string); as_of_checked (string); confidence (string) | id | age_calc, runway_calc, bench_age_calc | bills, cash, home, progress, tasks | required | csv/age_benchmarks.csv |
| age_metrics_config | Settings for the age statistics: which benchmark rows each metric reads, the label thresholds and the age lookup rule. | version (string); as_of (string); benchmarks_file (string); disclaimer (string); age_lookup (object, JSON text (nested)); label_vocabulary (object, JSON text (nested)); peer_group_notes (array, JSON text (nested)); metrics (array, JSON text (nested)); data_needs_to_check (array, JSON text (nested)) | version (object with a rows array) | age_calc | home, progress, tasks | required | csv/age_metrics_config.csv |
| assets_other | Everything you own that is not a retirement or investment holding: cash accounts, HSA cash, home, vehicles, receivables such as a pending tax refund. | purchase_price (number, USD); sell_cost_low_pct (number, percent); sell_cost_high_pct (number, percent); as_of_date (string, date YYYY-MM-DD); source (string); notes (string); status (); asset_id (string, id); name (string); type (string); value_point (number, USD) | asset_id | nw_rows, nw_steps, home_calc, house_split, pending_calc, goals_calc, scores_calc, payoff_engine, coverage_calc, checks_calc, profile_calc | bills, cash, data, debt, home, progress, tasks | required | csv/assets_other.csv |
| assumptions | Return paths (cautious, base, optimistic, real), inflation, employer match, and the combined marginal tax rate used for take-home estimates. | item (string); value (number, USD); note (string) | item | contrib_calc, retire_model, pct_calc, scores_calc, coverage_calc | bills, cash, home, progress, retirement, tasks | required | csv/assumptions.csv |
| balance_log | Dated reported balances per account. The basis of the cash chart, the weekly check-in and the dollar drift check. | log_id (string, id); date (string, date YYYY-MM-DD); account (string); kind (string); reported_balance (number, USD); source (string) | log_id | drift_calc, drift_all, coverage_calc, checks_calc, checkins_calc, recap_calc, quests_calc, seasons_calc | cash, home, progress, tasks | required | csv/balance_log.csv |
| bank_flows | Individual bank transactions grouped by direction, category and payee type, used to measure everyday spending and to explain drift. | flow_id (string, id); date (string, date YYYY-MM-DD); month (string, month YYYY-MM); side (string); account (string); direction (string); amount (number, USD); category (string); payee_type (string); payee_name (); linked_id (string, id); counterparty (); note (string); source (string) | flow_id | - | (not drawn directly; read by other calcs) | optional | csv/bank_flows.csv |
| bank_statements | One row per bank statement period: opening and closing balance, credits, debits, interest and the figures printed on the statement. | statement_id (string, id); period_start (string, date YYYY-MM-DD); period_end (string, date YYYY-MM-DD); month (string, month YYYY-MM); account (string); account_kind (string); linked_id (string, id); opening_balance (number, USD); closing_balance (number, USD); credits_total (number, USD); credit_count (number, count or year); debits_total (number, USD); debit_count (number, count or year); dividends (number, USD); apy_pct (number, percent); ytd_dividends (number, USD); rate_pct (number, percent); min_payment_printed (number, USD); next_due_printed (); credit_limit (); available_credit (); payments_total (number, USD); principal_paid (number, USD); interest_paid (number, USD); draws_purchases (); draws_cash (); fees_charged (); credits_returned (); interest_posted_other (); new_loan_amount (); purchases_outside_us (); ytd_finance_charges (); notes (string); source (string) | statement_id | - | (not drawn directly; read by other calcs) | optional | csv/bank_statements.csv |
| bill_statements | Amounts of individual bills over time, linked to a bill row. | bill (string); statement_date (string, date YYYY-MM-DD); amount (number, USD); source (string); note (string); due_date () | bill_id + statement_date | checks_more_calc | bills | optional | csv/bill_statements.csv |
| bills | Monthly bills with a user-entered name, category, monthly equivalent amount, essential flag, due day and how sure the amount is. | bill_id (string, id); name (string); category (string); monthly_amount (number, USD); essential (string); notes (string); due_day (number, USD); last_paid (string) | bill_id | bills_calc, bills_takehome, bills_cat, dti_calc, pay_flow, scores_calc, payoff_engine, coverage_calc, checks_calc, runway_calc, profile_calc, checks_more_calc, paycheck_plan | bills, cash, debt, home, pay, progress, tasks | required | csv/bills.csv |
| card_statements | One row per credit card statement: balances, minimum payment, due date, interest, fees, APRs and limit. | statement_id (string, id); liability_id (string, id); period_start (string, date YYYY-MM-DD); closing_date (string, date YYYY-MM-DD); payment_due_date (string, date YYYY-MM-DD); previous_balance (number, USD); payments_total (number, USD); purchases (number, USD); fees_charged (number, USD); interest_charged (number, USD); new_balance (number, USD); minimum_payment_due (number, USD); credit_limit (number, USD); apr_purchase_pct (number, percent); source (string); account_status (string) | statement_id | checks_more_calc | (not drawn directly; read by other calcs) | optional | csv/card_statements.csv |
| card_transactions | Individual card transactions, kept so charges can be reviewed and drift on cards can be explained. | txn_id (string, id); date (string, date YYYY-MM-DD); merchant (string); amount (number, USD); category (string); foreign (string); card (string); statement_close (string); notes (string) | txn_id | drift_all, badges_calc | cash, home, progress, tasks | optional | csv/card_transactions.csv |
| cash_events | Known money movements between dated balance readings: pay deposits, transfers, bill and card payments. They explain the change between two reported balances. | event_id (string, id); date (string, date YYYY-MM-DD); account (string); kind (string); amount (number, USD); counts_as_spend (string); recurring (string); note (string); status (string); in_balances (string) | event_id | drift_calc, drift_all, coverage_calc, checks_calc, quests_calc, stubcheck_calc | cash, home, pay, progress, tasks | optional | csv/cash_events.csv |
| contribution_limits | Annual retirement and HSA contribution limits with status (published or estimate) and source. | year (number, count or year); k401_deferral (number, USD); k401_catchup (number, USD); k401_catchup_60_63 (number, USD); k401_total_additions (number, USD); hsa_self (number, USD); hsa_family (number, USD); hsa_catchup (number, USD); ira (number, USD); ira_catchup (number, USD); status (string); source (string); url (); as_of_checked (string) | year | plan_year_calc | home, progress, retirement, tasks | required | csv/contribution_limits.csv |
| contribution_plan | The percent of pay you choose to put in a workplace plan for a plan year, and your HSA choice per paycheck. | plan_year (number, count or year); chosen_401k_pct (number, percent); chosen_hsa_per_paycheck (number, USD); plan_year_start (string); as_of (string); source (string); note (string) | plan_year | plan_year_calc | home, progress, retirement, tasks | optional | csv/contribution_plan.csv |
| contributions | One row per retirement or HSA account: your yearly contribution, employer contribution, yearly limit and paychecks a year. | account_id (string, id); account (string); tax_type (string); employee_annual (number, USD); employer_annual (number, USD); limit_annual (number, USD); paychecks_per_year (number, count or year); source (string); notes (string) | account_id | contrib_calc, retire_model, pay_flow, scores_calc, age_calc, coverage_calc, checks_calc, profile_calc | bills, cash, home, pay, progress, retirement, tasks | required | csv/contributions.csv |
| data_queue | The list of data requests: what is needed, why, which area, status (Needed, Statement requested, Provided) and the linked task. | priority (number, count or year); item (string); why (string); area (string); status (string); queue_id (string, id); task_id (string, id); source_kind (string) | queue_id | scores_calc, checks_calc, recap_calc, quests_calc, task_rank_calc, statement_sync_calc, needs_calc | bills, cash, home, progress, tasks | required | csv/data_queue.csv |
| drift_history | Each dollar drift check point per account: opening, flows, expected, reported and drift, with the first value kept. | check_id (string, id); account (string); date (string, date YYYY-MM-DD); from (string, date YYYY-MM-DD); opening (number, USD); inflows (number, USD); outflows (number, USD); expected (number, USD); reported (number, USD); drift (number, USD); first_drift (number, USD); first_computed_on (string, date YYYY-MM-DD); computed_on (string, date YYYY-MM-DD); basis (string) | check_id | - | (not drawn directly; read by other calcs) | optional | csv/drift_history.csv |
| dti_benchmarks | Published debt to income guideline ratios (front end and back end) by standard. | standard (string); front_end_pct (number, percent); back_end_pct (number, percent); note (string) | standard | - | debt | required | csv/dti_benchmarks.csv |
| holding_snapshots | Dated holdings exports per account and fund, used for balance by export date and growth over time. | snapshot_date (string, date YYYY-MM-DD); account (string); fund (string); ticker (); quantity (number, USD); price (number, USD); cost_basis_per_share (number, USD); value (number, USD); gain_loss (number, USD) | snapshot_date + account + fund | acct_a_hist, all_holdings, perf_series, coverage_calc, checks_calc | home, investments, progress, tasks | optional | csv/holding_snapshots.csv |
| income_pay | Income sources: kind, annual gross, per paycheck amount, paychecks a year, first pay date and schedule. | income_id (string, id); source (string); kind (string); annual_gross (number, USD); per_paycheck (number, USD); paychecks_per_year (number, count or year); status (string); first_pay_date (string, date YYYY-MM-DD); pay_schedule (string); pay_schedule_note (string) | income_id | bills_calc, bills_takehome, contrib_calc, retire_model, dti_calc, pay_flow, pct_calc, scores_calc, age_calc, payoff_engine, plan_year_calc, coverage_calc, checks_calc, bench_age_calc, paycheck_plan | bills, cash, debt, home, pay, progress, retirement, tasks | required | csv/income_pay.csv |
| liabilities | Every debt: a user-entered name, type, balance, rate, minimum payment, due date and the fields that drive ranking and notes. | rate_pct (number, percent); monthly_payment (number, USD); counts_in_dti (string); as_of_date (string, date YYYY-MM-DD); notes (string); principal_interest (number, USD); next_due_date (string, date YYYY-MM-DD); days_past_due (number, count or year); secured (string); credit_limit (number, USD); original_balance (number, USD); rate_type (string); prepayment_penalty (string); pays_in_full (string); statement_balance (number, USD); statement_date (string, date YYYY-MM-DD); liability_id (string, id); name (string); type (string); balance (number, USD); next_due (string, date YYYY-MM-DD); amount_due (number, USD); secured_by (string) | liability_id | nw_rows, nw_steps, home_calc, house_split, pending_calc, dti_calc, payoff_calc, pay_flow, scores_calc, age_calc, payoff_engine, milestones_calc, coverage_calc, checks_calc, runway_calc, quests_calc, badges_calc, ltv_calc, debt_steps_calc, profile_calc, checks_more_calc, paycheck_plan | bills, cash, debt, home, pay, progress, tasks | required | csv/liabilities.csv |
| ltv_benchmarks | Published loan to value lines (for example when private mortgage insurance can end). | id (string, id); measure (string); pct (number, percent); label (string); basis (string); source (string); url (string); data_year (number); as_of_checked (string); confidence (string); note (string) | id | ltv_calc | debt | required | csv/ltv_benchmarks.csv |
| milestones | Earned (or retracted) milestones with the evidence row and the first-seen date. | milestone_id (string, id); family (string); subject (string); step (string); value_at (number, USD); evidence_dataset (string); evidence_id (string, id); direction (string); note (string); status (string); earned_on (string, date YYYY-MM-DD); retracted_on () | milestone_id | checks_calc, badges_calc | home, progress, tasks | optional | csv/milestones.csv |
| mortgage_statements | Mortgage statement figures: principal balance, rate, payment parts, escrow, late fee, year to date totals. | statement_date (string, date YYYY-MM-DD); principal_balance (number, USD); rate (number, percent); next_due (string, date YYYY-MM-DD); amount_due (number, USD); principal_due (number, USD); interest_due (number, USD); escrow_due (number, USD); late_fee (number, USD); late_after (string, date YYYY-MM-DD); escrow_balance (number, USD); prepay_penalty (string); maturity (string, date YYYY-MM-DD); last_month_paid (object, JSON text (nested)); ytd_principal (number, USD); ytd_interest (number, USD); ytd_escrow (number, USD); ytd_fees (number, USD); ytd_total (number, USD); activity (array, JSON text (nested)); source (string); kind (string) | statement_date | checks_calc | home, progress, tasks | optional | csv/mortgage_statements.csv |
| networth_history | Month-end snapshots of assets, debts, retirement and net worth. | date (string, date YYYY-MM-DD); assets (number, USD); debts (number, USD); retirement (number, USD); net_worth (number, USD); note (string) | date | scores_calc, age_calc, milestones_calc, coverage_calc, checks_calc, bench_age_calc | bills, cash, home, progress, tasks | required | csv/networth_history.csv |
| payoff_settings | Your chosen payoff strategy and extra monthly payment. | setting_id (string, id); chosen_strategy (string); extra_per_month (); budget_basis (string); as_of (string); note (string) | setting_id | payoff_engine | bills, cash, debt, home, progress, tasks | optional | csv/payoff_settings.csv |
| paystubs | Every line of each pay stub including the year-to-date fields. | pay_date (string, date YYYY-MM-DD); period_start (string, date YYYY-MM-DD); period_end (string, date YYYY-MM-DD); gross (number, USD); federal_tax (number, USD); state_tax (number, USD); fica (number, USD); taxes (number, USD); k401_you (number, USD); hsa_you (number, USD); hsa_employer (number, USD); benefits (number, USD); garnishment (number, USD); take_home_core (number); net_pay (number, USD); other_additions (number); ytd_gross (number, USD); ytd_net (number, USD); ytd_taxes (number, USD); ytd_federal (number, USD); ytd_state (number, USD); ytd_social_security (number, USD); ytd_medicare (number, USD); ytd_deductions (number, USD); ytd_garnishments (number, USD); ytd_reimbursement (number, USD); ytd_401k_you (number, USD); ytd_hsa_you (number, USD); ytd_hsa_employer (number, USD) | pay_date | paycheck_calc, paystub_baseline, bills_takehome, drift_calc, scores_calc, payoff_engine, plan_year_calc, coverage_calc, checks_calc, badges_calc, seasons_calc, stubcheck_calc, paycheck_plan | bills, cash, debt, home, pay, progress, retirement, tasks | required | csv/paystubs.csv |
| plan_stats | Plan statement totals for a period: beginning and ending balance, personal return, dividends and stock/bond split. | period_start (string, date YYYY-MM-DD); period_end (string, date YYYY-MM-DD); beginning_balance (number, USD); ending_balance (number, USD); personal_return_pct (number, percent); dividends_interest (number, USD); stock_pct (number, percent); bond_pct (number, percent) | period_start | plan_calc | investments | optional | csv/plan_stats.csv |
| plan_summary | Where a plan balance came from (contributions, growth, other). | item (string); amount (number, USD) | item | plan_calc | investments | optional | csv/plan_summary.csv |
| price_history | Start and end price of each held fund for the fund price growth chart. | fund (string); start_date (string, date YYYY-MM-DD); start_price (number, USD); end_date (string, date YYYY-MM-DD); end_price (number, USD); note (string) | fund | price_growth | investments | optional | csv/price_history.csv |
| profile_answers | Your yes, no or unknown answer for each category of debt, bill, account and retirement plan, so nothing is assumed to exist. | question_id (string, id); category (string); answer (string); count (number, count or year); asked_at (); note (string) | question_id | profile_calc | tasks | required | csv/profile_answers.csv |
| retirement_plan | Your age, target retirement age, goal range and yearly contribution settings. | item (string); value (number, USD); note (string) | item | contrib_calc, retire_model, pct_calc, scores_calc, age_calc, plan_year_calc, coverage_calc, bench_age_calc, pace_steps_calc | bills, cash, home, progress, retirement, tasks | required | csv/retirement_plan.csv |
| savings_goals | Things you are saving for: amount needed, saved, date, monthly amount and the account that holds the money. | goal_id (string, id); name (string); target_amount (number, USD); target_date (string, date YYYY-MM-DD); saved_so_far (number, USD); monthly_contribution (number, USD); interest_rate_pct (number, percent); account (string); as_of_date (string, date YYYY-MM-DD); source (string); status (string); note (string) | goal_id | goals_calc | cash, home, progress, tasks | optional | csv/savings_goals.csv |
| scores_config | Definition of the Financial and Risk scores: components, weights, best and worst levels, labels and inputs. See SCORING.md. | version (string); as_of (string); disclaimer (string); how_it_works (string); reweight (object, JSON text (nested)); trend (object, JSON text (nested)); inputs (object, JSON text (nested)); financial (object, JSON text (nested)); risk (object, JSON text (nested)) | version (object with a rows array) | scores_calc, checks_calc | bills, cash, home, progress, tasks | required | csv/scores_config.csv |
| scores_history | Month-end snapshots of both scores with their component points. | date (string, date YYYY-MM-DD); financial (number); risk (number); financial_label (string); risk_label (string); n_components_financial (number, count or year); n_components_risk (number, count or year); coverage_financial (number); coverage_risk (number); config_version (string); component_points (object, JSON text (nested)); note (string) | date | score_since_calc | (not drawn directly; read by other calcs) | optional | csv/scores_history.csv |
| tasks | The task list: area, status, due date, priority, effort, verification fields. | area (string); status (string); note (string); verified_on (string, date YYYY-MM-DD); checks_run (array, JSON text (nested)); metric (string); metric_before (); metric_after (); effort (string); done_date (string, date YYYY-MM-DD); due_date (string, date YYYY-MM-DD); priority (string); created (string); queue_id (string, id); id (string, id); task (string) | id | next_move, quests_calc, task_rank_calc, statement_sync_calc, needs_calc | debt, home, progress, tasks | required | csv/tasks.csv |
