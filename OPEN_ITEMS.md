# OPEN_ITEMS: what is not done, and decisions the owner of this bundle must make

## Decisions needed

| # | Decision | Recommended default |
|---|---|---|
| D1 | License for the bundle and the code base | TODO: not chosen. Until chosen, no license is granted. Pick one (for example a permissive license for the code and a document license for the text) and replace the TODO in README.md and package.json |
| D2 | Where the bundle is hosted and who may receive it | private repository or shared folder |
| D3 | Whether Save for ships in the next stage or stays PLANNED | build in the order in ARCHITECTURE.md section 17, stage 7 |
| D4 | Whether metric_snapshots is built | yes, after one month of use; CORRELATIONS.md has the schema |
| D5 | Whether levels may ever be added to Progress | no; only if each is earned from verified facts and never decreases |
| D6 | Whether net worth sits on Home in a closed fold or on its own page | closed fold on Home |
| D7 | Contact address for security reports | add it to SECURITY.md before the repository is public |
| D8 | Git author identity of the first commits | check `git log --format='%an %ae'`; rewrite history before publishing if the identity should not be public |
| D9 | Net worth rows and copy name a house and a truck (page/js/10_calcs_base.js nw_rows, 40_draw_tabs.js, 80_draw_v3_v4.js) | make the labels generic (Property, Vehicles) from the asset types the user has; change the approved inventories and tests in the same change; until then the labels appear even when the user owns neither |

## Not built (PLANNED)

| Item | Where designed | Notes |
|---|---|---|
| Save for (goals with a pace, advice engine savefor_calc, screens, inbox) | ARCHITECTURE.md 11.1 to 11.5 | savings_goals exists as plain goals today |
| Spending picture (spending_coverage, spending_calc, spending_picture) | ARCHITECTURE.md 7.7 | everyday spending is a bill row of kind spending for now |
| metric_snapshots dataset and trend chart | CORRELATIONS.md | template provided |
| Page-side write of tasks without the db capability beyond the text box | ARCHITECTURE.md 14 | the text box fallback works |
| Other countries | LOCALIZATION.md | content swap list only |
| Multiple currencies, right to left layout | none | not designed |
| A native app or a connection to banks | none | out of scope; the user uploads statements |

## Known gaps in verification

| Gap | Detail |
|---|---|
| Approved inventories | tests/lib/inventory_approved.json and marks_approved.json were regenerated on the invented demo data (APPROVE_MARKS=1). When your own data shows a different set of elements (for example an account the demo lacks), the browser suite reports a changed inventory: review the diff and regenerate with APPROVE_MARKS=1 on your private copy |
| Real phone test | the measured bottom bar must be confirmed on a real phone by the user (ARCHITECTURE.md 9.3) |
| Live runtime | RUNTIME_CONTRACT.md was observed on contract 0.2.67; re-run the Stage 0 probe on every account |
| Screenshots | taken in a desktop Chromium at phone width with the invented data; fonts may differ from a phone |

## Process items

1. Keep tools/check_docs.js in the full run so a tile or dataset change fails until the docs are regenerated.
2. Review the secondary-summary rows in age_benchmarks once a year and replace them with primary sources where a primary source exists.
3. Decide how long to keep monthly backups (BACKUP_GOOGLE_DRIVE.md suggests 12).
