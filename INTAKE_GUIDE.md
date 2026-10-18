# INTAKE_GUIDE: from a statement to a dataset row

Intake is how a PDF, CSV, screenshot or typed number becomes dashboard data. The agent follows these steps for every upload. All example values are invented (Example Bank, Example Card, round numbers).

## The steps

1. Name the document type (table below) and the period it covers. Ask if unclear.
2. Read every page. Extract fields into rows; do not store the original file, an account number, a loan id, an address or a name that is not a label the user chose.
3. Read the key numbers back to the user (closing balance, minimum due, net pay) before saving.
4. Write rows to the datasets in the table. Each money row has `source` (document type and period, for example "Example Card statement, March") and `as_of_date`.
5. Run the checks that apply (CORRELATIONS.md): the statement closing balance must equal the liability balance and the balance log entry; stub arithmetic must add up; a deposit must match the stub.
6. Replace, never duplicate: a corrected value replaces the old row. History datasets (balance_log, scores_history, networth_history, snapshots) keep dated rows.
7. Close the task or data request that asked for this document (set status Provided or Done), and tell the user in one or two sentences what changed.
8. Update the dataset in the dashboard (PUBLISH.md) only when the user has asked for a publish; otherwise keep the rows ready.

## Which document goes where

| Document | Datasets written | Key fields to extract | Checks |
|---|---|---|---|
| Pay stub | paystubs, income_pay, contributions | pay_date, period_start, period_end, gross, federal_tax, state_tax, fica, k401_you, hsa_you, hsa_employer, benefits, garnishment, net_pay, other_additions, year to date lines | C4, C5, C6, C7 |
| Credit card statement | card_statements, card_transactions, liabilities, balance_log | closing_date, payment_due_date, previous_balance, payments_total, purchases, fees_charged, interest_charged, new_balance, minimum_payment_due, credit_limit, apr_purchase_pct, every transaction | C8, C29 |
| Bank statement (checking, savings) | bank_statements, bank_flows, cash_events, balance_log | period, opening_balance, closing_balance, credits_total, debits_total, apy, each flow with date, side, direction, amount, payee type | C12, C7, C11 |
| Mortgage statement | mortgage_statements, liabilities | statement_date, principal_balance, rate, amount_due, principal_due, interest_due, escrow_due, next_due, maturity, year to date | C22, C23 |
| Year end mortgage interest form | mortgage_statements (kind), liabilities | interest paid, points, outstanding principal | C23 |
| Utility or other bill | bill_statements, bills | bill, statement_date, amount, due_date | C27 |
| 401(k) or brokerage statement | holding_snapshots, plan_summary, plan_stats, acct_a_holdings, acct_b_holdings, assets_other | per fund: quantity, price, cost basis, value; account balance; period return | C2, C3 |
| Plan source split | acct_a_sources | balance by money source | C2 |
| Loan statement | liabilities, balance_log | balance, rate, payment, next_due, days past due | C9 |
| Tax refund or notice | liabilities or assets_other (Receivable) | amount, expected date | C1 |
| Home or vehicle value | assets_other | value_point, as_of_date, source | C10 |

Exact field names, types and units are in DATA_MODEL.md. Starter rows with every field set to null are in templates/.

## Example rows (all invented)

Pay stub row in paystubs (a gross of 2,310.00 paid every two weeks):

```json
{"pay_date":"2026-01-15","period_start":"2026-01-01","period_end":"2026-01-14","gross":2310.00,"federal_tax":277.20,"state_tax":92.40,"fica":176.72,"k401_you":231.00,"hsa_you":46.20,"hsa_employer":25.00,"benefits":61.00,"garnishment":0,"net_pay":1425.48,"other_additions":0}
```

Check C4: 2310.00 minus 277.20 minus 92.40 minus 176.72 minus 231.00 minus 46.20 minus 61.00 equals 1425.48.

Card statement row in card_statements:

```json
{"statement_id":"EC-2026-01","liability_id":"L2","period_start":"2025-12-10","closing_date":"2026-01-09","payment_due_date":"2026-02-03","previous_balance":870.35,"payments_total":870.35,"purchases":1234.56,"fees_charged":0,"interest_charged":0,"new_balance":1234.56,"minimum_payment_due":35.00,"credit_limit":4750.00,"apr_purchase_pct":22.99,"source":"Example Card statement, January"}
```

Bank flow row in bank_flows:

```json
{"flow_id":"F1","date":"2026-01-15","month":"2026-01","side":"in","account":"Example Checking","direction":"in","amount":1425.48,"category":"Pay","payee_type":"employer","payee_name":"Example Employer","counterparty":"Example Employer","note":"Pay deposit","source":"Example Bank statement, January"}
```

## Cleaning rules

- Dates become YYYY-MM-DD; months YYYY-MM. Money is a number in dollars with two decimals, positive unless the field says it is a credit. Percents are numbers like 22.99, not 0.2299.
- Names are the user's own labels, never an account number or a last four digits. If the statement prints an account number, drop it.
- A value you cannot read is null, with a note and a task. Never fill a guess.
- A PDF that cannot be read (scanned, protected) is reported to the user; ask for a CSV export or a screenshot.
- Before saving, scan the row text for em or en dashes, digit runs of 8 or more, emails and street addresses. Remove them.

## CSV uploads

CSV is a view, not a source: columns arrive as text, nested values are JSON text, a blank may be null or empty. Convert to the JSON dataset shape (flat rows, numbers as numbers) before saving. Never restore from CSV (BACKUP_FORMAT.md).
