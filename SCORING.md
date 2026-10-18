# SCORING: the two scores and how they are worked out

There are two scores, shown separately on Home and Progress: a Financial score (how well set up) and a Risk score (how exposed). They are never combined. No streaks, no points for typing a number, no comparison with other people beyond the sourced age statistics (ARCHITECTURE.md section 12).

The components below are generated from the code and the shipped config (`scores_config`). Weights sum to 100 per score. Values in the config can be changed by decision; the calc reads them, so no code change is needed.

## Method

1. Each component has one measured value, a best level and a worst level. fraction = clamp((value minus worst) / (best minus worst), 0, 1).
2. Financial points = fraction x weight. Risk points = (1 minus fraction) x weight.
3. Score = points / weight of the components that have data x 100. A component without data shows "Needs: ..." and is left out; the card says "based on N of M components".
4. Range line: the score if every Needs component earned the worst result, and if it earned full marks: "Could land X to Y once the Needs are filled".
5. If the components with data carry under 60 percent of the total weight, the card shows "Not enough data" and the Needs list, no number.
6. Labels. Financial: 80 and up Strong, 60 to 79 Solid, 40 to 59 Building, under 40 Needs attention. Risk: 75 and up High, 50 to 74 Elevated, 25 to 49 Moderate, under 25 Low. Colour never carries the label alone.
7. Any input that is a guess (a card minimum at 4 percent of the balance, an estimated payment, a planned amount) is named in the component's input line.
8. No trend arrow or green/red change until scores_history has 6 snapshots across 60 days.
9. A component that does not apply (no house, no variable-rate debt) is "Not used": it leaves the weight and is not a Needs. Only a component that applies but lacks data is a Needs.
10. Footer on both cards: "A planning tool built from numbers you entered. Not financial, tax or legal advice."

## Components

| Score | Id | Component | Weight | Best | Worst | Unit | Needs gating |
|---|---|---|---|---|---|---|---|
| financial | F1 | Debt to income | 12 | 20 | 50 | % |  |
| financial | F2 | High interest debt burden | 14 | 0 | 20 | % of annual gross |  |
| financial | F3 | Emergency fund (cash runway, must-pay) | 14 | 6 | 0 | months |  |
| financial | F4 | Savings rate from payroll | 10 | 15 | 0 | % of gross |  |
| financial | F5 | Retirement pace vs goal (401(k) saving only) | 18 | 100 | 0 | % of low goal |  |
| financial | F6 | Monthly cash flow surplus | 10 | 20 | 0 | % of take-home | yes |
| financial | F7 | Net worth vs age benchmark | 8 |  |  | percent of typical | yes |
| financial | F8 | Credit utilization | 6 | 10 | 60 | % | yes |
| financial | F9 | Insurance and protection | 8 | 100 | 0 | % covered | yes |
| risk | R2 | Single income earner | 16 | 50 | 100 | % of household gross |  |
| risk | R3 | Liquidity runway (all fixed costs) | 16 | 6 | 0.5 | months |  |
| risk | R4 | Debt rate sensitivity | 10 | 0 | 3 | % of take-home | yes |
| risk | R5 | Combined loan to value | 10 | 50 | 90 | % |  |
| risk | R6 | Total debt to total assets | 6 | 30 | 80 | % |  |
| risk | R7 | Tax exposure | 8 | 0 | 100 | % of liquid cash |  |
| risk | R8 | One-time planned cost | 10 | 0 | 30 | % of take-home |  |
| risk | R9 | Data completeness | 10 | 0 | 100 | % of queue still needed |  |

"Needs gating" yes means the component needs a specific input (a credit limit, a rate type, the insurance list) before it counts.

## Inputs the user can change

`scores_config` carries: the weights, best and worst, the high rate cutoff (default 10 percent), the card minimum estimate (default 4 percent of the balance), the trend rule (6 snapshots over 60 days), the labels and the disclaimer. `age_metrics_config` carries the age statistics settings (SOURCES_AND_BENCHMARKS.md). Change them only by a decision the user states; log the change in CHANGELOG.md.

## Score history

`scores_history` keeps one dated row per snapshot (financial, risk, based on N of M). Claude adds a row after the monthly check-in, never the page. Calc `score_since_calc` compares the latest row with the previous one for the "since last time" line.

## What would raise it

Home lists the top 3 components by room to improve, each with the number to change and the points it would add; this is `scores_calc` output, so a corrected balance moves the list with no code change.

## Tests

Unit tests per component with hand worked fixtures (tests/unit.js), invariants (weights sum to 100, fractions between 0 and 1, "Not enough data" below 60 percent coverage) in page/invariants.js, and check C19 on the page.
