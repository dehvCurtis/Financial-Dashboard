# DATA_MODEL: every dataset

39 datasets. Each is one JSON file: a non-empty array of flat row objects (or `{ "rows": [...] }`). A key missing from a row arrives as null on the page. Nested cells are limited to an allow-list (config datasets); everywhere else rows are flat scalars. Rules and the reasoning are in ARCHITECTURE.md sections 3 to 6.

## Classes

| Class | Meaning | Example |
|---|---|---|
| user | the user's own figures, from statements or answers | bills, liabilities, paystubs |
| history | dated rows that accumulate | balance_log, scores_history, networth_history, drift_history, price_history |
| reference | published statistics, with source | age_benchmarks, dti_benchmarks |
| config | settings with defaults | scores_config, assumptions, payoff_settings |
| derived | stored results that Claude refreshes | plan_summary, plan_stats |

## Common rules

- Money is a number in dollars; percent is a number like 4.5 (not 0.045); dates are YYYY-MM-DD; months YYYY-MM.
- Money rows carry `source` and `as_of_date` where the dataset has those fields.
- A correction replaces the old value in place (history datasets keep dated rows).
- Ids are stable and never reused (L1, B3, T7 ...).
- No account numbers, loan ids, SSNs, tax ids or addresses. Use the user's own labels.
- A dataset with nothing real yet ships one starter row with every value null (templates/). Calcs strip it (`realRows`).
- Dataset document title: 30 characters at most. Each document at most 256 KB.
- The Unit column below is a heuristic from the field name; the calc text and DATA_MODEL notes are the truth when they differ.

## Index

| Dataset id | Class | Key field | Needed for a first build | Rows in the demo | Doc title (max 30) | Read by (calcs) |
|---|---|---|---|---|---|---|
| acct_a_holdings | user | fund | optional | 2 | First 401(k) holdings | holdings_calc, all_holdings, retire_model, checks_calc |
| acct_a_sources | user | source | optional | 2 | First 401(k) money sources | nw_rows, nw_steps, all_holdings, account_calc, checks_calc |
| acct_b_holdings | user | fund | optional | 2 | Second plan holdings | nw_rows, nw_steps, holdings_calc, all_holdings, account_calc, retire_model |
| age_benchmarks | reference | id | yes | 320 | Age benchmarks | age_calc, runway_calc, bench_age_calc |
| age_metrics_config | config | version (object with a rows array) | yes | 1 | Age metrics settings | age_calc |
| assets_other | user | asset_id | yes | 6 | Other assets | nw_rows, nw_steps, home_calc, house_split, pending_calc, goals_calc, scores_calc, payoff_engine, coverage_calc, checks_calc, profile_calc |
| assumptions | config | item | yes | 6 | Return assumptions | contrib_calc, retire_model, pct_calc, scores_calc, coverage_calc |
| balance_log | history | log_id | yes | 6 | Balance log | drift_calc, drift_all, coverage_calc, checks_calc, checkins_calc, recap_calc, quests_calc, seasons_calc |
| bank_flows | user | flow_id | optional | 2 | Bank flows | (drawn directly) |
| bank_statements | user | statement_id | optional | 2 | Bank statements | (drawn directly) |
| bill_statements | user | bill_id + statement_date | optional | 5 | Bill statements | checks_more_calc |
| bills | user | bill_id | yes | 6 | Monthly bills | bills_calc, bills_takehome, bills_cat, dti_calc, pay_flow, scores_calc, payoff_engine, coverage_calc, checks_calc, runway_calc, profile_calc, checks_more_calc, paycheck_plan |
| card_statements | user | statement_id | optional | 1 | Card statements | checks_more_calc |
| card_transactions | user | txn_id | optional | 2 | Card transactions | drift_all, badges_calc |
| cash_events | user | event_id | optional | 2 | Cash events | drift_calc, drift_all, coverage_calc, checks_calc, quests_calc, stubcheck_calc |
| contribution_limits | reference | year | yes | 1 | Contribution limits | plan_year_calc |
| contribution_plan | user | plan_year | optional | 2 | Contribution plan | plan_year_calc |
| contributions | user | account_id | yes | 3 | Retirement contributions | contrib_calc, retire_model, pay_flow, scores_calc, age_calc, coverage_calc, checks_calc, profile_calc |
| data_queue | queue | queue_id | yes | 3 | Data still to add | scores_calc, checks_calc, recap_calc, quests_calc, task_rank_calc, statement_sync_calc, needs_calc |
| drift_history | history | check_id | optional | 1 | Dollar drift history | (drawn directly) |
| dti_benchmarks | reference | standard | yes | 5 | DTI benchmarks | (drawn directly) |
| holding_snapshots | user | snapshot_date + account + fund | optional | 4 | Holding snapshots | acct_a_hist, all_holdings, perf_series, coverage_calc, checks_calc |
| income_pay | user | income_id | yes | 2 | Income and pay | bills_calc, bills_takehome, contrib_calc, retire_model, dti_calc, pay_flow, pct_calc, scores_calc, age_calc, payoff_engine, plan_year_calc, coverage_calc, checks_calc, bench_age_calc, paycheck_plan |
| liabilities | user | liability_id | yes | 8 | Debts | nw_rows, nw_steps, home_calc, house_split, pending_calc, dti_calc, payoff_calc, pay_flow, scores_calc, age_calc, payoff_engine, milestones_calc, coverage_calc, checks_calc, runway_calc, quests_calc, badges_calc, ltv_calc, debt_steps_calc, profile_calc, checks_more_calc, paycheck_plan |
| ltv_benchmarks | reference | id | yes | 3 | Loan to value lines | ltv_calc |
| milestones | history | milestone_id | optional | 1 | Milestones | checks_calc, badges_calc |
| mortgage_statements | user | statement_date | optional | 1 | Mortgage statements | checks_calc |
| networth_history | history | date | yes | 2 | Net worth history | scores_calc, age_calc, milestones_calc, coverage_calc, checks_calc, bench_age_calc |
| payoff_settings | user | setting_id | optional | 1 | Payoff settings | payoff_engine |
| paystubs | user | pay_date | yes | 4 | Pay stubs | paycheck_calc, paystub_baseline, bills_takehome, drift_calc, scores_calc, payoff_engine, plan_year_calc, coverage_calc, checks_calc, badges_calc, seasons_calc, stubcheck_calc, paycheck_plan |
| plan_stats | user | period_start | optional | 1 | Plan statement totals | plan_calc |
| plan_summary | user | item | optional | 2 | Plan balance sources | plan_calc |
| price_history | user | fund | optional | 1 | Fund price history | price_growth |
| profile_answers | user | question_id | yes | 29 | What you have | profile_calc |
| retirement_plan | user | item | yes | 8 | Retirement plan inputs | contrib_calc, retire_model, pct_calc, scores_calc, age_calc, plan_year_calc, coverage_calc, bench_age_calc, pace_steps_calc |
| savings_goals | user | goal_id | optional | 1 | Savings goals | goals_calc |
| scores_config | config | version (object with a rows array) | yes | 1 | Score settings | scores_calc, checks_calc |
| scores_history | history | date | optional | 1 | Score history | score_since_calc |
| tasks | queue | id | yes | 4 | Tasks | next_move, quests_calc, task_rank_calc, statement_sync_calc, needs_calc |

## Datasets in detail

### acct_a_holdings

Funds held in the first held account (latest export): quantity, price, cost basis, value, gain or loss and asset class. The account name shown on the page comes from the account field of acct_a_sources.

- Class: user. Key field: fund. File shape: array. Optional: add it when the situation applies.
- Doc title: "First 401(k) holdings" (21 characters). Description: First 401(k) fund shares, prices and values from the latest export.
- Intake: 401(k) or brokerage statement. CSV view: acct_a_holdings.csv. Starter template: templates/acct_a_holdings.json
- Read by calcs: holdings_calc, all_holdings, retire_model, checks_calc
- Shown on pages: home, investments, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| fund | string | text |
| pct_of_plan | number | percent |
| quantity | number | USD |
| cost_basis_per_share | number | USD |
| last_price | number | USD |
| value | number | USD |
| gain_loss | number | USD |
| asset_class | string | text |

### acct_a_sources

Balance of the first held account split by money source (for example pre-tax and Roth). The account field carries the name shown as the section heading on Investments.

- Class: user. Key field: source. File shape: array. Optional: add it when the situation applies.
- Doc title: "First 401(k) money sources" (26 characters). Description: Estimated split by source and tax type, scaled to the latest fund total.
- Intake: 401(k) or brokerage statement. CSV view: acct_a_sources.csv. Starter template: templates/acct_a_sources.json
- Read by calcs: nw_rows, nw_steps, all_holdings, account_calc, checks_calc
- Shown on pages: home, investments, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| source | string | text |
| tax_type | string | text |
| balance | number | USD |
| account | string | text |

### acct_b_holdings

Funds held in the second held account. The account field carries the name shown as the section heading on Investments.

- Class: user. Key field: fund. File shape: array. Optional: add it when the situation applies.
- Doc title: "Second plan holdings" (20 characters). Description: Funds held in the second 401(k) with the value of each.
- Intake: 401(k) or brokerage statement. CSV view: acct_b_holdings.csv. Starter template: templates/acct_b_holdings.json
- Read by calcs: nw_rows, nw_steps, holdings_calc, all_holdings, account_calc, retire_model
- Shown on pages: home, investments, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| fund | string | text |
| short | string | text |
| ticker | string | text |
| asset_class | string | text |
| value | number | USD |
| account | string | text |

### age_benchmarks

Published statistics by age group (net worth, income, retirement balances, debt, savings rate) and published guidelines, each with source, url, data year and confidence.

- Class: reference. Key field: id. File shape: array. Needed for a first build.
- Doc title: "Age benchmarks" (14 characters). Description: National benchmarks by age group with sources, data year and dollar year.
- Intake: Claude researches and cites a published source (annual refresh). CSV view: age_benchmarks.csv. Starter template: templates/age_benchmarks.json
- Read by calcs: age_calc, runway_calc, bench_age_calc
- Shown on pages: bills, cash, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| id | string | id |
| metric | string | text |
| age_group | string | text |
| statistic | string | text |
| value | null or number | USD |
| unit | string | text |
| data_year | number | number |
| source | string | text |
| url | string | text |
| population_note | string | text |
| as_of_checked | string | text |
| confidence | string | text |

### age_metrics_config

Settings for the age statistics: which benchmark rows each metric reads, the label thresholds and the age lookup rule.

- Class: config. Key field: version (object with a rows array). File shape: object with rows. Needed for a first build.
- Doc title: "Age metrics settings" (20 characters). Description: Settings for the age ladder: guideline and typical values shown as percent. One settings row.
- Intake: shipped default, changed only by decision. CSV view: age_metrics_config.csv. Starter template: templates/age_metrics_config.json
- Read by calcs: age_calc
- Shown on pages: home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| version | string | text |
| as_of | string | text |
| benchmarks_file | string | text |
| disclaimer | string | text |
| age_lookup | object | JSON text (nested) |
| label_vocabulary | object | JSON text (nested) |
| peer_group_notes | array | JSON text (nested) |
| metrics | array | JSON text (nested) |
| data_needs_to_check | array | JSON text (nested) |

### assets_other

Everything you own that is not a retirement or investment holding: cash accounts, HSA cash, home, vehicles, receivables such as a pending tax refund.

- Class: user. Key field: asset_id. File shape: array. Needed for a first build.
- Doc title: "Other assets" (12 characters). Description: Truck, house, savings, checking, HSA cash and pending refund, as given or estimated.
- Intake: interview, bank or brokerage statement, your own estimate. CSV view: assets_other.csv. Starter template: templates/assets_other.json
- Read by calcs: nw_rows, nw_steps, home_calc, house_split, pending_calc, goals_calc, scores_calc, payoff_engine, coverage_calc, checks_calc, profile_calc
- Shown on pages: bills, cash, data, debt, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| purchase_price | null or number | USD |
| sell_cost_low_pct | null or number | percent |
| sell_cost_high_pct | null or number | percent |
| as_of_date | string | date YYYY-MM-DD |
| source | string | text |
| notes | string | text |
| status | null |  |
| asset_id | string | id |
| name | string | text |
| type | string | text |
| value_point | number | USD |

### assumptions

Return paths (cautious, base, optimistic, real), inflation, employer match, and the combined marginal tax rate used for take-home estimates.

- Class: config. Key field: item. File shape: array. Needed for a first build.
- Doc title: "Return assumptions" (18 characters). Description: Yearly returns after inflation, inflation, and the employer 401(k) match (0, stated by the owner).
- Intake: Claude proposes, you confirm. CSV view: assumptions.csv. Starter template: templates/assumptions.json
- Read by calcs: contrib_calc, retire_model, pct_calc, scores_calc, coverage_calc
- Shown on pages: bills, cash, home, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| item | string | text |
| value | number | USD |
| note | string | text |

### balance_log

Dated reported balances per account. The basis of the cash chart, the weekly check-in and the dollar drift check.

- Class: history. Key field: log_id. File shape: array. Needed for a first build.
- Doc title: "Balance log" (11 characters). Description: Account balances you report, by date, for the dollar drift check.
- Intake: bank, card or loan statement; app screenshot. CSV view: balance_log.csv. Starter template: templates/balance_log.json
- Read by calcs: drift_calc, drift_all, coverage_calc, checks_calc, checkins_calc, recap_calc, quests_calc, seasons_calc
- Shown on pages: cash, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| log_id | string | id |
| date | string | date YYYY-MM-DD |
| account | string | text |
| kind | string | text |
| reported_balance | number | USD |
| source | string | text |

### bank_flows

Individual bank transactions grouped by direction, category and payee type, used to measure everyday spending and to explain drift.

- Class: user. Key field: flow_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Bank flows" (10 characters). Description: Every credit and debit from the credit union statements, one row per movement, with category and linked debt or account. Extracted values only.
- Intake: bank statement CSV. CSV view: bank_flows.csv. Starter template: templates/bank_flows.json
- Read by calcs: none (drawn directly by the page)
- Shown on pages: (not drawn directly; read by other calcs)

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| flow_id | string | id |
| date | string | date YYYY-MM-DD |
| month | string | month YYYY-MM |
| side | string | text |
| account | string | text |
| direction | string | text |
| amount | number | USD |
| category | string | text |
| payee_type | string | text |
| payee_name | null |  |
| linked_id | null or string | id |
| counterparty | null |  |
| note | string | text |
| source | string | text |

### bank_statements

One row per bank statement period: opening and closing balance, credits, debits, interest and the figures printed on the statement.

- Class: user. Key field: statement_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Bank statements" (15 characters). Description: One row per credit union account statement: balances, totals, dividends, rates and loan or card fields. Extracted values only.
- Intake: bank statement (PDF or CSV). CSV view: bank_statements.csv. Starter template: templates/bank_statements.json
- Read by calcs: none (drawn directly by the page)
- Shown on pages: (not drawn directly; read by other calcs)

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| statement_id | string | id |
| period_start | string | date YYYY-MM-DD |
| period_end | string | date YYYY-MM-DD |
| month | string | month YYYY-MM |
| account | string | text |
| account_kind | string | text |
| linked_id | string | id |
| opening_balance | number | USD |
| closing_balance | number | USD |
| credits_total | number | USD |
| credit_count | number | count or year |
| debits_total | number | USD |
| debit_count | number | count or year |
| dividends | null or number | USD |
| apy_pct | null or number | percent |
| ytd_dividends | null or number | USD |
| rate_pct | null or number | percent |
| min_payment_printed | null or number | USD |
| next_due_printed | null |  |
| credit_limit | null |  |
| available_credit | null |  |
| payments_total | null or number | USD |
| principal_paid | null or number | USD |
| interest_paid | null or number | USD |
| draws_purchases | null |  |
| draws_cash | null |  |
| fees_charged | null |  |
| credits_returned | null |  |
| interest_posted_other | null |  |
| new_loan_amount | null |  |
| purchases_outside_us | null |  |
| ytd_finance_charges | null |  |
| notes | string | text |
| source | string | text |

### bill_statements

Amounts of individual bills over time, linked to a bill row.

- Class: user. Key field: bill_id + statement_date. File shape: array. Optional: add it when the situation applies.
- Doc title: "Bill statements" (15 characters). Description: One row per utility or phone statement, by bill.
- Intake: utility, phone, insurance or other bill. CSV view: bill_statements.csv. Starter template: templates/bill_statements.json
- Read by calcs: checks_more_calc
- Shown on pages: bills

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| bill | string | text |
| statement_date | string | date YYYY-MM-DD |
| amount | number | USD |
| source | string | text |
| note | string | text |
| due_date | null |  |

### bills

Monthly bills with a user-entered name, category, monthly equivalent amount, essential flag, due day and how sure the amount is.

- Class: user. Key field: bill_id. File shape: array. Needed for a first build.
- Doc title: "Monthly bills" (13 characters). Description: Everyday monthly bills you have sent so far.
- Intake: interview, bills and statements. CSV view: bills.csv. Starter template: templates/bills.json
- Read by calcs: bills_calc, bills_takehome, bills_cat, dti_calc, pay_flow, scores_calc, payoff_engine, coverage_calc, checks_calc, runway_calc, profile_calc, checks_more_calc, paycheck_plan
- Shown on pages: bills, cash, debt, home, pay, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| bill_id | string | id |
| name | string | text |
| category | string | text |
| monthly_amount | number | USD |
| essential | string | text |
| notes | string | text |
| due_day | number | USD |
| last_paid | string | text |

### card_statements

One row per credit card statement: balances, minimum payment, due date, interest, fees, APRs and limit.

- Class: user. Key field: statement_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Card statements" (15 characters). Description: One row per card statement: balances, payments, interest, fees, minimum, due date and APR, tied to its card. Extracted values only.
- Intake: credit card statement. CSV view: card_statements.csv. Starter template: templates/card_statements.json
- Read by calcs: checks_more_calc
- Shown on pages: (not drawn directly; read by other calcs)

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| statement_id | string | id |
| liability_id | string | id |
| period_start | string | date YYYY-MM-DD |
| closing_date | string | date YYYY-MM-DD |
| payment_due_date | string | date YYYY-MM-DD |
| previous_balance | number | USD |
| payments_total | number | USD |
| purchases | number | USD |
| fees_charged | number | USD |
| interest_charged | number | USD |
| new_balance | number | USD |
| minimum_payment_due | number | USD |
| credit_limit | number | USD |
| apr_purchase_pct | number | percent |
| source | string | text |
| account_status | string | text |

### card_transactions

Individual card transactions, kept so charges can be reviewed and drift on cards can be explained.

- Class: user. Key field: txn_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Card transactions" (17 characters). Description: Card charges and credits from statements, with categories, for spending categories.
- Intake: credit card statement. CSV view: card_transactions.csv. Starter template: templates/card_transactions.json
- Read by calcs: drift_all, badges_calc
- Shown on pages: cash, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| txn_id | string | id |
| date | string | date YYYY-MM-DD |
| merchant | string | text |
| amount | number | USD |
| category | string | text |
| foreign | string | text |
| card | string | text |
| statement_close | string | text |
| notes | string | text |

### cash_events

Known money movements between dated balance readings: pay deposits, transfers, bill and card payments. They explain the change between two reported balances.

- Class: user. Key field: event_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Cash events" (11 characters). Description: Known money movements between balance reports, used for dollar drift.
- Intake: bank statement, pay stub, your own entry. CSV view: cash_events.csv. Starter template: templates/cash_events.json
- Read by calcs: drift_calc, drift_all, coverage_calc, checks_calc, quests_calc, stubcheck_calc
- Shown on pages: cash, home, pay, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| event_id | string | id |
| date | string | date YYYY-MM-DD |
| account | string | text |
| kind | string | text |
| amount | number | USD |
| counts_as_spend | string | text |
| recurring | string | text |
| note | string | text |
| status | string | text |
| in_balances | string | text |

### contribution_limits

Annual retirement and HSA contribution limits with status (published or estimate) and source.

- Class: reference. Key field: year. File shape: array. Needed for a first build.
- Doc title: "Contribution limits" (19 characters). Description: Tax-law contribution limits by plan year.
- Intake: Claude researches the published limits each year. CSV view: contribution_limits.csv. Starter template: templates/contribution_limits.json
- Read by calcs: plan_year_calc
- Shown on pages: home, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| year | number | count or year |
| k401_deferral | number | USD |
| k401_catchup | number | USD |
| k401_catchup_60_63 | number | USD |
| k401_total_additions | number | USD |
| hsa_self | number | USD |
| hsa_family | number | USD |
| hsa_catchup | number | USD |
| ira | number | USD |
| ira_catchup | number | USD |
| status | string | text |
| source | string | text |
| url | null |  |
| as_of_checked | string | text |

### contribution_plan

The percent of pay you choose to put in a workplace plan for a plan year, and your HSA choice per paycheck.

- Class: user. Key field: plan_year. File shape: array. Optional: add it when the situation applies.
- Doc title: "Contribution plan" (17 characters). Description: Chosen 401(k) percent per plan year.
- Intake: your choice on the 401(k) card. CSV view: contribution_plan.csv. Starter template: templates/contribution_plan.json
- Read by calcs: plan_year_calc
- Shown on pages: home, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| plan_year | number | count or year |
| chosen_401k_pct | null or number | percent |
| chosen_hsa_per_paycheck | null or number | USD |
| plan_year_start | string | text |
| as_of | string | text |
| source | string | text |
| note | string | text |

### contributions

One row per retirement or HSA account: your yearly contribution, employer contribution, yearly limit and paychecks a year.

- Class: user. Key field: account_id. File shape: array. Needed for a first build.
- Doc title: "Retirement contributions" (24 characters). Description: Yearly 401(k), HSA and Roth contributions from pay stubs against limits.
- Intake: pay stub, plan documents. CSV view: contributions.csv. Starter template: templates/contributions.json
- Read by calcs: contrib_calc, retire_model, pay_flow, scores_calc, age_calc, coverage_calc, checks_calc, profile_calc
- Shown on pages: bills, cash, home, pay, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| account_id | string | id |
| account | string | text |
| tax_type | string | text |
| employee_annual | number | USD |
| employer_annual | number | USD |
| limit_annual | number | USD |
| paychecks_per_year | number | count or year |
| source | string | text |
| notes | string | text |

### data_queue

The list of data requests: what is needed, why, which area, status (Needed, Statement requested, Provided) and the linked task.

- Class: queue. Key field: queue_id. File shape: array. Needed for a first build.
- Doc title: "Data still to add" (17 characters). Description: Items needed from the user, each with an id, how the value arrived and a linked task when a statement is requested.
- Intake: created by Claude whenever a number is missing. CSV view: data_queue.csv. Starter template: templates/data_queue.json
- Read by calcs: scores_calc, checks_calc, recap_calc, quests_calc, task_rank_calc, statement_sync_calc, needs_calc
- Shown on pages: bills, cash, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| priority | number | count or year |
| item | string | text |
| why | string | text |
| area | string | text |
| status | string | text |
| queue_id | string | id |
| task_id | null or string | id |
| source_kind | null or string | text |

### drift_history

Each dollar drift check point per account: opening, flows, expected, reported and drift, with the first value kept.

- Class: history. Key field: check_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Dollar drift history" (20 characters). Description: Check points of reported minus expected balances per account.
- Intake: written by Claude after each drift check. CSV view: drift_history.csv. Starter template: templates/drift_history.json
- Read by calcs: none (drawn directly by the page)
- Shown on pages: (not drawn directly; read by other calcs)

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| check_id | string | id |
| account | string | text |
| date | string | date YYYY-MM-DD |
| from | string | date YYYY-MM-DD |
| opening | number | USD |
| inflows | number | USD |
| outflows | number | USD |
| expected | number | USD |
| reported | number | USD |
| drift | number | USD |
| first_drift | number | USD |
| first_computed_on | string | date YYYY-MM-DD |
| computed_on | string | date YYYY-MM-DD |
| basis | string | text |

### dti_benchmarks

Published debt to income guideline ratios (front end and back end) by standard.

- Class: reference. Key field: standard. File shape: array. Needed for a first build.
- Doc title: "DTI benchmarks" (14 characters). Description: Lender debt-to-income thresholds.
- Intake: Claude researches and cites a published source. CSV view: dti_benchmarks.csv. Starter template: templates/dti_benchmarks.json
- Read by calcs: none (drawn directly by the page)
- Shown on pages: debt

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| standard | string | text |
| front_end_pct | null or number | percent |
| back_end_pct | number | percent |
| note | string | text |

### holding_snapshots

Dated holdings exports per account and fund, used for balance by export date and growth over time.

- Class: user. Key field: snapshot_date + account + fund. File shape: array. Optional: add it when the situation applies.
- Doc title: "Holding snapshots" (17 characters). Description: Dated share counts, prices and values per holding, one block per export.
- Intake: 401(k) or brokerage statement export. CSV view: holding_snapshots.csv. Starter template: templates/holding_snapshots.json
- Read by calcs: acct_a_hist, all_holdings, perf_series, coverage_calc, checks_calc
- Shown on pages: home, investments, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| snapshot_date | string | date YYYY-MM-DD |
| account | string | text |
| fund | string | text |
| ticker | null |  |
| quantity | number | USD |
| price | number | USD |
| cost_basis_per_share | number | USD |
| value | number | USD |
| gain_loss | number | USD |

### income_pay

Income sources: kind, annual gross, per paycheck amount, paychecks a year, first pay date and schedule.

- Class: user. Key field: income_id. File shape: array. Needed for a first build.
- Doc title: "Income and pay" (14 characters). Description: Base and variable pay, verified from pay stubs.
- Intake: pay stub, offer letter. CSV view: income_pay.csv. Starter template: templates/income_pay.json
- Read by calcs: bills_calc, bills_takehome, contrib_calc, retire_model, dti_calc, pay_flow, pct_calc, scores_calc, age_calc, payoff_engine, plan_year_calc, coverage_calc, checks_calc, bench_age_calc, paycheck_plan
- Shown on pages: bills, cash, debt, home, pay, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| income_id | string | id |
| source | string | text |
| kind | string | text |
| annual_gross | number | USD |
| per_paycheck | null or number | USD |
| paychecks_per_year | null or number | count or year |
| status | string | text |
| first_pay_date | null or string | date YYYY-MM-DD |
| pay_schedule | null or string | text |
| pay_schedule_note | null or string | text |

### liabilities

Every debt: a user-entered name, type, balance, rate, minimum payment, due date and the fields that drive ranking and notes.

- Class: user. Key field: liability_id. File shape: array. Needed for a first build.
- Doc title: "Debts" (5 characters). Description: Every debt by category with a user-entered name: balance, rate and payment.
- Intake: loan, mortgage and card statements. CSV view: liabilities.csv. Starter template: templates/liabilities.json
- Read by calcs: nw_rows, nw_steps, home_calc, house_split, pending_calc, dti_calc, payoff_calc, pay_flow, scores_calc, age_calc, payoff_engine, milestones_calc, coverage_calc, checks_calc, runway_calc, quests_calc, badges_calc, ltv_calc, debt_steps_calc, profile_calc, checks_more_calc, paycheck_plan
- Shown on pages: bills, cash, debt, home, pay, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| rate_pct | null or number | percent |
| monthly_payment | null or number | USD |
| counts_in_dti | string | text |
| as_of_date | string | date YYYY-MM-DD |
| notes | string | text |
| principal_interest | null or number | USD |
| next_due_date | null or string | date YYYY-MM-DD |
| days_past_due | null or number | count or year |
| secured | string | text |
| credit_limit | null or number | USD |
| original_balance | null or number | USD |
| rate_type | null or string | text |
| prepayment_penalty | null or string | text |
| pays_in_full | null or string | text |
| statement_balance | null or number | USD |
| statement_date | null or string | date YYYY-MM-DD |
| liability_id | string | id |
| name | string | text |
| type | string | text |
| balance | null or number | USD |
| next_due | string | date YYYY-MM-DD |
| amount_due | number | USD |
| secured_by | string | text |

### ltv_benchmarks

Published loan to value lines (for example when private mortgage insurance can end).

- Class: reference. Key field: id. File shape: array. Needed for a first build.
- Doc title: "Loan to value lines" (19 characters). Description: Sourced reference lines for the loan to value chart.
- Intake: Claude researches and cites a published source. CSV view: ltv_benchmarks.csv. Starter template: templates/ltv_benchmarks.json
- Read by calcs: ltv_calc
- Shown on pages: debt

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| id | string | id |
| measure | string | text |
| pct | number | percent |
| label | string | text |
| basis | string | text |
| source | string | text |
| url | string | text |
| data_year | number | number |
| as_of_checked | string | text |
| confidence | string | text |
| note | string | text |

### milestones

Earned (or retracted) milestones with the evidence row and the first-seen date.

- Class: history. Key field: milestone_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Milestones" (10 characters). Description: Derived milestones and badges with the date first seen.
- Intake: written by Claude when a derived milestone is first seen. CSV view: milestones.csv. Starter template: templates/milestones.json
- Read by calcs: checks_calc, badges_calc
- Shown on pages: home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| milestone_id | string | id |
| family | string | text |
| subject | string | text |
| step | string | text |
| value_at | number | USD |
| evidence_dataset | string | text |
| evidence_id | string | id |
| direction | string | text |
| note | string | text |
| status | string | text |
| earned_on | string | date YYYY-MM-DD |
| retracted_on | null |  |

### mortgage_statements

Mortgage statement figures: principal balance, rate, payment parts, escrow, late fee, year to date totals.

- Class: user. Key field: statement_date. File shape: array. Optional: add it when the situation applies.
- Doc title: "Mortgage statements" (19 characters). Description: Extracted values from servicer statements, one row per statement, plus one row per annual Form 1098.
- Intake: mortgage statement. CSV view: mortgage_statements.csv. Starter template: templates/mortgage_statements.json
- Read by calcs: checks_calc
- Shown on pages: home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| statement_date | string | date YYYY-MM-DD |
| principal_balance | number | USD |
| rate | number | percent |
| next_due | string | date YYYY-MM-DD |
| amount_due | number | USD |
| principal_due | number | USD |
| interest_due | number | USD |
| escrow_due | number | USD |
| late_fee | number | USD |
| late_after | string | date YYYY-MM-DD |
| escrow_balance | number | USD |
| prepay_penalty | string | text |
| maturity | string | date YYYY-MM-DD |
| last_month_paid | object | JSON text (nested) |
| ytd_principal | number | USD |
| ytd_interest | number | USD |
| ytd_escrow | number | USD |
| ytd_fees | number | USD |
| ytd_total | number | USD |
| activity | array | JSON text (nested) |
| source | string | text |
| kind | string | text |

### networth_history

Month-end snapshots of assets, debts, retirement and net worth.

- Class: history. Key field: date. File shape: array. Needed for a first build.
- Doc title: "Net worth history" (17 characters). Description: One row per month: assets, debts, retirement and net worth.
- Intake: month-end snapshot written by Claude. CSV view: networth_history.csv. Starter template: templates/networth_history.json
- Read by calcs: scores_calc, age_calc, milestones_calc, coverage_calc, checks_calc, bench_age_calc
- Shown on pages: bills, cash, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| date | string | date YYYY-MM-DD |
| assets | number | USD |
| debts | number | USD |
| retirement | number | USD |
| net_worth | number | USD |
| note | string | text |

### payoff_settings

Your chosen payoff strategy and extra monthly payment.

- Class: user. Key field: setting_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Payoff settings" (15 characters). Description: Saved choices for the debt payoff plan.
- Intake: your choice on the Debt page. CSV view: payoff_settings.csv. Starter template: templates/payoff_settings.json
- Read by calcs: payoff_engine
- Shown on pages: bills, cash, debt, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| setting_id | string | id |
| chosen_strategy | string | text |
| extra_per_month | null |  |
| budget_basis | string | text |
| as_of | string | text |
| note | string | text |

### paystubs

Every line of each pay stub including the year-to-date fields.

- Class: user. Key field: pay_date. File shape: array. Needed for a first build.
- Doc title: "Pay stubs" (9 characters). Description: Ten semimonthly stubs, May to Oct 2026: taxes, deductions and take-home.
- Intake: pay stub (PDF). CSV view: paystubs.csv. Starter template: templates/paystubs.json
- Read by calcs: paycheck_calc, paystub_baseline, bills_takehome, drift_calc, scores_calc, payoff_engine, plan_year_calc, coverage_calc, checks_calc, badges_calc, seasons_calc, stubcheck_calc, paycheck_plan
- Shown on pages: bills, cash, debt, home, pay, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| pay_date | string | date YYYY-MM-DD |
| period_start | string | date YYYY-MM-DD |
| period_end | string | date YYYY-MM-DD |
| gross | number | USD |
| federal_tax | number | USD |
| state_tax | number | USD |
| fica | number | USD |
| taxes | number | USD |
| k401_you | number | USD |
| hsa_you | number | USD |
| hsa_employer | number | USD |
| benefits | number | USD |
| garnishment | number | USD |
| take_home_core | number | number |
| net_pay | number | USD |
| other_additions | number | number |
| ytd_gross | number | USD |
| ytd_net | number | USD |
| ytd_taxes | number | USD |
| ytd_federal | number | USD |
| ytd_state | number | USD |
| ytd_social_security | number | USD |
| ytd_medicare | number | USD |
| ytd_deductions | number | USD |
| ytd_garnishments | number | USD |
| ytd_reimbursement | number | USD |
| ytd_401k_you | number | USD |
| ytd_hsa_you | number | USD |
| ytd_hsa_employer | number | USD |

### plan_stats

Plan statement totals for a period: beginning and ending balance, personal return, dividends and stock/bond split.

- Class: user. Key field: period_start. File shape: array. Optional: add it when the situation applies.
- Doc title: "Plan statement totals" (21 characters). Description: Ending balance, time-weighted return and allocation from the plan statement.
- Intake: plan statement. CSV view: plan_stats.csv. Starter template: templates/plan_stats.json
- Read by calcs: plan_calc
- Shown on pages: investments

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| period_start | string | date YYYY-MM-DD |
| period_end | string | date YYYY-MM-DD |
| beginning_balance | number | USD |
| ending_balance | number | USD |
| personal_return_pct | number | percent |
| dividends_interest | number | USD |
| stock_pct | number | percent |
| bond_pct | number | percent |

### plan_summary

Where a plan balance came from (contributions, growth, other).

- Class: user. Key field: item. File shape: array. Optional: add it when the situation applies.
- Doc title: "Plan balance sources" (20 characters). Description: Where the first account balance came from, first to last statement.
- Intake: plan statement. CSV view: plan_summary.csv. Starter template: templates/plan_summary.json
- Read by calcs: plan_calc
- Shown on pages: investments

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| item | string | text |
| amount | number | USD |

### price_history

Start and end price of each held fund for the fund price growth chart.

- Class: user. Key field: fund. File shape: array. Optional: add it when the situation applies.
- Doc title: "Fund price history" (18 characters). Description: Fund prices at the first and last statement dates.
- Intake: statements or a price page. CSV view: price_history.csv. Starter template: templates/price_history.json
- Read by calcs: price_growth
- Shown on pages: investments

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| fund | string | text |
| start_date | string | date YYYY-MM-DD |
| start_price | number | USD |
| end_date | string | date YYYY-MM-DD |
| end_price | number | USD |
| note | string | text |

### profile_answers

Your yes, no or unknown answer for each category of debt, bill, account and retirement plan, so nothing is assumed to exist.

- Class: user. Key field: question_id. File shape: array. Needed for a first build.
- Doc title: "What you have" (13 characters). Description: One answer per kind of debt, bill and account: yes, no or not asked yet. Nothing is assumed to exist.
- Intake: first-run interview. CSV view: profile_answers.csv. Starter template: templates/profile_answers.json
- Read by calcs: profile_calc
- Shown on pages: tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| question_id | string | id |
| category | null or string | text |
| answer | string | text |
| count | null or number | count or year |
| asked_at | null |  |
| note | string | text |

### retirement_plan

Your age, target retirement age, goal range and yearly contribution settings.

- Class: user. Key field: item. File shape: array. Needed for a first build.
- Doc title: "Retirement plan inputs" (22 characters). Description: Goals, ages, contribution limits and the chosen 401(k) percent.
- Intake: first-run interview. CSV view: retirement_plan.csv. Starter template: templates/retirement_plan.json
- Read by calcs: contrib_calc, retire_model, pct_calc, scores_calc, age_calc, plan_year_calc, coverage_calc, bench_age_calc, pace_steps_calc
- Shown on pages: bills, cash, home, progress, retirement, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| item | string | text |
| value | null or number | USD |
| note | string | text |

### savings_goals

Things you are saving for: amount needed, saved, date, monthly amount and the account that holds the money.

- Class: user. Key field: goal_id. File shape: array. Optional: add it when the situation applies.
- Doc title: "Savings goals" (13 characters). Description: Savings goals with target, date, amount saved and monthly amount, for the forecast. A goal_id none row means no goals are set.
- Intake: interview. CSV view: savings_goals.csv. Starter template: templates/savings_goals.json
- Read by calcs: goals_calc
- Shown on pages: cash, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| goal_id | string | id |
| name | string | text |
| target_amount | number | USD |
| target_date | string | date YYYY-MM-DD |
| saved_so_far | number | USD |
| monthly_contribution | number | USD |
| interest_rate_pct | number | percent |
| account | string | text |
| as_of_date | string | date YYYY-MM-DD |
| source | string | text |
| status | string | text |
| note | string | text |

### scores_config

Definition of the Financial and Risk scores: components, weights, best and worst levels, labels and inputs. See SCORING.md.

- Class: config. Key field: version (object with a rows array). File shape: object with rows. Needed for a first build.
- Doc title: "Score settings" (14 characters). Description: Components, weights and ranges for the Financial and Risk Scores. One settings row.
- Intake: shipped default, changed only by decision. CSV view: scores_config.csv. Starter template: templates/scores_config.json
- Read by calcs: scores_calc, checks_calc
- Shown on pages: bills, cash, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| version | string | text |
| as_of | string | text |
| disclaimer | string | text |
| how_it_works | string | text |
| reweight | object | JSON text (nested) |
| trend | object | JSON text (nested) |
| inputs | object | JSON text (nested) |
| financial | object | JSON text (nested) |
| risk | object | JSON text (nested) |

### scores_history

Month-end snapshots of both scores with their component points.

- Class: history. Key field: date. File shape: array. Optional: add it when the situation applies.
- Doc title: "Score history" (13 characters). Description: Monthly score snapshots; trends show after 6 snapshots over 60 days.
- Intake: month-end snapshot written by Claude. CSV view: scores_history.csv. Starter template: templates/scores_history.json
- Read by calcs: score_since_calc
- Shown on pages: (not drawn directly; read by other calcs)

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| date | string | date YYYY-MM-DD |
| financial | number | number |
| risk | number | number |
| financial_label | string | text |
| risk_label | string | text |
| n_components_financial | number | count or year |
| n_components_risk | number | count or year |
| coverage_financial | number | number |
| coverage_risk | number | number |
| config_version | string | text |
| component_points | object | JSON text (nested) |
| note | string | text |

### tasks

The task list: area, status, due date, priority, effort, verification fields.

- Class: queue. Key field: id. File shape: array. Needed for a first build.
- Doc title: "Tasks" (5 characters). Description: To-do list with due date, priority and a linked data request, to complete the data and improve the metrics.
- Intake: created by Claude; changed with the task buttons. CSV view: tasks.csv. Starter template: templates/tasks.json
- Read by calcs: next_move, quests_calc, task_rank_calc, statement_sync_calc, needs_calc
- Shown on pages: debt, home, progress, tasks

| Field | Value types seen | Unit (heuristic) |
|---|---|---|
| area | string | text |
| status | string | text |
| note | string | text |
| verified_on | null or string | date YYYY-MM-DD |
| checks_run | array or null | JSON text (nested) |
| metric | null or string | text |
| metric_before | null |  |
| metric_after | null |  |
| effort | null or string | text |
| done_date | null or string | date YYYY-MM-DD |
| due_date | null or string | date YYYY-MM-DD |
| priority | null or string | text |
| created | string | text |
| queue_id | null or string | id |
| id | string | id |
| task | string | text |

