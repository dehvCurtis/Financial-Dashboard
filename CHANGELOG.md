# CHANGELOG

Structure only: datasets, fields, screens, calculations, rules and routines. No entry carries anyone's balances, rates, names or figures. Examples use placeholders.

Two version series: feature versions (v1.1 and up) and page stages (stage numbers up to 6i) describe the dashboard; the bundle version (VERSION) numbers this package. A feature version never means a document version.

How to use this file: a fresh chat reads the entries after the version its dashboard already has and builds them in order (PROMPTS.md, prompt 16).

## Bundle 1.0.0 (page stage 6i)

First standalone bundle: documents, templates, invented demo data with screenshots, code base zip, and the checks that tie them together (tools/check_docs.js, tools/verify_backup.js). Test totals for the code base are in REPORT.md.

### Review fixes after 1.0.0 (security and completeness)

Security: restore never publishes the page files from a kit; zip reading has size and name limits and the verify tool fails closed on hostile input; the test harness escapes data in its inline script; tools that empty an output folder refuse protected folders; .gitignore added; scheduled tasks no longer suggest automatic approval; SECURITY.md and SECURITY_REVIEW.md added; a new injection test (tests/inject.js) runs the real page on data full of markup and scripts. Completeness: AGENT_PROMPT.md and a first message; 18 prompts; a tax form row in INTAKE_GUIDE.md; the Google Drive table; REPORT.md and COMPLETENESS_REVIEW.md; designs of features that are not built marked as such; dataset descriptions made generic; the tile inventory waits for the page to settle (it was flaky under load); the dead code and size step added to the loop.

## Changes from v3.0 to page stage 6i (summary)

The exact order of the intermediate stages is not recorded in this bundle; the list is grouped by theme. Stage 6g and stage 6i are the two stage ids that carry a recorded result.

- Pages split by topic (Home, Debt, Bills, Cash, Investments, Retirement, Progress, Tasks, Pay, Data and backup) over one calc layer, with the tile link rule (ARCHITECTURE.md section 13).
- Statement intake datasets (card, bank, bill and mortgage statements, bank flows), drift for every account, and profile questions so no category is assumed (QUESTIONS.md).
- Standing rule: every card shows one key number or chart, short labels and chips; explanations move into a closed About this fold with source and how it is worked out; warnings and decision items stay visible; fold state is kept across page switches; tests check that the fold exists and warnings are visible, not their wording.
- Debt page audit and trim; the Cash cards show name and number only, with the as-of date and long source text in the fold; cash runway as a half-circle gauge against a target with the "essentials" wording; the bills list lives only on Bills.
- Stage 6g: the payoff trail records a first balance so it stops saying Needs; paid percent is clamped at zero while the balance is above the first recorded one.
- Retirement and Investments de-duplicated to one chart per fact; two clearly separated Investments sections (the current plan with holdings and returns, and the plan the money is moving to).
- Stage 6i: Limit reached chip on the 401(k) percent card (the amount stops changing at the limit, the chip says so, with a line that the match and next year's limit are separate); task rows removed from Progress, replaced by a count chip that links to Tasks; About this folds on Investments, Retirement, Pay and Data cards; paystub_baseline calc and invariant I34 (year to date from stubs); backup schema mo-backup-2 with the kit, manifest hashes and verify tool. Tests at 6i on the reference data: 500 pass, 0 fail.

## Feature versions

### v1.1 Mobile layout
Mobile first, bottom bar with an icon above each label, active tab highlighted. The bar stays at the bottom whether or not the page scrolls; because the host does not make `position: fixed` reliable it is a measured app shell (bar as the last row of a flex column). Non-home pages start hidden. Triple tap on the page title shows a debug readout (mode, heights, viewport, db, contract). Accordions on the denser pages. The tab set changed later to Home, Debt, Bills, Cash and More (ARCHITECTURE.md 9.2).

### v1.2 Save everything the user sends
Every number or document goes to a dataset with source and as-of date. Pay stubs save every line (gross, federal, state, FICA, 401(k), HSA, employer HSA, benefits, garnishment, net, year to date). Statements save every transaction. Corrections overwrite the wrong entry. Never store account numbers, SSNs, routing numbers, loan ids, trace numbers or addresses; never keep the original files. Replaces "never overwrite old balances": history datasets keep dated rows, errors are fixed in place.

### v1.3 card_transactions and statement ingestion
Dataset card_transactions (txn_id, date, merchant, amount, category, foreign, card, statement_close, notes). On a card statement: read every transaction, add the closing balance to balance_log, record payments as cash_events, flag charges the user may not recognize and repeated or foreign charges, ask which account paid. Bank statements use the same flow.

### v1.4 401(k) as a percent of salary
A card with a slider and number input (0 to 15 percent in 0.5 steps): per year, per paycheck, estimated take-home drop per paycheck, balance at the target age. The cap is the annual limit divided by salary; warn above it. Chart 1: balance over time for today's percent, the chosen percent, reference rates and the cap, with the goal range shaded. Chart 2: a ladder of percents. Take-home cost = pre-tax dollars x (1 minus combined marginal rate), labelled an estimate. Stage 6i adds the Limit reached chip.

### v1.5 Backup
Backup button on the Data and backup page, last backup date shown, due on the 1st of each month. Superseded in detail by v2.4 and schema mo-backup-2 (BACKUP_FORMAT.md).

### v1.6 Debt data for cards
Card liabilities carry type, rate, minimum and balance as-of date. When the minimum is missing, DTI is shown two ways: entered payments only, and with a labelled estimate of 4 percent of the balance. Payoff rank comes from data, never hard coded. A rate from an old chat is labelled "confirm on statement".

### v1.7 Dollar drift
Per account and in total: reported balance minus expected balance. Expected = last known balance plus known inflows minus known outflows since then. Covers checking, savings, HSA cash and each card. Shown as "unexplained spending" in plain words; never invent a cause. Check points are saved in drift_history; recomputed when new data arrives so the gap can be seen shrinking. A trend shows only with 3 or more check points. ATM withdrawals are a known outflow listed as "cash spending not tracked" until the user says what the cash was for.

### v1.8 Minimum payment requirement
Every card and loan needs a minimum taken from a statement, not guessed. Until then DTI shows two figures and a task asks for it. The exact question: the minimum payment due on the latest statement, read from the statement or a screenshot.

### v1.9 Standard task queue
A tasks dataset (id, task, area, status, note) shown on its own page and summarized on Home. Every gap becomes a task in the same turn it is found, with a note on why it matters and which metric it improves. Tasks start with a verb, are de-duplicated by topic, and are Done only when the data is entered and dependent numbers recomputed. Scores list the tasks that would raise their coverage.

### v2.0 Facelift and architecture v2
Facelift, not rebuild: every existing chart, table and correlation stays and moves into the new design. Before and after regression on frozen data, dollar values to the cent. Progress layer phase 1: Next best move, coverage rings (Picture, Scores, Explained) that print a percent and a count, derived milestones with date and evidence. No levels, points, streaks or animation. Task verification fields (verified_on, checks_run, metric, metric_before, metric_after, effort) and "Done (unverified)". Style fixes: darker light-mode text colours (4.5 to 1), a real filled primary button, a reduced-motion rule, direct labels on low-contrast chart colours.

### v2.1 Debt payoff strategy
New liability fields (days_past_due, late_fee, promo_end_date, secured, rate_type, prepayment_penalty, credit_limit, last_late_date; blank means Needs) and a payoff_settings dataset. Smart order: past due first, then late in the last 90 days, then highest effective rate; special-case notes for secured, variable, tax debt, expiring promos, prepayment penalties and high utilization ("confirm on the statement or notice"). Next payment recommendation, extra slider starting at the computed surplus, plan comparison against a minimums-only reference. Rules: monthly rate = rate/1200; months to payoff n = -ln(1 - B*r/p)/ln(1+r), B/p at zero rate; month by month simulation. Check C17.

### v2.2 Age statistics and benchmarks
Datasets age_benchmarks and age_metrics_config. Every statistic in three percent forms (percent of guideline, percent of typical, percentile rank), "Needs data" when the source lacks the row, never "x" multiples, source and data year and dollar year on every figure, median and percentile first, label the peer group, never a verdict from one number. Check C18.

### v2.3 Scores
Financial and Risk scores from scores_config; "based on N of M components"; range line; "Not enough data" below 60 percent weight coverage; what would raise it; Show the math. scores_history and insurance datasets. No trend until 6 snapshots over 60 days. SCORING.md.

### v2.4 Data model rule and monthly backup
Each number stored once as the latest confirmed value with source and as-of date; unconfirmed values labelled estimate or confirm on statement; only history datasets keep dated rows. Originals are kept by the user in their own folder.

### v2.5 Correlation register
Every check has a named executor: page function, Claude at ingestion, verifier agent, or the test harness. A check only Claude runs must leave a trace in the data. CORRELATIONS.md.

### v2.6 Plan-year contribution tile
Contribution limits keyed by year; the tile reads the current plan year or the prior year labelled an estimate; the chosen percent is stored per plan year; year to date from stubs against the limit, quarterly bars, on track or behind with the per-paycheck amount to reach the limit.

### v3.0 Facelift, task system, mortgage annual section, cleanup
Home with both score rings, four tiles, payoff summary, task count, Next best move, coverage rings, age ladder; Debt with net worth, payoff plan, loan to value and DTI; Cash with runway, this month, drift gap per account; Retirement with the balance at target age, ladder, percent card, plan-year tile; Progress with quests, weeks, milestones, badges. Payoff smart order: minimums first; extra by late rank, then effective rate (a promo debt by the rate it will carry), then smaller balance; extra = take-home minus bills minus everyday spending minus minimums; the mortgage keeps its schedule and is left out of the debt free date. Loan to value: first lien / value and combined. Quests are groups of tasks that unlock one thing; no points, levels or streaks.

## Future entries

Add above "Bundle 1.0.0" in this form: version or stage, one line title, structure changes only, the tests added, the docs regenerated. Mark an entry PENDING until the user confirms they have it.
