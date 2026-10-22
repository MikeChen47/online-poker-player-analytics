# Data Audit

**Dataset:** Division on Addiction, *Second Session at the Virtual Poker Table*. Medford, MA: Division on Addiction, The Transparency Project [database distributor], accessed October 21, 2026.

**Notebooks:** `notebooks/01_data_audit.ipynb` (checks) and `notebooks/02_data_model.ipynb` (cleaning rules applied)

## What the data covers

| File | Grain | Rows | Players | Date range |
|---|---|---|---|---|
| Demographics | One row per player | 5,028 | 5,028 | n/a |
| Cash games | Player x day | 51,763 | 4,232 | 2015-02-01 to 2020-07-28 |
| Tournaments | Player x day | 82,831 | 3,448 | 2015-02-01 to 2021-01-01 |
| Deposits | One row per attempt | 295,088 | 5,008 | 2015-02-01 to 2020-07-15 |
| Withdrawals | One row per request | 32,307 | 1,827 | 2015-02-01 to 2020-07-15 |

All players opened their account in February 2015. Amounts are in euros.

The original study used February 2015 to January 2017, but the files run to mid-2020. That gives roughly five and a half years of history for the same cohort, which is useful for long-run retention and LTV.

## Issues found

| # | Issue | Size | Treatment |
|---|---|---|---|
| 1 | Deposit file has fewer rows than the codebook states | 295,088 vs 295,119 (31 rows) | Cannot be resolved from the files. Documented; impact is under 0.02% of rows. |
| 2 | Exact duplicate transactions (same player, dates, time, method, amount, status) | 40 deposits, 3 withdrawals | Remove, following the original authors' R code. |
| 3 | Inconsistent payment category labels | `ewallet` vs `eWallet` (481 rows) | Standardize to `eWallet`. |
| 4 | Inconsistent card type codes | `CCD`/`CCd`, `DCD`/`DCd`/`Dd `, `UND`/`Und`, blanks | Standardize following the R code. |
| 5 | Missing country | 12 players | Keep the players; label region as `Unknown`. |
| 6 | Players with no deposit record | 20 players | Keep; they still have poker activity. |
| 7 | Stray tournament rows dated 2021-01-01 | 2 rows | Exclude; nothing else exists after July 2020. |
| 8 | Partial final month | Data ends mid-July 2020 | Analysis window ends 2020-06-30 so every month is complete. |

No duplicate player-days, no all-zero activity rows, no negative amounts, and no transactions processed before they were requested.

## Business-relevant findings already visible

- **24% of deposit attempts fail.** Success rates vary widely by method, from about 70% for PayPal and Mastercard to about 90% for PaysafeCard vouchers. Failed deposits are lost revenue and a likely churn trigger, so this becomes a payments analysis.
- **47% of withdrawal requests are reversed, covering 55% of requested withdrawal value.** Players frequently cancel a cash-out and keep the money in their account. This is a known pattern in online gambling and an important driver of net deposits.
- **Monthly active players fall from 3,215 in February 2015 to about 370 by year end,** then decline slowly. The steepest loss is in the first three months, which is where retention analysis should focus.
- **Active players rise in March 2020** (110 to 153) while the number of completed deposits falls. This lines up with COVID-19 lockdowns and is a candidate for the anomaly investigation.

## Limitations

- **No rake or house revenue field.** Cash game stakes and winnings are pot amounts between players, so operator revenue cannot be calculated directly. Player value is measured with **net deposits** (completed deposits minus completed withdrawals), a common proxy in the industry.
- **Single registration cohort.** Everyone joined in February 2015, so cohorts are built on first poker activity or first deposit, not registration month.
- **Post-2017 completeness is not documented.** The monthly series is continuous through mid-2020, so the data is treated as complete, but this is an assumption.

## Acknowledgement

This project utilized data from the Transparency Project (www.thetransparencyproject.org), Division on Addiction, the Cambridge Health Alliance, a teaching affiliate of Harvard Medical School.
