# LOCALIZATION: using the dashboard outside the United States

The reference build is US style. The structure (datasets, calcs, checks, pages) works anywhere; the content that is country specific is listed here, with what to change and where.

| Area | US content | Change for another country | Where |
|---|---|---|---|
| Currency | USD, "$", two decimals, comma thousands | set the symbol and separator in the money helper; keep numbers as plain numbers in data | page/js/00_helpers_charts.js (money, pctf) |
| Dates | YYYY-MM-DD in data; month names in English on screen | data format stays; on-screen names follow the locale | helpers, date formatters |
| Retirement accounts | 401(k), IRA, Roth, HSA, catch-up ages | rename account types and the contribution_limits fields (employee limit, tax-free allowance, pension relief); the plan-year tile and the percent card read these fields | contribution_limits, contributions, page/js/30_retire_pct.js |
| Employer match | percent of pay formula | keep as `employer_annual`; change the copy | contributions, copy |
| Tax | federal tax, state tax, FICA (social security and medicare) | map to income tax, regional tax, national insurance; keep the stub arithmetic check C4 with your lines | paystubs fields, C4 |
| Take-home cost estimate | combined marginal rate from filing status and state | your marginal rate; the estimate is always labelled | assumptions |
| Credit | APR, credit limit, utilization | the same ideas apply; swap benchmark sources | liabilities, scores_config F8 |
| Housing | mortgage, escrow, PMI, home equity line, loan to value | rename escrow and PMI lines; the LTV thresholds come from ltv_benchmarks | ltv_benchmarks, mortgage_statements |
| Benchmarks | national household survey, statistics bureau, record keeper study | replace every row with sources from your country, each with url, data year and dollar year | age_benchmarks, dti_benchmarks |
| Spelling and words | US English | translate the copy in the page; the tests that check copy are keyed to ids, not wording | page/js, markup.html |
| Paper forms | year end interest form (US) | your equivalent; C23 ties it to the statements | mortgage_statements.kind |

## Rules when localizing

1. Keep ids, dataset names and field names in English; change labels only. The tests, tile links and backups depend on ids.
2. Replace reference data completely; never leave US numbers under a foreign label.
3. Update SOURCES_AND_BENCHMARKS.md with the new sources.
4. Keep the no-dash rule in the new language: use commas, colons and full stops.
5. Add or change tests for any calc that changes meaning (limits, tax lines). Add a CHANGELOG entry.
6. The scores use the best and worst levels from scores_config; revisit them for your country's norms (for example a different emergency fund target).

## Not localized in this bundle

Right to left layouts, non-Latin number formats, multiple currencies in one dashboard, and tax calculators. These are listed in OPEN_ITEMS.md.
