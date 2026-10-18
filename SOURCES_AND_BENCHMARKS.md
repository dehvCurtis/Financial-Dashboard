# SOURCES_AND_BENCHMARKS: where the reference numbers come from and how they stay honest

The dashboard compares you with published statistics and uses published limits. These are reference datasets, not your data. Each row carries its source, url, data year and the date it was last checked. A row without a source is not shown. No number in this bundle is taken from a person's own accounts.

## Reference datasets

| Dataset | What it holds | Refresh |
|---|---|---|
| age_benchmarks | published statistics by age group (net worth, income, retirement balances, debt, savings rate) and published guidelines, each with `source`, `url`, `data_year`, `population_note`, `as_of_checked` | annually, or when a new survey publishes |
| age_metrics_config | settings for the age ladder: which metrics are shown, the percentile rows used, labels | only by decision |
| dti_benchmarks | debt to income guidelines (the classic front end and back end guideline, lender style thresholds) with a note that these are rules of thumb | annually |
| ltv_benchmarks | loan to value thresholds (for example when private mortgage insurance can be cancelled) with source and url | annually |
| contribution_limits | per year: 401(k) deferral, catch up, total additions, HSA self and family, IRA, with `status` published or estimate | every October or November when the next year is announced; confirm on the tax authority site |
| scores_config | weights and thresholds (SCORING.md) | by decision |

## Source rules

1. Prefer a primary source: a central bank survey, a statistics bureau, a regulator, a tax authority, a plan record keeper. A secondary summary is labelled "(secondary summary)" in `source` and has lower confidence.
2. Every row has `data_year`, a dollar year in `population_note` or `unit`, and `as_of_checked` (the day Claude last opened the source).
3. A benchmark the agent cannot find is left out. The card says "Needs data" for that metric. Nothing is estimated from memory.
4. Percentile interpolation: between the published rows (p25, p50, p75, p90, p95) for the bracket; below p25 the card says "below the 25th", above p95 "above the 95th". A median alone gives "above" or "below the typical", never a made-up percentile.
5. Nothing here is a recommendation. The copy says "published guideline" or "typical", never "should".

## Refresh procedure (once a year, and when a limit is announced)

1. Open each source in the rows being refreshed. Note the new value, year and url.
2. Replace the row in place (same `id`), set `as_of_checked` to today.
3. Run `node tests/data_rules.js` (every used row has a source and year) and check C18.
4. Update the CHANGELOG entry and the dataset document description if the scope changed.
5. Publish only if the user asked (PUBLISH.md).

## Kinds of source the reference data uses

Central bank household surveys (net worth and income by age), national statistics bureaus (income, spending), a retirement record keeper's annual "how people save" report, regulator consumer pages (mortgage insurance, credit), tax authority pages (annual limits), and named financial education sites (always marked secondary). The exact list is in the `source` column of age_benchmarks, dti_benchmarks and ltv_benchmarks in demo_data/ and templates/; every url is public.

## Other countries

LOCALIZATION.md lists what to swap: benchmark sources, limits, account types and terms.
