# QUESTIONS: what Claude asks, in order, and where each answer goes

Two kinds of question exist. The profile questions are in the page code (`PROFILE_CATS` in page/js/65_calcs_profile_needs.js): the table below is generated from that code, so it cannot drift. The interview topics are the first-run script in ARCHITECTURE.md section 15, summarized here.

## Rules for asking

1. Ask in plain words, one topic per turn, a few questions at a time. Give an example of where to find the answer.
2. Never assume a debt, bill or account exists. The profile question comes first: "Do you have a mortgage?" The answer is yes, no or unknown.
3. A no is final. The category is left out of every calc and is not asked again. A no with rows in the data is a conflict (check C28) and is shown, not hidden.
4. A yes with no rows shows a Needs line and becomes a task and a data request. An unknown with no rows is a question for Claude to ask in chat. Rows in the data count as yes.
5. Accept a screenshot, PDF, CSV export or pasted text. Read every attachment fully, read the numbers back, then save with source and as-of date.
6. After each topic say in one sentence what it unlocks.
7. Never ask for account numbers, SSN, routing numbers, loan ids or street addresses.

## How an answer is stored

Dataset `profile_answers`, one row per question: `question_id` (the id in the table), `category`, `answer` (yes, no or unknown), `count` (rows the user says exist, optional), `asked_at` (date YYYY-MM-DD, optional), `note`. Rows already in liabilities, bills, assets_other or contributions count as yes without an answer row.

## Profile questions (28)

The question is always "Do you have X?" where X is the phrase in column "Asked in Claude as". Order is the order the page checks them.

| # | Question id | Category | Kind | Asked in Claude as | Counts as yes when the data has | Unanswered: the Needs text | Feeds |
|---|---|---|---|---|---|---|---|
| 1 | debt_mortgage | Mortgage | debt | Do you have a mortgage? | a liabilities row with type Mortgage | Needs: your mortgage details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 2 | debt_heloc | Home equity line | debt | Do you have a home equity line? | a liabilities row with type HELOC | Needs: your home equity line details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 3 | debt_card | Credit card | debt | Do you have any credit cards? | a liabilities row whose type contains "credit card" | Needs: your credit card details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 4 | debt_auto | Auto loan | debt | Do you have an auto loan? | a liabilities row with type Auto | Needs: your auto loan details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 5 | debt_student | Student loan | debt | Do you have any student loans? | a liabilities row whose type contains "student" | Needs: your student loan details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 6 | debt_personal | Personal loan | debt | Do you have a personal loan? | a liabilities row with type Personal | Needs: your personal loan details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 7 | debt_medical | Medical bill | debt | Do you have any medical bills owed? | a liabilities row with type Medical | Needs: your medical bill details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 8 | debt_tax | Tax owed | debt | Do you have any tax owed? | a liabilities row with type Tax | Needs: your tax owed details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 9 | debt_support | Support order | debt | Do you have a support order? | a liabilities row with type Support | Needs: your support order details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 10 | bill_housing | Rent or housing bill | bill | Do you have rent or another housing bill? | a bills row with category Housing | Needs: your rent or housing bill details (name, amount a month and due day) | profile_calc, needs_calc |
| 11 | bill_utilities | Utilities | bill | Do you have utility bills? | a bills row with category Utilities | Needs: your utilities details (name, amount a month and due day) | profile_calc, needs_calc |
| 12 | bill_phone | Phone | bill | Do you have a phone bill? | a bills row with category Phone | Needs: your phone details (name, amount a month and due day) | profile_calc, needs_calc |
| 13 | bill_insurance | Insurance | bill | Do you have insurance bills? | a bills row with category Insurance | Needs: your insurance details (name, amount a month and due day) | profile_calc, needs_calc |
| 14 | bill_transport | Transportation | bill | Do you have transportation costs? | a bills row with category Transportation | Needs: your transportation details (name, amount a month and due day) | profile_calc, needs_calc |
| 15 | bill_food | Food | bill | Do you have a food budget? | a bills row with category Food | Needs: your food details (name, amount a month and due day) | profile_calc, needs_calc |
| 16 | bill_pets | Pets | bill | Do you have pet costs? | a bills row with category Pets | Needs: your pets details (name, amount a month and due day) | profile_calc, needs_calc |
| 17 | bill_childcare | Childcare | bill | Do you have childcare costs? | a bills row whose category contains "child" | Needs: your childcare details (name, amount a month and due day) | profile_calc, needs_calc |
| 18 | bill_subscriptions | Subscriptions | bill | Do you have subscriptions? | a bills row whose category contains subscri, streaming or entertain | Needs: your subscriptions details (name, amount a month and due day) | profile_calc, needs_calc |
| 19 | acct_checking | Checking account | account | Do you have a checking account? | an assets_other row of type Cash whose name contains "check" | Needs: your checking account details (balance and as-of date) | profile_calc, needs_calc |
| 20 | acct_savings | Savings account | account | Do you have a savings account? | an assets_other row of type Cash whose name contains "saving" | Needs: your savings account details (balance and as-of date) | profile_calc, needs_calc |
| 21 | acct_hsa | HSA | account | Do you have an HSA? | an assets_other row of type HSA | Needs: your hsa details (balance and as-of date) | profile_calc, needs_calc |
| 22 | acct_brokerage | Brokerage account | account | Do you have a brokerage account? | an assets_other row of type Brokerage | Needs: your brokerage account details (balance and as-of date) | profile_calc, needs_calc |
| 23 | acct_home | Home you own | account | Do you have a home you own? | an assets_other row of type Real estate | Needs: your home you own details (balance and as-of date) | profile_calc, needs_calc |
| 24 | acct_vehicle | Vehicle you own | account | Do you have a vehicle you own? | an assets_other row of type Vehicle | Needs: your vehicle you own details (balance and as-of date) | profile_calc, needs_calc |
| 25 | debt_other | Other debt | debt | Do you have any other debt? | any liabilities row that no other debt question matches | Needs: your other debt details (balance, rate, payment and due date) | profile_calc, needs_calc |
| 26 | bill_other | Other monthly bill | bill | Do you have any other monthly bill? | any bills row that no other bill question matches | Needs: your other monthly bill details (name, amount a month and due day) | profile_calc, needs_calc |
| 27 | ret_workplace | Workplace retirement plan | retirement | Do you have a workplace retirement plan? | a contributions row whose account contains 401, 403 or 457 | Needs: your workplace retirement plan details (what goes in each paycheck) | profile_calc, needs_calc |
| 28 | ret_ira | IRA | retirement | Do you have an IRA? | a contributions row whose account contains IRA | Needs: your ira details (what goes in each paycheck) | profile_calc, needs_calc |

Status values the calc `profile_calc` returns per question: `have` (rows exist), `no`, `needs_entries` (answered yes, no rows), `ask` (unknown, no rows), `conflict` (answered no, rows exist). `needs_calc` turns `needs_entries` and `ask` into rows of the Needs list.

## Interview topics (ARCHITECTURE.md section 15)

| # | Topic | Stored in | Unlocks |
|---|---|---|---|
| 0 | Welcome, reminders preference | page preferences, reminders | habits |
| 1 | Names of bank, card issuers, lenders, plan, payroll | labels across datasets | readable labels |
| 2 | Age, target retirement age, income goal | retirement_plan | retirement model, age ladder |
| 3 | Filing status, state, tax bracket | assumptions | take-home cost estimate |
| 4 | Pay frequency, gross, next payday, latest stub | income_pay, paystubs | take-home, payoff surplus, Pay page |
| 5 | Rent or own, payment, balance, rate | bills or liabilities | housing in DTI |
| 6 | Every monthly bill | bills | Bills page, runway |
| 7 | Everyday spending estimate | bills (spending kind) | left over estimate |
| 8 | Balance of each bank and savings account on one day | balance_log, assets_other | cash, runway, drift |
| 9 | Each card and loan: balance, rate, minimum from the statement, due date | liabilities | DTI, payoff plan, scores |
| 10 | Money withheld from pay | liabilities | payoff surplus |
| 11 | Retirement and investment accounts, percent to 401(k), match, HSA | contributions, holding_snapshots, contribution_plan | Retirement page |
| 12 | Home, vehicle, other valuables | assets_other | net worth, loan to value |
| 13 | Upcoming expenses | savings_goals (and Save for when built) | goals, runway |
| 14 | Tax refund or tax owed | liabilities, assets_other | net worth |
| 15 | Insurance | bills, liabilities | score components |
| 16 | Layout preference | page config | layout |
| 17 | Check-in day, reminder time | milestones, reminders | check-ins |
| 18 | Confirm assumptions (growth, inflation, high rate line, card minimum estimate) | assumptions, scores_config | projections, scores |
| 19 | Read back one page and ask for corrections | all | trust |

## Wording library

| Situation | Say |
|---|---|
| Missing minimum payment | "Please read the minimum payment due from your latest statement, or send a screenshot. I will not estimate it." |
| Rate from memory | "Is this rate from a statement? I will mark it confirm on statement until it is." |
| Two balances disagree | "Statement A says X and the dashboard says Y. Which date is right?" |
| User says no to a category | "Understood. I will leave it out and not ask again." |
| Unknown | "No problem. I will keep a task for it and ask again on the next check-in." |
