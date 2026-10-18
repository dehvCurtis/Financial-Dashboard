# TILES: every card, tile, chart and table the page draws

This file is generated from the rendered page (tools/tiles_inventory.js builds the page with the example data, opens each of the 10 pages and records every card, number tile, chart and table) together with hand notes (docs_src/tiles_notes.json). tools/check_docs.js fails when a rendered tile is missing here or this file lists a tile that does not render. Do not edit the tables by hand; edit the notes file or the code and regenerate.

Counts at this version: 64 number tiles, 82 cards, 25 charts, 13 tables.

## How to read the tables

| Column | Meaning |
|---|---|
| Tile id or key | the element id in the page, or "(no id)" |
| Label | the label on screen; amounts, percents and dates are masked as $N, N% and DATE |
| Key number or chart (example shape) | what the tile shows, in words or shape |
| Calcs and datasets | the calcs and datasets the tile reads (its Source on tap list) |
| Formula in words | how the number is worked out |
| Unit | USD, percent, months, count, date or text |
| Source on tap | the text shown when you tap the number |
| Chips | the small status words that can appear (Needs, Planned, Limit reached, Stale) |
| Links to | the larger chart or table the tile jumps to (the tile link rule, ARCHITECTURE.md section 13), or why there is none |
| About this fold | the closed "About this" text of the card |
| Needs when empty | what the tile says when it has no data, from the render of the all-empty starter (templates) |

Rules: every number tile links to the larger chart or table that shows the same number unless none exists (listed in the last section). Each card shows one key number or chart, short labels and chips; explanations sit in the closed About this fold; warnings and decision items stay visible.

## Page: home

### home: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| cs-checkin | Weekly check-in2 of N weeks, this week done | card | checkins_calc | A week (Monday to Sunday) counts as checked in when every account in balance_log has a reported balance inside one 3 day window of that week. Rolling window of the last 12 weeks; no streaks and nothing resets. A clean check is a check-in whose drift checks ending in that window are all within 25 dollars per account. |  | A week counts when all N accounts (Checking, Example Card Beta, Savings) have a balance within N days. Nothing resets; a | N of N weeks, this week done | target of tile links |  |  |
| hm-need | Needs attention3 tasks, N data to send | card | next_move, task_rank_calc, tasks, data_queue |  |  | Why: It is first in the payoff plan. | N tasks, N data to send; To do; Open | target of tile links |  | Needs: the task list |
| hm-hero | Net worth, known items | Net worth, known items | nw_rows, networth_history | Total assets minus total debts, with the change since the last month-end snapshot. | USD | Retirement, house, truck, savings, checking and uninvested HSA, minus every debt. Pending tax refunds are not counted. |  | nw-hist (Net worth over time) |  |  |
| (no id) | Net worth over time | card | networth_history, milestones |  |  | First seen DATE. Market moves and paychecks both count; noted, not celebrated | Earned | target of tile links | Dots only, no line, until there are three history rows. example Hover a point for assets and debts. |  |
| (no id) | From what you own to what you are worth | card | nw_steps | Assets stepping up, each debt stepping down, ending at net worth. |  |  |  | target of tile links | Assets step up (retirement, house, truck, savings and checking cash, and HSA), each debt steps down, and the last bar is net worth. The uninvested HSA cash is its own step. The pending tax refund is a |  |
| (no id) | What you own and what you owe | card | nw_steps | Assets stepping up, each debt stepping down, ending at net worth. |  |  |  | target of tile links | Both bars use the same scale. Assets count retirement, the house, the truck, savings and checking cash and the HSA. The pending tax refund is a receivable and stays out until it arrives. The uninveste |  |
| (no id) | Assets | card | assets_other |  |  |  |  | target of tile links | The house lives only here and the mortgage only under debts, so equity is never counted twice. Values are your own estimates until a Zillow or Kelley Blue Book range is added. |  |
| (no id) | Where each dollar sits | card | account_calc | Each held account today: the first account split into its Traditional (pre-tax) and Roth source balances, and the second 401(k). The HSA is uninvested cash and is not included here. |  |  |  | target of tile links | Retirement money by account today, with the first account split into its Traditional and Roth balances. Savings and checking cash are counted in net worth and shown in the card below, not in this ring |  |
| (no id) | Assets table | card | assets_other |  |  |  |  | target of tile links |  |  |
| hm-glance | Each subject, one number | One row per subject | dti_calc, bills_calc, runway_calc, holdings_calc, age60_calc, payoff_engine, age_calc, paycheck_calc | One key number per subject from the same calc as the subject page (Debt and DTI, Bills, Cash runway, Investments, Retirement, Pay, Age ladder). |  |  | Under N%; Short; Low | target of tile links |  | Needs: a pay stub, paychecks per year, how often you are paid and a first pay date / Needs: bills and cash / Needs: retirement plan and balances / Needs: age, s |
| hm-season | Season N, autumn | card | seasons_calc | A season is a calendar quarter (winter, spring, summer, autumn), counted from the first dated row in the data, 13 weeks long. Three goals per season, all derived: reach the next cash step, pay off the first debt in the payoff plan from its first balance this season, and check in on 9 of the 13 weeks. Missing a week loses nothing; past seasons without balances are not scored. |  | Three goals from your own numbers, no streaks to protect. Miss a week and the season just keeps going. |  | target of tile links |  |  |
| hm-payoff | Debt payoff plan | card | payoff_engine | Every non-mortgage debt with a balance. Surplus = monthly take-home (latest stub x paychecks per year / 12) minus every bill minus every debt payment you make (support withheld from pay is left out). Monthly compounding, minimums first, then the extra plus freed minimums in the chosen order. Smart order: past due first, then a late payment in the last 90 days, then the highest effective rate (a card paid in full each month costs no interest; a promo is ranked by the rate after it), ties by smaller balance. Missing rates, minimums or dates are listed as Needs. A saved strategy and extra amount (payoff_settings) are used when set; otherwise smart order and the cushion-safe extra. |  | Highest effective rate: N%. Needs: due date. |  | target of tile links |  | Needs: debts with balances |
| po-trail | Your payoff trail | card | payoff_engine | Every non-mortgage debt with a balance. Surplus = monthly take-home (latest stub x paychecks per year / 12) minus every bill minus every debt payment you make (support withheld from pay is left out). Monthly compounding, minimums first, then the extra plus freed minimums in the chosen order. Smart order: past due first, then a late payment in the last 90 days, then the highest effective rate (a card paid in full each month costs no interest; a promo is ranked by the rate after it), ties by smaller balance. Missing rates, minimums or dates are listed as Needs. A saved strategy and extra amount (payoff_settings) are used when set; otherwise smart order and the cushion-safe extra. |  | One stop per debt, in payoff order. The line fills with the share already paid when the original balance is on file. | N% of limit used; Minimum is an estimate; On plan; Secured; Secured by House | target of tile links |  | Needs: debts with balances |
| hm-scores | Financial Score parts | card | scores_calc | Two 0 to 100 scores from scores_config. fraction = clamp((value - worst)/(best - worst)); Financial points = fraction x weight, Risk points = (1 - fraction) x weight; score = points / weight with data x 100. Components without data are left out and listed as Needs; the range shows the score if every Needs scored worst or best. Under the minimum weight coverage the card shows Not enough data. What-ifs rerun the same function on changed copies of the data. |  | Financial: Could land N to N once the Needs are filled. Risk: Could land N to N once the Needs are filled. No trend yet: | Building; Elevated | target of tile links |  |  |

### home: tiles

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| hm-t-assets | Assets | Total assets | nw_rows, assets_other, acct_a_sources, acct_b_holdings | Retirement balances + home + vehicle + cash (savings and checking) + uninvested HSA cash. Receivables such as pending refunds are left out. | USD |  |  | nw-assets (Assets by type) |  |  |
| hm-t-debts | Debts | Total debts | nw_rows, liabilities | Sum of the balances of every debt row (the expected balance when it is newer than the reported one). | USD |  |  | debt-donut (Debt balance by debt) |  |  |
| hm-t-dti | Debt to income | Debt to income | dti_calc, income_pay, liabilities, bills | Monthly debt payments entered, over gross monthly income. Rent joins the headline only when there is no mortgage; a second labeled line always includes rent. | percent |  |  | debt-dti (Debt to income bars) |  | hidden until data exists |
| hm-t-cash | Cash | Cash, savings and checking | nw_rows, balance_log, assets_other | Latest reported balance of each cash account, added up. | USD |  |  | cs-balhist (Cash balance over time) |  |  |
| nw-net | Net worth, known items | Net worth, known items | nw_rows | Total assets minus total debts, using only values you have entered. | USD |  |  | nw-hist (Net worth over time) |  | hidden until data exists |
| nw-assets | Total assets | Total assets | nw_rows | Same total as the Assets tile on this page. | USD |  |  | nw-assets (Assets by type) |  | hidden until data exists |
| nw-debts | Total debts | Total debts | nw_rows, liabilities | Sum of debt balances. | USD |  |  | debt-donut (Debt balance by debt) |  | hidden until data exists |
| nw-ret | Retirement accounts | Retirement accounts | nw_rows, account_calc | Balance of every held account (first and second account sections of Investments). | USD |  |  | ov-donut (Retirement balance by account) |  | hidden until data exists |
| nw-cash | Cash, savings and checking | Cash, savings and checking | nw_rows | Latest reported cash balances. | USD |  |  | cs-balhist (Cash balance over time) |  | hidden until data exists |
| nw-hsa | HSA, uninvested | HSA, uninvested | nw_rows, assets_other | HSA cash that is not invested, from the assets list. | USD |  |  | nw-assets (Assets by type) |  | hidden until data exists |
| (no id) | Debts missing a payment | Count of debts with no payment entered | dti_calc | Number of debt rows where monthly_payment is empty. Each is a Needs and has a task. | count |  |  | none: the debts are listed in the Debts missing a payment card on the Debt page |  | hidden until data exists |
| nw-equity | Home equity | Home equity | nw_rows, home_calc | Home value minus mortgage and home equity line balances. | USD |  |  | nw-house (House value split) |  | hidden until data exists |
| (no id) | Net if sold | Net if sold range | home_calc | Home value minus liens minus selling costs, low and high, using the selling-cost percentages on the house row. | USD range |  |  | none: a range, not a single series; the house card shows the inputs |  | hidden until data exists |
| nw-refunds | Tax refunds pending | Tax refunds pending | pending_calc, assets_other | Refunds still owed to you (Receivable rows). Not counted in net worth. | USD |  |  | t-assets (Assets table) |  | hidden until data exists |
| hm-po-free | Debt free, no mortgage | Debt free month | payoff_engine | Month and year the last non-mortgage debt reaches zero on the chosen payoff plan (monthly compounding, minimums first, then the extra). | month |  |  | po-planchart (Debt payoff timeline, your payoff plan) |  | hidden until data exists |
| hm-po-int | Interest saved from DATE26 to payoff, versus minimums only | Interest saved | payoff_engine | Interest on the minimums-only path minus interest on the chosen plan. | USD | Paid off by DATE27 on the plan, DATE31 on minimums only (N debts never clear on minimums and are left out) |  | po-compare (Compare plans table) |  | hidden until data exists |

### home: charts

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| nw-hist | Net worth over time | chart: Net worth over time | networth_history |  |  |  |  | target of tile links |  |  |
| nw-wf | Waterfall from assets through each debt to net worth | chart: Waterfall from assets through each debt to net wor | nw_steps | Assets stepping up, each debt stepping down, ending at net worth. |  |  |  | target of tile links |  |  |
| nw-assets | Assets by type | chart: Assets by type | assets_other |  |  |  |  | target of tile links |  |  |
| ov-donut | Retirement balance by account | chart: Retirement balance by account | account_calc | Each held account today: the first account split into its Traditional (pre-tax) and Roth source balances, and the second 401(k). The HSA is uninvested cash and is not included here. |  |  |  | target of tile links |  |  |

### home: tables

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| t-assets |  | table | assets_other |  |  |  |  | target of tile links |  |  |

## Page: debt

### debt: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| card-owe | What you owe | card | liabilities |  |  |  |  | target of tile links | Balance by debt. The support withholding has no balance, so it only appears in the monthly payments below. Debts with no rate yet show "rate not added" on hover. Source: the Debts list, with the as-of |  |
| card-pay | Monthly debt payments | card | liabilities |  |  |  | N debt has no payment, total is a floor | target of tile links | Each payment is the amount you gave or the statement shows (see the notes on each debt). A mortgage payment includes escrow. Debts with no payment entered are not in this ring, so the total is a floor |  |
| nw-missing | Debts missing a payment (N) | card | dti_calc, payoff_engine, liabilities |  |  |  |  | target of tile links |  |  |
| po-plan | Debt payoff plan | card | payoff_engine | Every non-mortgage debt with a balance. Surplus = monthly take-home (latest stub x paychecks per year / 12) minus every bill minus every debt payment you make (support withheld from pay is left out). Monthly compounding, minimums first, then the extra plus freed minimums in the chosen order. Smart order: past due first, then a late payment in the last 90 days, then the highest effective rate (a card paid in full each month costs no interest; a promo is ranked by the rate after it), ties by smaller balance. Missing rates, minimums or dates are listed as Needs. A saved strategy and extra amount (payoff_settings) are used when set; otherwise smart order and the cushion-safe extra. |  |  | Minimum is an estimate; Interest $N; Saves $N; Estimate in use: Example Card Alpha; Needs: a minimum | target of tile links | Every debt gets its minimum first. The extra each month, plus the minimum of every debt already cleared, goes to the debt at the top of the order. The mortgage keeps its own schedule. A planning tool, | Needs: take-home from a pay stub and paychecks per year |
| po-trail-link |  | Link to the payoff trail on Home | payoff_engine | A link row, no number of its own. |  |  |  | target of tile links |  |  |
| pp-plan | Paycheck plan | card | paycheck_plan | What to pay from each of the next three paychecks. Pay dates come from the base income row (pay_schedule, first_pay_date, optional pay_days as text such as "10, last"; a weekend date moves to the Friday before), take-home from the latest stub. A bill or debt payment due on a date belongs to the latest paycheck on or before that date, so each is assigned to exactly one paycheck; one due before the first paycheck is listed apart as paid from cash on hand. Debt amounts are the minimums the payoff plan uses. Then the monthly extra of the payoff plan, divided by paychecks per year, goes to the debts the plan puts it on; an extra that does not fit this paycheck moves to the next one. Spending lines and items with no due day are set aside evenly. What is left is yours to keep. Missing pay dates or due days are Needs. |  |  | Due before the next paycheck $N; Short by $N on Fri DATE; Short by $N on Mon DATE | target of tile links | Each paycheck pays what is due before the next one, then the extra from your payoff plan. Spending and bills with no due day are set aside; the rest is yours to keep. A planning tool, not advice.Due b | Needs: a pay stub, paychecks per year, how often you are paid and a first pay date |
| card-timeline | Debt payoff timeline | card | payoff_calc, payoff_engine, tasks |  |  |  |  | target of tile links | All debtsWithout the mortgageBalance by month if you keep paying today's payments, the first payment in the current month (the same start as the payoff plan). A mortgage uses its principal and interes |  |
| card-rate | Rate against balance | card | liabilities |  |  |  |  | target of tile links | Higher on the chart means a higher rate; bigger bubbles are bigger monthly payments. Example Card Alpha at N% is the highest rate, and Example Card Beta at N% is second. A card costs no interest when  |  |
| card-dti | Debt to income | card | dti_calc, dti_benchmarks | Housing and total debt payments over gross monthly income, using the payments entered so far. The missing count shows debts still without a payment. The estimate fields add 4 percent of the balance of credit cards with no payment entered. Rent (Housing bills) joins the standard ratio only when no mortgage is on file; otherwise it is a second ratio. Total monthly obligations (one definition for the whole page) = every monthly bill (rent included, once) plus every debt payment entered; a debt still missing its payment is named, never guessed. |  |  |  | target of tile links | Within the classic guidelineUp to the conventional ceilingPast typical ceilingsYour ratio (dark bar)Total debt ratio is all monthly debt payments divided by gross monthly income; the housing ratio cou |  |
| card-house | House, in one place | card | house_split, home_calc |  |  |  |  | target of tile links | What the house is worth split into what you owe and what you own. An automated estimate is a range, not a number, and selling costs (N% to N%) reduce what you keep. |  |
| mn-ltv | Loan to value | card | ltv_calc, ltv_benchmarks | Mortgage only = mortgage / home value; combined = mortgage plus HELOC / home value (the house equity numbers). Reference lines come from ltv_benchmarks with their sources; the PMI lines are measured on the original value by the servicer, so they are references, not tests. |  |  |  | target of tile links |  | Needs: the house value and its loans |
| (no id) | The tables | card | liabilities, dti_benchmarks |  |  |  |  | target of tile links |  |  |

### debt: tiles

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| po-free | Debt free, no mortgage | Debt free month | payoff_engine | Same as the Home tile. | month | Source: the payoff plan with $N extra a month, Smart order. Details in About this | Interest $N; Saves $N | po-planchart (Debt payoff timeline, your payoff plan) |  | hidden until data exists |
| (no id) | Pay from the Fri DATE paycheck | Amount to pay from the next paycheck | paycheck_plan | Bills and debt payments due before the following paycheck, plus the extra on the first debt in the plan. | USD | Source: the paycheck plan, from the latest pay stub and each due date. Details in About this |  | none: the paycheck plan card holds the list of what is paid from it |  | hidden until data exists |
| (no id) | Left to keep | Left from the paycheck | paycheck_plan | Take-home minus what the plan pays from this paycheck. Negative means the paycheck does not cover what is due. | USD |  |  | none: shown with the paycheck plan it comes from |  | hidden until data exists |
| house-value | Value (your estimate) | Home value | home_calc, assets_other | Your estimate from the assets list, with its source and date. | USD |  |  | nw-house (House value split) |  | hidden until data exists |
| house-loans | Mortgage plus HELOC | Mortgage plus HELOC | home_calc, liabilities | Sum of the mortgage and home equity line balances. | USD |  |  | mn-ltv (Loan to value) |  | hidden until data exists |
| house-bought | Bought for | Bought for | assets_other | purchase_price on the house row. | USD |  |  | t-assets (Assets table) |  | hidden until data exists |
| (no id) | Gain since purchase | Gain since purchase | home_calc, assets_other | Home value minus purchase price. | USD |  |  | none: the Bought for tile and the value tile beside it hold the two inputs |  | hidden until data exists |

### debt: charts

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| debt-donut | Debt balance by debt | chart: Debt balance by debt | liabilities |  |  |  |  | target of tile links |  |  |
| debt-pay | Monthly payment by debt | chart: Monthly payment by debt | liabilities |  |  |  |  | target of tile links |  |  |
| debt-payoff | Stacked area of debt balances over time as scheduled payment | chart: Stacked area of debt balances over time as schedul | payoff_calc, tasks | Month by month balance of each debt with a balance if today's payment continues. The first payment is in the current month, the same start as the payoff plan (payoff_engine), so both give the same payoff month on minimums. The mortgage uses its principal and interest part, a tax balance without a rate carries no interest, and a credit line uses its payment with no new draws. |  |  |  | target of tile links |  |  |
| po-planchart | Stacked balances under your payoff plan, month by month | chart: Stacked balances under your payoff plan, month by  | payoff_engine | Every non-mortgage debt with a balance. Surplus = monthly take-home (latest stub x paychecks per year / 12) minus every bill minus every debt payment you make (support withheld from pay is left out). Monthly compounding, minimums first, then the extra plus freed minimums in the chosen order. Smart order: past due first, then a late payment in the last 90 days, then the highest effective rate (a card paid in full each month costs no interest; a promo is ranked by the rate after it), ties by smaller balance. Missing rates, minimums or dates are listed as Needs. A saved strategy and extra amount (payoff_settings) are used when set; otherwise smart order and the cushion-safe extra. |  |  |  | target of tile links |  |  |
| debt-bubble | Bubble chart of interest rate against balance for each debt, | chart: Bubble chart of interest rate against balance for  | liabilities |  |  |  |  | target of tile links |  |  |
| nw-house | House value split | chart: House value split | house_split | House value divided into the mortgage, the HELOC and your equity. |  |  |  | target of tile links |  |  |

### debt: tables

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| t-liab |  | table | liabilities |  |  |  |  | target of tile links |  |  |
| t-bench |  | table | dti_benchmarks |  |  |  |  | target of tile links |  |  |

## Page: bills

### bills: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| bl-coming | Coming up | card | runway_calc | Months = liquid cash / essentials a month (essential bills plus every debt payment, the same figures as the score). Steps at 0.5, 1, 2, 3 and 6 months; the gap to a step is step x essentials minus cash. This month comes from the payoff plan (take-home, bills, debt payments, left over). Coming up lists debt due dates in the next 45 days from the liabilities; a debt with no due date is listed as Needs. |  |  |  | target of tile links | Debt and bill due dates through DATE, from each debt's next due date or due day and each bill's due day. A month already paid is skipped; spending lines have no due day. | Needs: cash balances, essential bills and debt payments |
| card-cats | Bills by category | card | bills_cat | Monthly bill totals grouped by category with each category share of the total. |  |  |  | target of tile links | Utilities usually swing with the seasons. Each bill shows the amount you gave until a statement proves otherwise; the notes on each bill say where its amount came from. |  |
| card-ess | Essential and optional | card | bills |  |  |  |  | target of tile links | Bills you marked essential against the ones you could cut. |  |
| card-every | Every bill | card | bills |  |  |  |  | target of tile links | Bills are not debts, so they stay out of the debt ratio. A bill with a due day shows in Coming up on this page; send bank and card exports and actual payments land here. |  |
| card-billhist | Bill statement history | card | bill_statements |  |  |  |  | target of tile links |  | Needs: bill statements |

### bills: tiles

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| cf-take | Take-home pay per month | Take-home pay per month | bills_takehome, paystubs | Latest stub net pay x paychecks per year / 12 (an estimate). | USD | Source: the latest pay stub take-home times paychecks per year, divided by N. Details in About this |  | pc-chart (Each paycheck: take-home and deductions) |  | hidden until data exists |
| cf-debt | Debt payments | Debt payments | dti_calc, liabilities | Sum of monthly debt payments entered. | USD |  |  | debt-pay (Monthly payment by debt) |  | hidden until data exists |
| cf-bills | Bills per month | Bills per month | bills_calc, bills | Sum of counted monthly bill amounts (a bill paid through a debt is not counted twice). | USD |  |  | cf-donut (Bills by category) |  |  |
| (no id) | Left over this month | Left over | payoff_engine | Take-home minus bills minus debt payments (the payoff surplus). | USD |  |  | none: the same number is the This month box on the Cash runway card |  | hidden until data exists |

### bills: charts

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| cf-donut | Bills by category | chart: Bills by category | bills_cat | Monthly bill totals grouped by category with each category share of the total. |  |  |  | target of tile links |  |  |
| cf-ess |  | chart:  | bills |  |  |  |  | target of tile links |  |  |

### bills: tables

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| t-bills |  | table | bills |  |  |  |  | target of tile links |  |  |

## Page: cash

### cash: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| cs-runway | Cash runway | card | runway_calc | Months = liquid cash / essentials a month (essential bills plus every debt payment, the same figures as the score). Steps at 0.5, 1, 2, 3 and 6 months; the gap to a step is step x essentials minus cash. This month comes from the payoff plan (take-home, bills, debt payments, left over). Coming up lists debt due dates in the next 45 days from the liabilities; a debt with no due date is listed as Needs. |  |  | Essentials $N a month | target of tile links | Months of cash = cash $N (savings plus checking) divided by essentials $N a month: essential bills $N plus debt payments $N. Essentials are the bills and debt payments you cannot skip. This is the Eme | Needs: cash balances, essential bills and debt payments |
| cs-balhist | Cash balance over time | card | runway_calc, balance_log | Months = liquid cash / essentials a month (essential bills plus every debt payment, the same figures as the score). Steps at 0.5, 1, 2, 3 and 6 months; the gap to a step is step x essentials minus cash. This month comes from the payoff plan (take-home, bills, debt payments, left over). Coming up lists debt due dates in the next 45 days from the liabilities; a debt with no due date is listed as Needs. |  |  |  | target of tile links | Each dot is a balance you reported, by date; a hollow dot is the balance expected after the money movements you confirmed. Above the dashed line, the account alone covers one month of essentials. Dots | Needs: cash balances with their dates |
| hm-goals | Savings goals | card | goals_calc | Per goal: progress, months to goal at the current monthly amount, monthly amount needed to hit the target date, a month by month forecast and a status. The first row is a summary across goals. Any missing required field gives a Needs list and no number. Interest is monthly compounding on the opening balance with the deposit at month end; no rate means 0 percent. |  |  |  | target of tile links |  |  |
| card-goals | Savings goals | card | goals_calc, savings_goals | Per goal: progress, months to goal at the current monthly amount, monthly amount needed to hit the target date, a month by month forecast and a status. The first row is a summary across goals. Any missing required field gives a Needs list and no number. Interest is monthly compounding on the opening balance with the deposit at month end; no rate means 0 percent. |  |  | Active; Held in Savings | target of tile links |  | Needs: amount saved for / Needs: which account holds the goal money () / Needs: target amount, target date, amount saved so far, monthly contribution, date of t |
| card-drift | Dollar drift | card | drift_calc, balance_log, cash_events, paystubs | For each pair of reported balance dates: opening reported, plus known inflows, minus known outflows, expected closing, reported closing, and the gap. Events with an unconfirmed account are only used in a total cash pair. A missing input gives a Needs message, never a guess. |  |  |  | target of tile links |  |  |
| cs-gap | Gap per account | card | drift_all | Every account with two or more reported balances: bank accounts use expected = opening + known inflows - known outflows; cards use expected = opening + loaded charges - known payments, so a card balance above expected is unexplained spending. Weekly check points line up all accounts on the same date. ATM withdrawals count as a known outflow and are listed as cash spending not tracked. A trend shows only with 3 or more check points. |  |  |  | target of tile links |  | Needs: two reported balances on one account |
| card-drift-all | Every account, weekly check points | card | drift_all | Every account with two or more reported balances: bank accounts use expected = opening + known inflows - known outflows; cards use expected = opening + loaded charges - known payments, so a card balance above expected is unexplained spending. Weekly check points line up all accounts on the same date. ATM withdrawals count as a known outflow and are listed as cash spending not tracked. A trend shows only with 3 or more check points. |  |  |  | target of tile links |  |  |

### cash: tiles

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| cash-total | Cash, savings and checking | Cash, savings and checking | account_calc, balance_log | Sum of the latest reported cash balances. | USD | Source: the balances below. As-of dates in About this |  | cs-balhist (Cash balance over time) |  | hidden until data exists |
| cash-sav | Savings account | Savings account | balance_log, assets_other | Latest reported balance of the savings account row. | USD | Source: example As of DATE. |  | cs-balhist (Cash balance over time) |  | hidden until data exists |
| cash-chk | Checking account | Checking account | balance_log, assets_other | Latest reported balance of the checking account row. | USD | Source: example As of DATE. |  | cs-balhist (Cash balance over time) |  | hidden until data exists |
| sg-saved | Total saved for goals | Total saved for goals | goals_calc, savings_goals | Sum of saved_so_far over active goals. | USD |  |  | sg-goals (Goal list with forecasts) |  | hidden until data exists |
| sg-monthly | Going to goals each month | Going to goals each month | goals_calc, savings_goals | Sum of monthly_contribution over active goals. | USD |  |  | sg-goals (Goal list with forecasts) |  |  |
| sg-earmarked | Earmarked in Savings | Earmarked in Savings | goals_calc, savings_goals | Part of the goal money held in the savings account named on the goal. | USD |  |  | sg-goals (Goal list with forecasts) |  | hidden until data exists |
| sg-unassigned | Unassigned in Savings | Unassigned in Savings | goals_calc, savings_goals | Savings balance minus the earmarked goal money. | USD |  |  | sg-goals (Goal list with forecasts) |  | hidden until data exists |
| dr-latest | Latest dollar drift | Latest dollar drift | drift_calc | Reported closing balance minus expected closing balance (opening + known inflows - known outflows) for the latest period. | USD |  |  | dr-bridge (Latest period, step by step) |  | hidden until data exists |
| dr-points | Drift points so far | Drift check points | drift_calc | Number of drift check points so far; a trend shows from 3. | count |  |  | dr-chart (Drift over time) |  | hidden until data exists |
| dr-waiting | Periods waiting on you | Periods waiting on you | drift_calc | Periods that cannot be checked yet because a balance or event is missing. | count |  |  | t-drift (Every period (table)) |  | hidden until data exists |

### cash: charts

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| cs-balchart | Cash balance over time by account, with one month of essenti | Cash balance by account | runway_calc, balance_log | Reported balances over time per cash account, with one month of essentials marked. | USD |  |  | target of tile links |  |  |
| dr-bridge | Bridge from the opening reported balance to the reported clo | chart: Bridge from the opening reported balance to the re | drift_calc, balance_log, cash_events, paystubs | For each pair of reported balance dates: opening reported, plus known inflows, minus known outflows, expected closing, reported closing, and the gap. Events with an unconfirmed account are only used in a total cash pair. A missing input gives a Needs message, never a guess. |  |  |  | target of tile links |  |  |
| dr-chart | Dollar drift over time with a zero baseline | chart: Dollar drift over time with a zero baseline | drift_calc | For each pair of reported balance dates: opening reported, plus known inflows, minus known outflows, expected closing, reported closing, and the gap. Events with an unconfirmed account are only used in a total cash pair. A missing input gives a Needs message, never a guess. |  |  |  | target of tile links |  |  |

### cash: tables

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| t-drift |  | Every drift period | drift_calc, drift_all | One row per period: opening, inflows, outflows, expected, reported and gap. | USD |  |  | target of tile links |  |  |
| t-balances |  | table | balance_log |  |  |  |  | target of tile links |  |  |
| t-events |  | table | cash_events |  |  |  |  | target of tile links |  |  |

## Page: investments

### investments: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| inv-c-class | Allocation by asset class | card | by_class | Total value of all holdings by asset type. |  |  |  | target of tile links | Asset types come from each fund's row in the holdings data; a target-date fund is its own class because its stock and bond mix is inside. A fund without a type shows as Not yet classified. |  |
| inv-c-map | Holdings map | card | all_holdings | Every fund held, with its account, its share of that account, cost basis, gain or loss, return on cost where reported, and a small return history. |  |  |  | target of tile links | Each tile is one fund you hold, sized by dollars. |  |
| inv-c-funds | Funds | Funds in the first account | all_holdings, acct_a_holdings | One list of funds with value, share of the account, cost basis and gain or loss. | USD |  |  | target of tile links | The funds are one list. The export has no per-fund split between Traditional and Roth money, so the two balances above are shown for the account as a whole. The small line is the fund's return on cost |  |
| inv-c-scatter | Cost basis against value | card | all_holdings | Every fund held, with its account, its share of that account, cost basis, gain or loss, return on cost where reported, and a small return history. |  |  |  | target of tile links | Each dot is a fund in this account. Dots above the diagonal are worth more than you paid; dots below are worth less. Both axes are logarithmic so the small funds stay visible. Cost basis here resets w |  |
| inv-c-growth | Growth over time | card | perf_series | Return on cost for each holding at average cost and at each snapshot date. |  |  |  | target of tile links | Return on cost for each fund: average cost is N%, then each holdings export adds a point. The three best and three worst now are colored; the rest are gray. Send an export monthly to lengthen the line |  |
| inv-c-since | Since the plan start | card | plan_calc, price_growth, plan_summary |  |  |  |  | target of tile links | From the plan's own statement for the period shown in the tiles. Market growth on the statement and a gain or loss on the fund list can both be true: the fund list measures from each fund's current co |  |
| inv-c-src | Balance by source | card | acct_a_sources |  |  |  |  | target of tile links | Estimated split by source from the plan's sources screen, scaled to the latest fund total. A fresh sources screen makes this exact. |  |
| inv-c-hi | Funds | card | all_holdings | Every fund held, with its account, its share of that account, cost basis, gain or loss, return on cost where reported, and a small return history. |  |  |  | target of tile links | Each bar is the fund's share of this account. Performance appears here once a statement or export with cost basis is added. |  |

### investments: tiles

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| inv-total | All holdings, total | All holdings, total | holdings_calc, all_holdings | Sum of fund values across both held accounts. | USD |  |  | inv-class (Allocation by asset class) |  |  |
| inv-funds | Funds held | Funds held | all_holdings | Number of distinct funds in the holdings lists. | count |  |  | inv-tree (Holdings map) |  |  |
| inv-trad | Traditional (pre-tax) | Traditional (pre-tax) balance | account_calc, acct_a_sources | Balance of the first account with tax_type Pre-tax. | USD |  |  | inv-src (Balance by source) |  |  |
| inv-roth | Roth | Roth balance | account_calc, acct_a_sources | Balance of the first account with tax_type Roth. | USD |  |  | inv-src (Balance by source) |  |  |
| inv-gain | Gain since current cost basis | Gain since current cost basis | holdings_calc, acct_a_holdings | Sum of value minus cost basis x quantity over the first account funds. | USD |  |  | inv-scatter (Cost basis against value) |  |  |
| inv-growth | Market growth since start | Market growth since the plan statement start | plan_calc, plan_summary | The market growth line of plan_summary. | USD |  |  | inv-planwf (Where the balance came from) |  | hidden until data exists |
| plan-return | Plan's own return since start | Plan's own return | plan_calc, plan_stats | personal_return_pct from the plan statement totals. | percent |  |  | inv-planwf (Where the balance came from) |  | hidden until data exists |
| plan-yearly | Per year, about | Per year, about | plan_calc, plan_stats | The time-weighted return converted to a yearly equivalent. | percent |  |  | inv-planwf (Where the balance came from) |  | hidden until data exists |
| plan-share | Share of balance that is market growth | Share of balance that is market growth | plan_calc, plan_stats | Market growth / ending balance x 100. | percent |  |  | inv-planwf (Where the balance came from) |  | hidden until data exists |
| plan-bal | Balance on the last statement | Balance on the statement date | plan_calc, plan_stats | ending_balance of the plan statement totals. | USD |  |  | inv-planwf (Where the balance came from) |  | hidden until data exists |
| inv-hi-bal | Balance | Balance of the second account | holdings_calc, acct_b_holdings | Sum of fund values in the second held account. | USD |  |  | inv-hi (Funds in the second 401(k)) |  | hidden until data exists |
| inv-hi-funds | Funds held | Funds held in the second account | all_holdings, acct_b_holdings | Count of fund rows in the second account. | count |  |  | inv-hi (Funds in the second 401(k)) |  | hidden until data exists |

### investments: charts

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| inv-class | Holdings by asset class | chart: Holdings by asset class | by_class | Total value of all holdings by asset type. |  |  |  | target of tile links |  |  |
| inv-tree | Treemap of holdings by account | chart: Treemap of holdings by account | all_holdings | Every fund held, with its account, its share of that account, cost basis, gain or loss, return on cost where reported, and a small return history. |  |  |  | target of tile links |  |  |
| inv-scatter | Scatter of cost basis against current value for each fund he | chart: Scatter of cost basis against current value for ea | all_holdings | Every fund held, with its account, its share of that account, cost basis, gain or loss, return on cost where reported, and a small return history. |  |  |  | target of tile links |  |  |
| growth | Return on cost over time for each holding | chart: Return on cost over time for each holding | perf_series | Return on cost for each holding at average cost and at each snapshot date. |  |  |  | target of tile links |  |  |
| inv-planwf | Where the balance came from | chart: Where the balance came from | plan_calc, plan_summary | Plan statement totals with the yearly equivalent of the time-weighted return and the share of the ending balance that is market growth. |  |  |  | target of tile links |  |  |
| inv-slope | Fund price growth since DATE20, indexed to N | chart: Fund price growth since DATE20, indexed to N | price_growth | Price change and yearly equivalent from the start date to the end date for each fund with a start price. |  |  |  | target of tile links |  |  |
| inv-src | Balance by source | chart: Balance by source | acct_a_sources |  |  |  |  | target of tile links |  |  |

### investments: tables

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| t-all |  | Every fund | all_holdings | Value, cost basis, gain or loss and a small return history for each fund. | USD |  |  | target of tile links |  |  |
| t-src |  | table | acct_a_sources |  |  |  |  | target of tile links |  |  |
| t-hi |  | Funds in the second account | all_holdings, acct_b_holdings | Fund, short name, asset class and value. | USD |  |  | target of tile links |  |  |

## Page: retirement

### retirement: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| ret-head | Balance at N | Balance at the target age | retire_model | Projection FV = B0 (1+r)^n + C ((1+r)^n - 1) / r at the base return, today's contributions, in today's dollars. The slider reads any age. | USD |  |  | target of tile links | At age N, N% a year after inflation, in today's dollars. Drag the slider to read any age, or tap the chart below. |  |
| hm-ret | Retirement at N | Retirement at the target age | pace_calc, age60_calc | Bar of the balance at the target age against the high goal, with the goal range marked. | USD |  |  | target of tile links | Bar full means the $N high goal; the goal range is $N to $N. Base case, N% a year after inflation, in today's dollars. | Needs: retirement plan, assumptions and balances |
| rt-plan | N(k) plan year | card | plan_year_calc | Year to date 401(k) from the stubs in the plan year against the limit for that year (prior year row labeled estimate when the year is not published). Remaining paychecks come from the pay calendar (first pay date and schedule), never from the number of stubs. Needed per paycheck = room left / remaining paychecks. Quarterly bars: even pace to the limit against what the stubs show. |  |  | Behind | target of tile links |  | Needs: a year to date N(k) column from the stubs to tie this total; pay dates. |
| pct-card | N(k) as a percent of salary | card | pct_calc, retire_model |  |  |  | Limit reached | target of tile links | Using this year's limit of $N (N% of pay). Anything over N% hits the cap. Room left under it at this rate: $N a year.Take-home change is an estimate: the pre-tax dollars times (N minus N percent), you |  |
| ret-main | What you will have at N, depending on what you contribute | card | retire_model | Everything the retirement chart needs: starting balance now, ages, goals, return paths from the assumptions file, each account contribution today and with every account at its limit (maxed), and the balance at the target age for both on all three paths. Contributions at year end, before tax, in today's dollars. |  |  |  | target of tile links | Set what goes in each paycheck or month and the chart shows the balance by age. The lines start at what your retirement accounts hold now. The two reference lines (today and every account at the limit |  |
| ret-c-limits | What you put in against the limits | card | contrib_calc, retire_model |  |  |  |  | target of tile links | Each row fills toward the yearly legal limit for that account. The solid bar is what your pay stubs add now (employer part lighter), the diamond is your current setting above, and the tick is the limi |  |
| ret-c-pace | Pace to your goal | card | pace_steps_calc | Base path balance at the target age on today's pay stubs as a percent of your low goal (the existing pace table), with steps at 25, 50, 75 and 100 percent. |  |  |  | target of tile links | Each step is a contribution level. The bar shows the base path balance at N as a share of the low goal; the table below gives all three return paths. | Needs: goal_low and the pace table |
| ret-c-paths | Balance at N, all three return paths | card | pace_calc | Balance at the target age for today and with every account at its limit (maxed) on each return path, and whether the base path reaches the low goal. |  |  |  | target of tile links | The HSA balance is not in the starting balance because it is uninvested cash for medical expenses, savings and checking cash are left out because they are not retirement accounts, and the mega backdoo |  |

### retirement: tiles

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| ret-short | Short of the $N low goal today | Short of the low goal | pace_calc, retire_model | Low goal minus the base-path balance at the target age with today's contributions (0 when reached). | USD |  |  | ret-chart (What you will have at 60) |  | hidden until data exists |
| ret-extra | Extra per month to max every account | Extra per month to max every account | contrib_calc | (Sum of account limits minus sum of current yearly contributions) / 12. | USD |  |  | ret-limits (What you put in against the limits) |  | hidden until data exists |
| ret-k401now | N(k) per paycheck now | 401(k) per paycheck now | contrib_calc, paystubs | Latest stub 401(k) deduction per paycheck. | USD |  |  | ret-limits (What you put in against the limits) |  | hidden until data exists |
| ret-k401max | Per paycheck to max the N(k) | Per paycheck to max the 401(k) | contrib_calc, contribution_limits | Employee deferral limit / paychecks per year. | USD |  |  | rt-plan (401(k) plan year) |  | hidden until data exists |
| ret-need | Total saving per year to reach the low goal | Total saving per year to reach the low goal | need_calc | Yearly contribution C so that balance now plus C reaches the low goal at the target age at the base return. | USD |  |  | rt-pace (Pace to your own goal) |  | hidden until data exists |

### retirement: charts

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| ret-chart | Projected retirement balance by age for today's pay stubs, e | Balance by age | retire_model | Three real return paths for today's stub contributions and for every account at its limit, the goal range shaded, the balance at the target age marked. This is the one retirement chart. | USD |  |  | target of tile links |  |  |

### retirement: tables

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| t-pace |  | table | pace_calc | Balance at the target age for today and with every account at its limit (maxed) on each return path, and whether the base path reaches the low goal. |  |  |  | target of tile links |  |  |

## Page: progress

### progress: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| hm-cover | How complete the picture is | card | coverage_calc, milestones_calc |  |  |  |  | target of tile links |  |  |
| hm-recap | Weekly recap | card | recap_calc, checkins_calc |  |  |  |  | pg-weeks (Past weeks) |  |  |
| (no id) | Coverage | Three coverage rings | coverage_calc, milestones_calc | Picture, Scores and Explained, each with its percent and count. | percent |  |  | target of tile links |  |  |
| pg-quests | Quests | Quests | quests_calc | Groups of tasks and requests that unlock one thing; a quest is done only when its tasks are Done and verified. Counts only, no task rows (tasks live on the Tasks page). | count |  | Needs data; N of N done | target of tile links | N quests, each a group of tasks and requests that unlocks one thing. A task counts as done only when it is Done and verified. The tasks themselves are on the Tasks page.Everything on this page is comp |  |
| (no id) | Milestones | Milestones | milestones_calc | Derived facts with first-seen dates: debt paid percent steps, months of cash, net worth steps. |  |  | Earned | target of tile links |  |  |
| (no id) | Checks run on this page | Check results | checks_calc, checks_more_calc | Pass, Needs or Fail for each correlation check (CORRELATIONS.md). |  |  | Pass; Needs data | target of tile links |  |  |
| pg-weeks | Past weeks | Past weeks | recap_calc, checkins_calc | Recap of each past check-in week. |  |  | Clean check; Checked in | target of tile links |  |  |
| (no id) | Badges | Badges | badges_calc, milestones_calc | Durable facts that are true in the data; kept with a first-seen date. |  |  |  | target of tile links |  |  |
| (no id) | Seasons | Seasons | seasons_calc | Quarter goals, all derived from the data. |  |  | in progress; not scored | target of tile links |  |  |
| rt-ladder | Where you stand at N | card | age_calc | Each metric in three percent forms: percent of guideline (value / guideline x 100, lower is better metrics use the limit), percent of typical (value / peer median x 100) and percentile rank (linear between the published p25, p50, p75, p90, p95 rows). A form without a sourced row says Needs data. Ratios are never shown as multiples. Every figure keeps its source, data year and dollar year. |  |  | NEXT; NOW | target of tile links |  | Needs: age, salary, retirement balance and the guideline rows / Needs: the age ladder (age, salary, retirement balance) |
| hm-ladder | Where you stand at N | card | age_calc | Each metric in three percent forms: percent of guideline (value / guideline x 100, lower is better metrics use the limit), percent of typical (value / peer median x 100) and percentile rank (linear between the published p25, p50, p75, p90, p95 rows). A form without a sourced row says Needs data. Ratios are never shown as multiples. Every figure keeps its source, data year and dollar year. |  |  | NEXT; NOW | target of tile links |  | Needs: age, salary, retirement balance and the guideline rows |
| hm-age | Percent of guideline, typical, and rank | card | age_calc | Each metric in three percent forms: percent of guideline (value / guideline x 100, lower is better metrics use the limit), percent of typical (value / peer median x 100) and percentile rank (linear between the published p25, p50, p75, p90, p95 rows). A form without a sourced row says Needs data. Ratios are never shown as multiples. Every figure keeps its source, data year and dollar year. |  |  |  | target of tile links |  | Needs: your age and the benchmarks dataset |
| hm-moves | What would move you up one rung | card | moves_calc | Three moves computed from the same calcs as the rest of the page: the 401(k) at its limit (months to the next ladder rung at today's 401(k) pace against at the limit, before growth), the next payment in the payoff plan (interest avoided a year = extra to that debt x its rate), and cash to 3 months of essentials. Score effects come from the score what-ifs, which rerun the score on changed copies of the data. |  |  |  | target of tile links |  | Needs: N(k) pace, the limit and the ladder / Needs: debts, bills and a pay stub |
| rt-age | You and people your age | card | age_calc, age_benchmarks | Each metric in three percent forms: percent of guideline (value / guideline x 100, lower is better metrics use the limit), percent of typical (value / peer median x 100) and percentile rank (linear between the published p25, p50, p75, p90, p95 rows). A form without a sourced row says Needs data. Ratios are never shown as multiples. Every figure keeps its source, data year and dollar year. |  |  |  | target of tile links |  | Needs: age and the benchmarks dataset |
| rt-bench | Benchmarks and sources | card | bench_age_calc, age_calc |  |  |  |  | target of tile links |  | Needs: age and salary |
| (no id) | Benchmarks for N to N and guidelines | Benchmark table | bench_age_calc, age_benchmarks | Every benchmark used for your age band with source, year and confidence. |  |  |  | target of tile links |  | hidden until data exists |

### progress: tiles

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| hm-t2-runway | Cash runway | Cash runway | runway_calc | Liquid cash / essentials a month (essential bills + every debt payment). | months |  |  | cs-runway (Cash runway) |  | hidden until data exists |
| hm-t2-pace | Retirement vs the age guideline at N | Retirement vs the age guideline | bench_age_calc, age_calc, pace_calc | Retirement balance as a percent of the published age guideline at your salary. | percent |  |  | rt-ladder (Age ladder and age 60 pace) |  | hidden until data exists |

## Page: tasks

### tasks: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| tq-card | Task queue | card | next_move, tasks | One move, ranked: (a) a failed check that blocks a core number; (b) the open task that unlocks the most score weight per unit of effort (effort not set counts as 1); (c) the first debt in the payoff plan when the surplus is positive; (d) the oldest stale core dataset; (e) nothing urgent. |  |  | To do; Open; Done, verified DATE | target of tile links |  | Needs: the task list |
| nd-card | What the cards are waiting for | card | needs_calc | Every Needs line the calcs give, one row each with the data request (data_queue) and task that cover it: matched by the request needs_key when set, else by the subject and field words in the request text. Categories answered no are left out. A Needs row without a request is listed so Claude can add one; a request whose need is gone is listed as ready to mark Provided. |  |  |  | target of tile links | Every Needs line on the page, with the data request and task that cover it. A line without a request is one Claude still has to add; a request whose need is gone is ready to mark Provided. |  |
| pf-card | What you have | card | profile_calc | One row per category of debt, bill, account and retirement plan: your answer (yes, no or unknown), how many rows the data holds, and what follows. No leaves the category out; yes with no rows asks for the details; unknown with no rows is a question to ask in Claude. Rows in the data count as yes. A no with rows on file is a conflict to resolve. |  |  |  | target of tile links | Nothing is assumed to exist. Each kind of debt, bill and account is a question: yes, no or not asked yet. Rows on file count as yes; a no leaves that kind out everywhere. |  |
| (no id) | What to add next | card | data_queue |  |  |  |  | target of tile links | Send a downloaded statement (PDF or CSV) when you can, or paste the values in the chat, and they go into the right place. A screenshot still works: its values are used right away and the statement is  |  |
| (no id) | Update routine | Update routine text | data_queue | A short list of what to send and when. Static text. |  |  |  | target of tile links | Each time you report a savings balance, send checking too so dollar drift can be calculated. Monthly: send holdings exports and pay stubs, add a row to the net worth history, update debt balances from |  |

### tasks: tables

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| t-queue |  | table | data_queue |  |  |  |  | target of tile links |  |  |

## Page: pay

### pay: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| (no id) | Your paycheck, stub by stub | card | paycheck_calc, stubcheck_calc, paystubs |  |  |  |  | target of tile links | Each column is one paycheck from your pay stubs, split into take-home, taxes and what comes out for saving and benefits. Dashed lines mark the first stub with a N(k) deduction and the first with a wit |  |
| pb-card | Year to date on your stubs | card | paystub_baseline | Year-to-date totals printed on each pay stub: the tax rate, the share kept as take-home, and what was saved (401(k) plus HSA, both parts), per pay date. The last row is the latest. |  |  |  | target of tile links | The latest pay stub prints year-to-date totals. Tax rate is year-to-date taxes over year-to-date gross pay. Kept as take-home is year-to-date net pay over gross. Saved adds your N(k), your HSA part an |  |
| (no id) | Where your gross pay goes each month | card | pay_flow | Links for the pay flow: gross monthly pay into debt payments, bills, your saving from pay and everything else, then into each debt, bill category and saving account. |  |  |  | target of tile links | Gross monthly base pay splits into debt payments, bills, your own saving from the paycheck, and everything else (taxes, health benefits and anything not tracked yet). Debts with no payment entered are |  |
| (no id) | Income used | card | income_pay |  |  |  |  | target of tile links |  |  |

### pay: tiles

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| pc-net | Take-home per paycheck | Take-home per paycheck | paycheck_calc | net_pay on the latest stub. | USD |  |  | pc-chart (Each paycheck split) |  | hidden until data exists |
| pc-month | Take-home per month | Take-home per month | paycheck_calc | Latest net pay x paychecks per year / 12 (an estimate). | USD |  |  | pc-chart (Each paycheck split) |  |  |
| pc-tax | Taxes withheld | Taxes withheld | paycheck_calc | Taxes / gross on the latest stub. | percent |  |  | pc-chart (Each paycheck split) |  | hidden until data exists |
| pc-saved | Saved from each check | Saved from each check | paycheck_calc | 401(k) + HSA (you and employer) on the latest stub. | USD |  |  | pc-chart (Each paycheck split) |  |  |
| pc-count | Paychecks on file | Paychecks on file | paystubs | Number of pay stub rows. | count |  |  | pc-stubs (Pay stubs table) |  |  |
| (no id) | Tax rate so far | Year-to-date tax rate | paystub_baseline | ytd_taxes / ytd_gross on the latest stub. | percent |  |  | none: it is a stub-printed year-to-date figure; the Year to date card beside it shows the series |  | hidden until data exists |
| (no id) | Kept as take-home | Share kept as take-home | paystub_baseline | ytd_net / ytd_gross on the latest stub. | percent |  |  | none: it is a stub-printed year-to-date figure; the Year to date card beside it shows the series |  | hidden until data exists |
| (no id) | Saved so far | Saved year to date | paystub_baseline | ytd 401(k) + HSA (you and employer) on the latest stub. | USD |  |  | none: it is a stub-printed year-to-date figure; the Year to date card beside it shows the series |  | hidden until data exists |

### pay: charts

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| pc-chart | Each paycheck split into take-home, taxes and deductions | chart: Each paycheck split into take-home, taxes and dedu | paystubs |  |  |  |  | target of tile links |  |  |
| cf-sankey | Where gross monthly pay goes | chart: Where gross monthly pay goes | pay_flow | Links for the pay flow: gross monthly pay into debt payments, bills, your saving from pay and everything else, then into each debt, bill category and saving account. |  |  |  | target of tile links |  |  |

### pay: tables

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| t-income |  | table | income_pay |  |  |  |  | target of tile links |  |  |

## Page: data

### data: cards

| Tile id or key | Label | Key number or chart (example shape) | Calcs and datasets | Formula in words | Unit | Source on tap | Chips | Links to | About this fold | Needs when empty |
|---|---|---|---|---|---|---|---|---|---|---|
| bk-card | Backup | Backup | (static; last backup date from the per-user db doc and browser storage) | Buttons that make the backup JSON and the rebuild kit, and the last confirmed backup date (kept in the per-user db document and the browser). |  |  |  | target of tile links | Saves every dataset on this dashboard, with its title, description and source, to a file you keep. A backup counts only when this card says Saved. No account numbers are stored in these datasets. |  |
| asof-card | As-of dates and sources | card | assets_other |  |  |  | DATE | target of tile links | Each figure on the other screens carries its own as-of date or source note. This list gathers the latest date for each kind of data. | Needs: assets file / Needs: paystubs / Needs: holding snapshots / Needs: networth history / Needs: balance log |
| st-card | Self-test | Self-test | (static text) | Runs the invariant rules (page/invariants.js) on the data the page has loaded. |  |  |  | target of tile links | Runs the rule checks on the data this page has loaded right now: every calc ran, totals add up, shares add to N, a Needs line never shows a number. Nothing is saved or sent. |  |


## Tiles without a link

| Page | Tile | Why it has no link |
|---|---|---|
| home | Debts missing a payment | the debts are listed in the Debts missing a payment card on the Debt page |
| home | Net if sold | a range, not a single series; the house card shows the inputs |
| debt | Pay from the Fri DATE paycheck | the paycheck plan card holds the list of what is paid from it |
| debt | Left to keep | shown with the paycheck plan it comes from |
| debt | Gain since purchase | the Bought for tile and the value tile beside it hold the two inputs |
| bills | Left over this month | the same number is the This month box on the Cash runway card |
| pay | Tax rate so far | it is a stub-printed year-to-date figure; the Year to date card beside it shows the series |
| pay | Kept as take-home | it is a stub-printed year-to-date figure; the Year to date card beside it shows the series |
| pay | Saved so far | it is a stub-printed year-to-date figure; the Year to date card beside it shows the series |
