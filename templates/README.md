# templates: starter files, one per dataset

Every file is a valid dataset for the Money Operations Center: upload it as an asset (one per publish call), write its dataset document (title of 30 characters or less, description), and the page loads.

- User, history and queue datasets hold one row with every field null. The runtime cannot load an empty array, so this row stands in for "nothing yet"; the calcs strip it. Replace it with your first real row, or add rows and remove it. Never leave a dataset with zero rows.
- Reference and config datasets (age_benchmarks, dti_benchmarks, ltv_benchmarks, contribution_limits, age_metrics_config, scores_config, assumptions) ship with content: published statistics with their source and year, and settings with defaults. Confirm the limits and statistics against the source each year (SOURCES_AND_BENCHMARKS.md); the contribution limits and assumptions here are examples.
- planned/metric_snapshots.json is a PLANNED dataset. No page reads it yet (CORRELATIONS.md). Do not upload it until it is built.

Field meanings, units and which calcs read each dataset: DATA_MODEL.md. What to ask for each: INTAKE_GUIDE.md and QUESTIONS.md.

| Dataset | Class | Content | Rows |
|---|---|---|---|
| acct_a_holdings | user | one row, every field null | 1 |
| acct_a_sources | user | one row, every field null | 1 |
| acct_b_holdings | user | one row, every field null | 1 |
| age_benchmarks | reference | shipped content | 320 |
| age_metrics_config | config | shipped content | 1 |
| assets_other | user | one row, every field null | 1 |
| assumptions | config | shipped content | 6 |
| balance_log | history | one row, every field null | 1 |
| bank_flows | user | one row, every field null | 1 |
| bank_statements | user | one row, every field null | 1 |
| bill_statements | user | one row, every field null | 1 |
| bills | user | one row, every field null | 1 |
| card_statements | user | one row, every field null | 1 |
| card_transactions | user | one row, every field null | 1 |
| cash_events | user | one row, every field null | 1 |
| contribution_limits | reference | shipped content | 1 |
| contribution_plan | user | one row, every field null | 1 |
| contributions | user | one row, every field null | 1 |
| data_queue | queue | one row, every field null | 1 |
| drift_history | history | one row, every field null | 1 |
| dti_benchmarks | reference | shipped content | 5 |
| holding_snapshots | user | one row, every field null | 1 |
| income_pay | user | one row, every field null | 1 |
| liabilities | user | one row, every field null | 1 |
| ltv_benchmarks | reference | shipped content | 3 |
| milestones | history | one row, every field null | 1 |
| mortgage_statements | user | one row, every field null | 1 |
| networth_history | history | one row, every field null | 1 |
| payoff_settings | user | one row, every field null | 1 |
| paystubs | user | one row, every field null | 1 |
| plan_stats | user | one row, every field null | 1 |
| plan_summary | user | one row, every field null | 1 |
| price_history | user | one row, every field null | 1 |
| profile_answers | user | one row, every field null | 1 |
| retirement_plan | user | one row, every field null | 1 |
| savings_goals | user | one row, every field null | 1 |
| scores_config | config | shipped content | 1 |
| scores_history | history | one row, every field null | 1 |
| tasks | queue | one row, every field null | 1 |
| planned/metric_snapshots | history | PLANNED, one row, every field null | 1 |
