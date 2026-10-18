# CALCS: every calculation

58 calcs. A calc is a pure function of its input datasets and other calcs: `dash.calc(id, {title, description, inputs, fn})`. It returns rows or a "Needs: ..." message, never a guess and never an exception: a calc that throws takes down every calc that lists it as an input (RUNTIME_CONTRACT.md). The page draws only from calcs and datasets; no number is typed into the page.

Design and rules in full: ARCHITECTURE.md sections 7 and 8 (net worth, housing and DTI, loan to value, payoff engine, bills, runway, pay, drift, holdings, retirement, scores, age statistics, progress, tasks, checks). Scores: SCORING.md. Checks: CORRELATIONS.md.

How tests cover calcs: tests/unit.js has one or more fixture tests per calc with hand worked expected values; tests/inv_run.js runs the 34 page invariants on the current data, on the fixture, on the fixture with every dataset reduced to its starter row, and on each single dataset reduced to its starter row (no calc may throw for a missing category); tools/engine.js runs the calcs through the runtime's own parser and calc engine without a browser.

## Index

| Calc id | Title | Inputs | Shown on |
|---|---|---|---|
| nw_rows | Net worth, known items | acct_a_sources, acct_b_holdings, assets_other, liabilities | home, progress, tasks |
| nw_steps | Net worth waterfall steps | acct_a_sources, acct_b_holdings, assets_other, liabilities | home |
| acct_a_hist | First account balance by export date | holding_snapshots | (not drawn directly; read by other calcs) |
| home_calc | House equity and proceeds | assets_other, liabilities | debt, home, progress, tasks |
| house_split | House value split | assets_other, liabilities | debt |
| holdings_calc | Holdings totals | acct_a_holdings, acct_b_holdings | (not drawn directly; read by other calcs) |
| all_holdings | All holdings, one list | acct_a_holdings, acct_b_holdings, holding_snapshots, acct_a_sources | investments |
| by_class | Holdings by asset type | all_holdings | investments |
| perf_series | Holding growth over time | holding_snapshots | investments |
| plan_calc | First account lifetime summary | plan_stats, plan_summary | investments |
| price_growth | Fund price growth | price_history | investments |
| account_calc | Balances by account | acct_a_sources, acct_b_holdings | home |
| pending_calc | Pending tax refunds | assets_other, liabilities | (not drawn directly; read by other calcs) |
| paycheck_calc | Latest paycheck | paystubs | pay |
| paystub_baseline | Pay stub year to date | paystubs | pay |
| bills_calc | Monthly bills total | bills, income_pay | (not drawn directly; read by other calcs) |
| bills_takehome | Bills against take-home | bills, income_pay, paystubs | (not drawn directly; read by other calcs) |
| bills_cat | Bills by category | bills | bills |
| contrib_calc | Contributions against limits | contributions, income_pay, retirement_plan, assumptions | retirement |
| retire_model | Retirement model | assumptions, retirement_plan, contributions, income_pay, acct_a_holdings, acct_b_holdings | home, progress, retirement, tasks |
| pace_calc | Balance at target age | retire_model | home, progress, retirement, tasks |
| age60_calc | Balance at 60 headline | retire_model | (not drawn directly; read by other calcs) |
| need_calc | Saving needed for the low goal | retire_model | (not drawn directly; read by other calcs) |
| dti_calc | Debt to income | income_pay, liabilities, bills | debt, home, progress, tasks |
| payoff_calc | Debt payoff schedule | liabilities | debt |
| pay_flow | Where gross monthly pay goes | income_pay, liabilities, bills, contributions | pay |
| pay_split | Gross pay split | dti_calc, bills_calc, contrib_calc | (not drawn directly; read by other calcs) |
| goals_calc | Savings goals | savings_goals, assets_other | cash, home, progress, tasks |
| drift_calc | Dollar drift | balance_log, cash_events, paystubs | cash, home, progress, tasks |
| pct_calc | 401(k) percent of salary | retire_model, retirement_plan, income_pay, assumptions | retirement |
| scores_calc | Financial and Risk scores | scores_config, liabilities, assets_other, bills, income_pay, contributions, networth_history, retirement_plan, assumptions, data_queue, paystubs | bills, cash, home, progress, tasks |
| age_calc | Where you stand at your age | age_benchmarks, age_metrics_config, retirement_plan, networth_history, income_pay, contributions, liabilities, scores_calc, dti_calc, pace_calc | home, progress, tasks |
| payoff_engine | Debt payoff plan | liabilities, bills, paystubs, income_pay, assets_other, payoff_settings | bills, cash, debt, home, progress, tasks |
| drift_all | Dollar drift, every account | balance_log, cash_events, card_transactions | cash, home, progress, tasks |
| plan_year_calc | Plan-year 401(k) tile | contribution_limits, contribution_plan, paystubs, income_pay, retirement_plan | home, progress, retirement, tasks |
| milestones_calc | Milestones | liabilities, networth_history, scores_calc | progress |
| coverage_calc | Coverage rings | scores_calc, drift_all, income_pay, paystubs, contributions, retirement_plan, assumptions, liabilities, assets_other, bills, balance_log, cash_events, networth_history, holding_snapshots | home, progress, tasks |
| checks_calc | Correlation checks | nw_rows, networth_history, retire_model, paystubs, contributions, income_pay, liabilities, assets_other, data_queue, drift_calc, drift_all, goals_calc, home_calc, payoff_engine, age_calc, scores_calc, plan_year_calc, scores_config, cash_events, balance_log, bills, mortgage_statements, acct_a_holdings, holding_snapshots, acct_a_sources, milestones, statement_sync_calc | home, progress, tasks |
| next_move | Next best move | checks_calc, tasks, scores_calc, payoff_engine, coverage_calc | home, progress, tasks |
| runway_calc | Cash runway | scores_calc, payoff_engine, liabilities, age_benchmarks, bills | bills, cash, home, tasks |
| checkins_calc | Weekly check-ins | balance_log, drift_all | home, progress |
| recap_calc | Weekly recap | checkins_calc, balance_log, drift_all, next_move, data_queue | progress |
| quests_calc | Quests | tasks, data_queue, scores_calc, liabilities, balance_log, cash_events, drift_all | (not drawn directly; read by other calcs) |
| badges_calc | Badges | coverage_calc, scores_calc, card_transactions, liabilities, paystubs, checkins_calc, milestones, pace_steps_calc | (not drawn directly; read by other calcs) |
| seasons_calc | Seasons | balance_log, paystubs, runway_calc, payoff_engine | home |
| moves_calc | What would move you up one rung | age_calc, scores_calc, payoff_engine | progress |
| stubcheck_calc | Pay stubs and the bank | paystubs, cash_events | pay |
| ltv_calc | Loan to value | home_calc, ltv_benchmarks, liabilities | debt |
| debt_steps_calc | Per debt milestones | liabilities | (not drawn directly; read by other calcs) |
| bench_age_calc | Benchmarks by age | age_benchmarks, income_pay, retirement_plan, networth_history | progress |
| score_since_calc | Since last month | scores_history | (not drawn directly; read by other calcs) |
| pace_steps_calc | Pace to your low goal, every account | pace_calc, retirement_plan | retirement |
| task_rank_calc | Ranked tasks | tasks, scores_calc, data_queue | home |
| statement_sync_calc | Statement requests in sync | data_queue, tasks | home, progress, tasks |
| profile_calc | What you have | profile_answers, liabilities, bills, assets_other, contributions | tasks |
| needs_calc | What the cards are waiting for | profile_calc, payoff_engine, dti_calc, goals_calc, drift_calc, plan_year_calc, runway_calc, paycheck_plan, data_queue, tasks | tasks |
| checks_more_calc | More correlation checks | bills, liabilities, bill_statements, dti_calc, payoff_engine, profile_calc, needs_calc, card_statements | (not drawn directly; read by other calcs) |
| paycheck_plan | Paycheck plan | payoff_engine, liabilities, bills, income_pay, paystubs | debt, tasks |

## Calcs in detail

### nw_rows
- Title: Net worth, known items
- Rule: Retirement balances plus house, truck, cash (savings and checking) and uninvested HSA cash, minus debts. Receivables such as pending refunds are excluded.
- Inputs: acct_a_sources, acct_b_holdings, assets_other, liabilities
- Feeds: checks_calc
- Output: array of row objects (item, amount)
- Shown on: home, progress, tasks

### nw_steps
- Title: Net worth waterfall steps
- Rule: Assets stepping up, each debt stepping down, ending at net worth.
- Inputs: acct_a_sources, acct_b_holdings, assets_other, liabilities
- Feeds: drawn by the page only
- Output: array of row objects (label, value, kind, type)
- Shown on: home

### acct_a_hist
- Title: First account balance by export date
- Rule: Total of the first account funds at each holdings export date, used for sparklines and the change since the first export.
- Inputs: holding_snapshots
- Feeds: drawn by the page only
- Output: array of row objects (date, value)
- Shown on: (not drawn directly; read by other calcs)

### home_calc
- Title: House equity and proceeds
- Rule: Value minus mortgage and HELOC, loan to value, and a net-if-sold range using the selling-cost percentages on the house row.
- Inputs: assets_other, liabilities
- Feeds: checks_calc, ltv_calc
- Output: array of row objects (value, balance, equity, ltv_pct, first_lien_pct, purchase_price, gain_since_purchase, net_proceeds_low, net_proceeds_high)
- Shown on: debt, home, progress, tasks

### house_split
- Title: House value split
- Rule: House value divided into the mortgage, the HELOC and your equity.
- Inputs: assets_other, liabilities
- Feeds: drawn by the page only
- Output: array of row objects (part, amount)
- Shown on: debt

### holdings_calc
- Title: Holdings totals
- Rule: Sum of fund values, cost basis and gain or loss across the first account, plus the second 401(k) value.
- Inputs: acct_a_holdings, acct_b_holdings
- Feeds: drawn by the page only
- Output: array of row objects (total_all, total_est, total_hi, cost_basis, gain_total, gain_pct, funds)
- Shown on: (not drawn directly; read by other calcs)

### all_holdings
- Title: All holdings, one list
- Rule: Every fund held, with its account, its share of that account, cost basis, gain or loss, return on cost where reported, and a small return history.
- Inputs: acct_a_holdings, acct_b_holdings, holding_snapshots, acct_a_sources
- Feeds: by_class
- Output: array of row objects (holding, ticker, account, asset_class, value, share_pct, cost_basis, gain_loss, return_pct, spark)
- Shown on: investments

### by_class
- Title: Holdings by asset type
- Rule: Total value of all holdings by asset type.
- Inputs: all_holdings
- Feeds: drawn by the page only
- Output: array of row objects (cls, value, share_pct)
- Shown on: investments

### perf_series
- Title: Holding growth over time
- Rule: Return on cost for each holding at average cost and at each snapshot date.
- Inputs: holding_snapshots
- Feeds: drawn by the page only
- Output: array of row objects (fund, account, point, order, return_pct)
- Shown on: investments

### plan_calc
- Title: First account lifetime summary
- Rule: Plan statement totals with the yearly equivalent of the time-weighted return and the share of the ending balance that is market growth.
- Inputs: plan_stats, plan_summary
- Feeds: drawn by the page only
- Output: array of row objects (personal_return_pct, years, annualized_pct, growth_share_pct, ending_balance)
- Shown on: investments

### price_growth
- Title: Fund price growth
- Rule: Price change and yearly equivalent from the start date to the end date for each fund with a start price.
- Inputs: price_history
- Feeds: drawn by the page only
- Output: array of row objects (fund, start_price, end_price, start_date, end_date, growth_pct, annualized_pct, note)
- Shown on: investments

### account_calc
- Title: Balances by account
- Rule: Each held account today: the first account split into its Traditional (pre-tax) and Roth source balances, and the second 401(k). The HSA is uninvested cash and is not included here.
- Inputs: acct_a_sources, acct_b_holdings
- Feeds: drawn by the page only
- Output: array of row objects (account, bucket, now_usd)
- Shown on: home

### pending_calc
- Title: Pending tax refunds
- Rule: Refunds still owed to you, the tax balance owed that could be applied first, and what is left if it is fully applied.
- Inputs: assets_other, liabilities
- Feeds: drawn by the page only
- Output: array of row objects (pending_refunds, irs_balance, after_offset)
- Shown on: (not drawn directly; read by other calcs)

### paycheck_calc
- Title: Latest paycheck
- Rule: Take-home, taxes and deductions on the latest pay stub, and the monthly take-home.
- Inputs: paystubs
- Feeds: drawn by the page only
- Output: array of row objects (pay_date, gross, take_home, take_home_month, tax_rate_pct, saved_per_check, first_take_home, stub_count)
- Shown on: pay

### paystub_baseline
- Title: Pay stub year to date
- Rule: Year-to-date totals printed on each pay stub: the tax rate, the share kept as take-home, and what was saved (401(k) plus HSA, both parts), per pay date. The last row is the latest.
- Inputs: paystubs
- Feeds: drawn by the page only
- Output: array of row objects (pay_date, ytd_gross, tax_rate_pct, take_home_pct, saved_ytd, saved_pct, latest)
- Shown on: pay

### bills_calc
- Title: Monthly bills total
- Rule: Sum of monthly bills, the yearly equivalent, and the share of gross monthly pay.
- Inputs: bills, income_pay
- Feeds: pay_split
- Output: array of row objects (total_monthly, total_annual, pct_of_gross, essential_monthly, optional_monthly)
- Shown on: (not drawn directly; read by other calcs)

### bills_takehome
- Title: Bills against take-home
- Rule: Monthly take-home pay (latest stub take-home times paychecks per year, divided by 12, an estimate) and the monthly bills as a share of it. Gross figures stay in the bills and debt ratio calcs.
- Inputs: bills, income_pay, paystubs
- Feeds: drawn by the page only
- Output: array of row objects (take_home_month, take_home_per_paycheck, paychecks_per_year, bills_monthly, pct_of_take_home, gross_monthly)
- Shown on: (not drawn directly; read by other calcs)

### bills_cat
- Title: Bills by category
- Rule: Monthly bill totals grouped by category with each category share of the total.
- Inputs: bills
- Feeds: drawn by the page only
- Output: array of row objects (category, total, share_pct)
- Shown on: bills

### contrib_calc
- Title: Contributions against limits
- Rule: Yearly, monthly and per-paycheck contributions from the pay stubs next to each account limit, with the room left and what each adds by the target age.
- Inputs: contributions, income_pay, retirement_plan, assumptions
- Feeds: pay_split
- Output: array of row objects (account_id, account, tax_type, employee_annual, employer_annual, total, limit, plan_total, gap, pct_of_limit, per_paycheck_now, per_paycheck_max, pct_pay_now, pct_pay_max, fv_now, fv_plan, monthly_now, plan_monthly)
- Shown on: retirement

### retire_model
- Title: Retirement model
- Rule: Everything the retirement chart needs: starting balance now, ages, goals, return paths from the assumptions file, each account contribution today and with every account at its limit (maxed), and the balance at the target age for both on all three paths. Contributions at year end, before tax, in today's dollars.
- Inputs: assumptions, retirement_plan, contributions, income_pay, acct_a_holdings, acct_b_holdings
- Feeds: pace_calc, age60_calc, need_calc, pct_calc, checks_calc
- Output: array of row objects (start, start_nb, start_hi, age0, age1, years, goal_low, goal_high, rates, nominal, inflation, paychecks, base_pay_pp, accts, now_total, plan_total, scenarios, ladder, need_annual)
- Shown on: home, progress, retirement, tasks

### pace_calc
- Title: Balance at target age
- Rule: Balance at the target age for today and with every account at its limit (maxed) on each return path, and whether the base path reaches the low goal.
- Inputs: retire_model
- Feeds: age_calc, pace_steps_calc
- Output: array of row objects (scenario, annual, end_lo, end_mid, end_hi, pct_low_goal, on_pace)
- Shown on: home, progress, retirement, tasks

### age60_calc
- Title: Balance at 60 headline
- Rule: Balance at the target age at the base return for today's contribution rate and with every account at its limit (maxed), with the gap to the low goal.
- Inputs: retire_model
- Feeds: drawn by the page only
- Output: array of row objects (age, now_end, plan_end, now_annual, plan_annual, extra_year, extra_month, goal_low, gap_now, over_plan, now_lo, now_hi, plan_lo, plan_hi)
- Shown on: (not drawn directly; read by other calcs)

### need_calc
- Title: Saving needed for the low goal
- Rule: Yearly saving that reaches the low goal at the base return from today's balances, contributions at year end.
- Inputs: retire_model
- Feeds: drawn by the page only
- Output: array of row objects (need_7_annual)
- Shown on: (not drawn directly; read by other calcs)

### dti_calc
- Title: Debt to income
- Rule: Housing and total debt payments over gross monthly income, using the payments entered so far. The missing count shows debts still without a payment. The estimate fields add 4 percent of the balance of credit cards with no payment entered. Rent (Housing bills) joins the standard ratio only when no mortgage is on file; otherwise it is a second ratio. Total monthly obligations (one definition for the whole page) = every monthly bill (rent included, once) plus every debt payment entered; a debt still missing its payment is named, never guessed.
- Inputs: income_pay, liabilities, bills
- Feeds: pay_split, age_calc, needs_calc, checks_more_calc
- Output: array of row objects (rent_monthly, rent_names, rent_in_standard, back_end_with_rent_pct, obligations_monthly, obligations_annual, obligations_bills, obligations_debts, missing_names, est_card_balance, est_card_payment, debt_payments_est, back_end_est_pct, base_monthly, total_monthly, debt_payments, housing_payments, missing_payments, front_end_pct, back_end_pct, back_end_with_variable_pct, total_balance)
- Shown on: debt, home, progress, tasks

### payoff_calc
- Title: Debt payoff schedule
- Rule: Month by month balance of each debt with a balance if today's payment continues. The first payment is in the current month, the same start as the payoff plan (payoff_engine), so both give the same payoff month on minimums. The mortgage uses its principal and interest part, a tax balance without a rate carries no interest, and a credit line uses its payment with no new draws.
- Inputs: liabilities
- Feeds: drawn by the page only
- Output: array of row objects (name, type, liability_id, rate_pct, balance, payment, months, payoff, interest_total, balances, start_date)
- Shown on: debt

### pay_flow
- Title: Where gross monthly pay goes
- Rule: Links for the pay flow: gross monthly pay into debt payments, bills, your saving from pay and everything else, then into each debt, bill category and saving account.
- Inputs: income_pay, liabilities, bills, contributions
- Feeds: drawn by the page only
- Output: array of row objects (source, target, value, cls)
- Shown on: pay

### pay_split
- Title: Gross pay split
- Rule: Monthly base pay divided into debt payments, bills, saving from the paycheck and everything else.
- Inputs: dti_calc, bills_calc, contrib_calc
- Feeds: drawn by the page only
- Output: array of row objects (part, amount, base)
- Shown on: (not drawn directly; read by other calcs)

### goals_calc
- Title: Savings goals
- Rule: Per goal: progress, months to goal at the current monthly amount, monthly amount needed to hit the target date, a month by month forecast and a status. The first row is a summary across goals. Any missing required field gives a Needs list and no number. Interest is monthly compounding on the opening balance with the deposit at month end; no rate means 0 percent.
- Inputs: savings_goals, assets_other
- Feeds: checks_calc, needs_calc
- Output: array of row objects (kind, count, savings_balance, savings_asof, saved_needs, monthly_needs, total_saved, total_monthly, earmark_needs, earmarked, unassigned)
- Shown on: cash, home, progress, tasks

### drift_calc
- Title: Dollar drift
- Rule: For each pair of reported balance dates: opening reported, plus known inflows, minus known outflows, expected closing, reported closing, and the gap. Events with an unconfirmed account are only used in a total cash pair. A missing input gives a Needs message, never a guess.
- Inputs: balance_log, cash_events, paystubs
- Feeds: checks_calc, needs_calc
- Output: array of row objects (scope, from, to, status, needs, send, opening, reported, inflows, outflows, expected, drift, items)
- Shown on: cash, home, progress, tasks

### pct_calc
- Title: 401(k) percent of salary
- Rule: The 401(k) as a percent of base salary: salary, paychecks, today's dollars and percent, the annual limit as a dollar amount and as a percent of pay, and the percent you chose for next year (empty until you choose). The take-home estimate uses the tax saving rate on pre-tax dollars from the assumptions (item tax_saving_pretax_pct); without it the take-home change says Needs. Employer money does not count against the employee deferral limit.
- Inputs: retire_model, retirement_plan, income_pay, assumptions
- Feeds: drawn by the page only
- Output: array of row objects (salary, paychecks, cap_annual, cap_pct, cur_annual, cur_pct, cur_pp, employer, chosen_pct, tax_save)
- Shown on: retirement

### scores_calc
- Title: Financial and Risk scores
- Rule: Two 0 to 100 scores from scores_config. fraction = clamp((value - worst)/(best - worst)); Financial points = fraction x weight, Risk points = (1 - fraction) x weight; score = points / weight with data x 100. Components without data are left out and listed as Needs; the range shows the score if every Needs scored worst or best. Under the minimum weight coverage the card shows Not enough data. What-ifs rerun the same function on changed copies of the data.
- Inputs: scores_config, liabilities, assets_other, bills, income_pay, contributions, networth_history, retirement_plan, assumptions, data_queue, paystubs
- Feeds: age_calc, milestones_calc, coverage_calc, checks_calc, next_move, runway_calc, quests_calc, badges_calc, moves_calc, task_rank_calc
- Output: array of row objects (version, as_of, disclaimer, how, trend, financial, risk, metrics, x, whatifs, room_fin, room_risk)
- Shown on: bills, cash, home, progress, tasks

### age_calc
- Title: Where you stand at your age
- Rule: Each metric in three percent forms: percent of guideline (value / guideline x 100, lower is better metrics use the limit), percent of typical (value / peer median x 100) and percentile rank (linear between the published p25, p50, p75, p90, p95 rows). A form without a sourced row says Needs data. Ratios are never shown as multiples. Every figure keeps its source, data year and dollar year.
- Inputs: age_benchmarks, age_metrics_config, retirement_plan, networth_history, income_pay, contributions, liabilities, scores_calc, dti_calc, pace_calc
- Feeds: checks_calc, moves_calc
- Output: array of row objects (age, bracket, bracket_label, salary, disclaimer, vocabulary, metrics, ladder, rows_used)
- Shown on: home, progress, tasks

### payoff_engine
- Title: Debt payoff plan
- Rule: Every non-mortgage debt with a balance. Surplus = monthly take-home (latest stub x paychecks per year / 12) minus every bill minus every debt payment you make (support withheld from pay is left out). Monthly compounding, minimums first, then the extra plus freed minimums in the chosen order. Smart order: past due first, then a late payment in the last 90 days, then the highest effective rate (a card paid in full each month costs no interest; a promo is ranked by the rate after it), ties by smaller balance. Missing rates, minimums or dates are listed as Needs. A saved strategy and extra amount (payoff_settings) are used when set; otherwise smart order and the cushion-safe extra.
- Inputs: liabilities, bills, paystubs, income_pay, assets_other, payoff_settings
- Feeds: checks_calc, next_move, runway_calc, seasons_calc, moves_calc, needs_calc, checks_more_calc, paycheck_plan
- Output: array of row objects (cash, mustpay, cash_months, cushion_shortfall, safe_extra, start, today, take_home, bills, mins, mins_rows, withheld, surplus, extra, extra_source, strategy, debts, plans, c17, needs, settings)
- Shown on: bills, cash, debt, home, progress, tasks

### drift_all
- Title: Dollar drift, every account
- Rule: Every account with two or more reported balances: bank accounts use expected = opening + known inflows - known outflows; cards use expected = opening + loaded charges - known payments, so a card balance above expected is unexplained spending. Weekly check points line up all accounts on the same date. ATM withdrawals count as a known outflow and are listed as cash spending not tracked. A trend shows only with 3 or more check points.
- Inputs: balance_log, cash_events, card_transactions
- Feeds: coverage_calc, checks_calc, checkins_calc, recap_calc, quests_calc
- Output: array of row objects (expected_events, expected_now, open_items, pending, accounts, grid, next_check, atm, unexplained_total, known_outflow, check_points, explained_pct)
- Shown on: cash, home, progress, tasks

### plan_year_calc
- Title: Plan-year 401(k) tile
- Rule: Year to date 401(k) from the stubs in the plan year against the limit for that year (prior year row labeled estimate when the year is not published). Remaining paychecks come from the pay calendar (first pay date and schedule), never from the number of stubs. Needed per paycheck = room left / remaining paychecks. Quarterly bars: even pace to the limit against what the stubs show.
- Inputs: contribution_limits, contribution_plan, paystubs, income_pay, retirement_plan
- Feeds: checks_calc, needs_calc
- Output: array of row objects (year, limit, limit_row, estimate, catchup, ytd, ytd_column, ytd_tie, stubs, remaining_paychecks, calendar_known, pace_pp, projected, room, need_pp, need_pct_pp, status, short_by, quarters, chosen_pct, try_rate, rate_used, rate_annual, rate_pp, ...)
- Shown on: home, progress, retirement, tasks

### milestones_calc
- Title: Milestones
- Rule: Derived from facts already in the datasets: per debt 25, 50, 75 percent paid and paid off against its original balance; emergency fund 0.5, 1, 2, 3 and 6 months; net worth crossing each 50,000. A stored milestone keeps its first-seen date; one whose condition is no longer true is shown as retracted, never deleted.
- Inputs: liabilities, networth_history, scores_calc
- Feeds: drawn by the page only
- Output: array of row objects (milestone_id, family, subject, step, value_at, evidence_dataset, evidence_id, direction, note, status)
- Shown on: progress

### coverage_calc
- Title: Coverage rings
- Rule: Picture: core datasets with a row fresher than 45 days (or undated rows present) over core datasets. Scores: weight of score components with data over total weight, averaged over both scores. Explained: known outflows over known outflows plus unexplained drift in the check windows; no check yet below one check point.
- Inputs: scores_calc, drift_all, income_pay, paystubs, contributions, retirement_plan, assumptions, liabilities, assets_other, bills, balance_log, cash_events, networth_history, holding_snapshots
- Feeds: next_move, badges_calc
- Output: array of row objects (date, picture_pct, picture_have, picture_total, picture, scores_pct, scores_weight_have, scores_weight_total, explained_pct, explained_checks, unexplained, known_outflow, missing)
- Shown on: home, progress, tasks

### checks_calc
- Title: Correlation checks
- Rule: The checks the page can run on its own data, numbered as in the architecture register: C1 net worth identity, C2 holdings sum, C3 retirement start, C4 stub arithmetic, C5 stub to contributions, C6 stub to income, C7 stub to cash, C9 DTI inputs, C10 LTV inputs, C11 bills to cash, C12 drift identity, C13 goals, C14 dates, C15 dataset privacy and dashes, C16 reward rows, C17 payoff simulation, C18 benchmark rows, C19 score inputs, C21 plan-year tile, C22 mortgage to statement, C23 Form 1098 to statements, C24 statement requests in sync. C8 (card to liability) runs at ingestion, not on the page. Status is pass, needs (data missing, no number is guessed) or fail.
- Inputs: nw_rows, networth_history, retire_model, paystubs, contributions, income_pay, liabilities, assets_other, data_queue, drift_calc, drift_all, goals_calc, home_calc, payoff_engine, age_calc, scores_calc, plan_year_calc, scores_config, cash_events, balance_log, bills, mortgage_statements, acct_a_holdings, holding_snapshots, acct_a_sources, milestones, statement_sync_calc
- Feeds: next_move
- Output: array of row objects (id, name, status, detail, blocks)
- Shown on: home, progress, tasks

### next_move
- Title: Next best move
- Rule: One move, ranked: (a) a failed check that blocks a core number; (b) the open task that unlocks the most score weight per unit of effort (effort not set counts as 1); (c) the first debt in the payoff plan when the surplus is positive; (d) the oldest stale core dataset; (e) nothing urgent.
- Inputs: checks_calc, tasks, scores_calc, payoff_engine, coverage_calc
- Feeds: recap_calc
- Output: array of row objects (kind, label, text, why, effort, task_id, others)
- Shown on: home, progress, tasks

### runway_calc
- Title: Cash runway
- Rule: Months = liquid cash / essentials a month (essential bills plus every debt payment, the same figures as the score). Steps at 0.5, 1, 2, 3 and 6 months; the gap to a step is step x essentials minus cash. This month comes from the payoff plan (take-home, bills, debt payments, left over). Coming up lists debt due dates in the next 45 days from the liabilities; a debt with no due date is listed as Needs.
- Inputs: scores_calc, payoff_engine, liabilities, age_benchmarks, bills
- Feeds: seasons_calc, needs_calc
- Output: array of row objects (months, liquid, mustpay, ess_bills, debt_payments, guideline, steps, next, plan_keep, plan_extra, this_month, coming, coming_needs, window_end, as_of)
- Shown on: bills, cash, home, tasks

### checkins_calc
- Title: Weekly check-ins
- Rule: A week (Monday to Sunday) counts as checked in when every account in balance_log has a reported balance inside one 3 day window of that week. Rolling window of the last 12 weeks; no streaks and nothing resets. A clean check is a check-in whose drift checks ending in that window are all within 25 dollars per account.
- Inputs: balance_log, drift_all
- Feeds: recap_calc, badges_calc
- Output: array of row objects (weeks, count, total, accounts, expected, tolerance, clean_checks, first_data, as_of)
- Shown on: home, progress

### recap_calc
- Title: Weekly recap
- Rule: One recap per check-in week: card and cash totals at the check-in compared with the previous check-in, unexplained drift in the check periods that end in that week, and the next best move. Shown on Home for 7 days after a check-in, then kept under Past weeks. A first check-in has nothing to compare with and says so.
- Inputs: checkins_calc, balance_log, drift_all, next_move, data_queue
- Feeds: drawn by the page only
- Output: array of row objects (recaps, latest, days_since, visible, next_check, days_to_next, next_move, data_provided, data_needed, as_of)
- Shown on: progress

### quests_calc
- Title: Quests
- Rule: A quest is a view over tasks and data requests grouped by what they unlock: one per score part in Needs (tasks whose metric names it), one per debt missing a rate or minimum (and the HELOC rate type), one per account with no balance in 60 days, and one to close the drift gap (events with an unconfirmed account and accounts over 25 dollars of drift). A task counts as done only when it is Done and verified.
- Inputs: tasks, data_queue, scores_calc, liabilities, balance_log, cash_events, drift_all
- Feeds: drawn by the page only
- Output: array of row objects (quest_id, title, unlocks, unlock_text, items, task_ids, done_count, total_count, status)
- Shown on: (not drawn directly; read by other calcs)

### badges_calc
- Title: Badges
- Rule: A badge is a rare, durable fact with a name. Earned when the fact is true in the data; a badge stored in milestones (family badge) keeps its first-seen date for good, and if the fact stops being true it says so instead of being taken away. On pace reads the same pace as the Age ladder (base path on today's pay stubs against the low goal). No points, levels or counts.
- Inputs: coverage_calc, scores_calc, card_transactions, liabilities, paystubs, checkins_calc, milestones, pace_steps_calc
- Feeds: drawn by the page only
- Output: array of row objects (badge_id, name, earned, earned_on, first_seen, still_true, fact, goal)
- Shown on: (not drawn directly; read by other calcs)

### seasons_calc
- Title: Seasons
- Rule: A season is a calendar quarter (winter, spring, summer, autumn), counted from the first dated row in the data, 13 weeks long. Three goals per season, all derived: reach the next cash step, pay off the first debt in the payoff plan from its first balance this season, and check in on 9 of the 13 weeks. Missing a week loses nothing; past seasons without balances are not scored.
- Inputs: balance_log, paystubs, runway_calc, payoff_engine
- Feeds: drawn by the page only
- Output: array of row objects (number, name, year, start, end, week, weeks, strip, goals, met, past, as_of)
- Shown on: home

### moves_calc
- Title: What would move you up one rung
- Rule: Three moves computed from the same calcs as the rest of the page: the 401(k) at its limit (months to the next ladder rung at today's 401(k) pace against at the limit, before growth), the next payment in the payoff plan (interest avoided a year = extra to that debt x its rate), and cash to 3 months of essentials. Score effects come from the score what-ifs, which rerun the score on changed copies of the data.
- Inputs: age_calc, scores_calc, payoff_engine
- Feeds: drawn by the page only
- Output: array of row objects (id, title, extra_month, next_pct, months_now, months_limit, sooner, d_fin, d_risk)
- Shown on: progress

### stubcheck_calc
- Title: Pay stubs and the bank
- Rule: Every stub with take-home, taxes, 401(k) and health (HSA you plus benefits), and the deposit seen in cash_events on the pay date. Gap = deposit minus net pay; a stub with no deposit on file says Needs.
- Inputs: paystubs, cash_events
- Feeds: drawn by the page only
- Output: array of row objects (pay_date, gross, take_home, net_pay, taxes, k401, health, withheld, deposit, gap)
- Shown on: pay

### ltv_calc
- Title: Loan to value
- Rule: Mortgage only = mortgage / home value; combined = mortgage plus HELOC / home value (the house equity numbers). Reference lines come from ltv_benchmarks with their sources; the PMI lines are measured on the original value by the servicer, so they are references, not tests.
- Inputs: home_calc, ltv_benchmarks, liabilities
- Feeds: drawn by the page only
- Output: array of row objects (value, first_lien_pct, combined_pct, first_balance, combined_balance, lines)
- Shown on: debt

### debt_steps_calc
- Title: Per debt milestones
- Rule: For each debt with a balance: steps at under 75, 50 and 25 percent of its first recorded (original) balance and at 0. For a revolving card the first recorded balance is the balance on its first statement. Paid percent never goes below 0 (a balance above the first recorded one shows 0 percent paid and no steps done). A debt without an original balance on file says Needs.
- Inputs: liabilities
- Feeds: drawn by the page only
- Output: array of row objects (id, name, type, original, balance, remaining_pct, paid_pct, steps, needs)
- Shown on: (not drawn directly; read by other calcs)

### bench_age_calc
- Title: Benchmarks by age
- Rule: The retirement guideline in dollars at your salary: a published salary multiple by age (anchors at 30, 40, 50, 60 and 67, straight lines between them, derived) and a published range (every 5 years from 30 to 65), with the publisher read from each row source. Your row compares your retirement balance with both.
- Inputs: age_benchmarks, income_pay, retirement_plan, networth_history
- Feeds: drawn by the page only
- Output: array of row objects (age, salary, retirement, rows, pct_fidelity, pct_trowe_low, pub_guideline, pub_range, sources)
- Shown on: progress

### score_since_calc
- Title: Since last month
- Rule: Uses the last scores_history row of each calendar month. With two month-end rows: the change in each score and the score parts that moved it most, largest first. With fewer, it says how many rows exist; no arrow, no trend word.
- Inputs: scores_history
- Feeds: drawn by the page only
- Output: array of row objects (available, month_rows)
- Shown on: (not drawn directly; read by other calcs)

### pace_steps_calc
- Title: Pace to your low goal, every account
- Rule: Base path balance at the target age on today's pay stubs as a percent of your low goal (the existing pace table), with steps at 25, 50, 75 and 100 percent.
- Inputs: pace_calc, retirement_plan
- Feeds: badges_calc
- Output: array of row objects (pct, balance, goal_low, target_age, steps)
- Shown on: retirement

### task_rank_calc
- Title: Ranked tasks
- Rule: Open tasks (status not Done) in order: due date first (a task without one goes after every dated task), then priority (High, Medium, not set, Low), then the score weight the task unlocks (largest first), then file order. Also the data requests still needed (status Needed or Statement requested) by their order number.
- Inputs: tasks, scores_calc, data_queue
- Feeds: drawn by the page only
- Output: array of row objects (tasks, open_count, to_do, open_status, done_count, data_needed, data_top)
- Shown on: home

### statement_sync_calc
- Title: Statement requests in sync
- Rule: A value that came from a screenshot keeps a data request with status Statement requested and an open task that asks for the downloaded statement; the two point at each other (task_id on the request, queue_id on the task). When the statement arrives the request becomes Provided and the task Done with a done date. Lists every pair that is out of step.
- Inputs: data_queue, tasks
- Feeds: checks_calc
- Output: array of row objects (pairs, issues, ok)
- Shown on: home, progress, tasks

### profile_calc
- Title: What you have
- Rule: One row per category of debt, bill, account and retirement plan: your answer (yes, no or unknown), how many rows the data holds, and what follows. No leaves the category out; yes with no rows asks for the details; unknown with no rows is a question to ask in Claude. Rows in the data count as yes. A no with rows on file is a conflict to resolve.
- Inputs: profile_answers, liabilities, bills, assets_other, contributions
- Feeds: needs_calc, checks_more_calc
- Output: array of row objects (question_id, category, kind, answer, answered, asked_at, rows, status, needs)
- Shown on: tasks

### needs_calc
- Title: What the cards are waiting for
- Rule: Every Needs line the calcs give, one row each with the data request (data_queue) and task that cover it: matched by the request needs_key when set, else by the subject and field words in the request text. Categories answered no are left out. A Needs row without a request is listed so Claude can add one; a request whose need is gone is listed as ready to mark Provided.
- Inputs: profile_calc, payoff_engine, dti_calc, goals_calc, drift_calc, plan_year_calc, runway_calc, paycheck_plan, data_queue, tasks
- Feeds: checks_more_calc
- Output: array of row objects (key, area, subject, text, from, queue_id, task_id, request_status, match)
- Shown on: tasks

### checks_more_calc
- Title: More correlation checks
- Rule: C25 bills and debts not double counted (no counted bill shares a name with a debt or is paid through one); C26 totals identity (total monthly obligations = every bill + every debt payment entered; payoff surplus = take-home - bills - minimums); C27 every bill statement belongs to a bill (bill_id, else the same name); C28 existence rule (no category answered no has rows; every category answered yes without rows has a data request); C29 every card statement belongs to a card on file and its latest minimum matches the minimum entered. Kept apart from the core checks so a new dataset that has not loaded cannot stop them.
- Inputs: bills, liabilities, bill_statements, dti_calc, payoff_engine, profile_calc, needs_calc, card_statements
- Feeds: drawn by the page only
- Output: array of row objects (id, name, status, detail, blocks)
- Shown on: (not drawn directly; read by other calcs)

### paycheck_plan
- Title: Paycheck plan
- Rule: What to pay from each of the next three paychecks. Pay dates come from the base income row (pay_schedule, first_pay_date, optional pay_days as text such as "10, last"; a weekend date moves to the Friday before), take-home from the latest stub. A bill or debt payment due on a date belongs to the latest paycheck on or before that date, so each is assigned to exactly one paycheck; one due before the first paycheck is listed apart as paid from cash on hand. Debt amounts are the minimums the payoff plan uses. Then the monthly extra of the payoff plan, divided by paychecks per year, goes to the debts the plan puts it on; an extra that does not fit this paycheck moves to the next one. Spending lines and items with no due day are set aside evenly. What is left is yours to keep. Missing pay dates or due days are Needs.
- Inputs: payoff_engine, liabilities, bills, income_pay, paystubs
- Feeds: needs_calc
- Output: array of row objects (as_of, take_home, per_year, schedule, strategy, extra_month, extra_per_paycheck, extra_unplaced, edges, horizon_end, before, paychecks, reserve, reserve_per_paycheck, undated_names, needs, needs_pay, next)
- Shown on: debt, tasks

