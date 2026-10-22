# Metric Dictionary

How every reported metric is defined. Source notebooks: `03_metric_definitions.ipynb` and `04_weekly_growth_loss.ipynb`.

## Time

| Term | Definition |
|---|---|
| Analysis window | 2015-02-01 to 2020-06-30 (complete months only) |
| Week | Monday to Sunday. Weekly reports use full weeks only: 2015-02-02 to 2020-06-28 (282 weeks) |
| Cohort | A group of players who started at the same time. Everyone opened an account in February 2015, so groups are based on `first_play_month`, the month a player first played poker |

## Player status

| Metric | Definition |
|---|---|
| Weekly active players | Played at least one cash game or tournament in the week (`WAP` in DAX measure names) |
| 4-week active players | Played at least once in the last 4 weeks, including the current week. Similar to the industry's "28-day actives" (`active_base` in the data) |
| New players | First week a player ever played poker |
| Retained (stayed) | Played this week and also within the previous 4 weeks |
| Returning (reactivated) | Played this week after more than 4 weeks away |
| Lost (churned) | No play for 4 weeks in a row; counted in that fourth week |
| Net change | New + returning − lost |

**Weekly balance:** 4-week active players this week = last week's number + new + returning − lost. Like a bank balance, it adds up exactly; checked in notebook 03 for all 282 weeks.

**Why 4 weeks:** 86% of returning play happens within 4 weeks of the previous active week, it matches the common 30-day inactivity rule, and a sensitivity check (2, 4 and 8 weeks) changes how many players count as lost, but not the trend.

## Money

All amounts in euros.

| Metric | Definition |
|---|---|
| Stakes (money bet) | Cash put into cash game pots (including blinds and antes) plus tournament entry fees and add-ons |
| Player result | Cash winnings + tournament prizes − stakes. Positive means the player came out ahead |
| Deposits | Completed deposits only (status `S`) |
| Withdrawals | Completed withdrawals only (status `S`) |
| Net deposits | Money deposited minus money withdrawn. **Main player-value measure.** The data has no rake or fee field, so this is a simple proxy and an upper bound: it also includes money lost to other players, spending on other products in the same account, and unwithdrawn balances (all players together: EUR 5.2M net deposits vs EUR 1.1M lost at the poker tables) |
| Deposit failure rate | Failed deposit attempts / all deposit attempts |
| Withdrawal reversal rate | Reversed withdrawal value / (reversed + completed withdrawal value) |
| Churned prior net deposits | Net deposits of the players who churned this week, over their last 4 weeks before going inactive (weeks −7 to −4 relative to the churn week) |

## Payment transactions

- A transaction is dated by its request date (`SummaryDate`); requests processed after 2020-06-30 are excluded.
- Exact duplicates (same player, dates, time, method, category, card type, amount and status) are removed.
- Payment categories and card types are standardized; see `02_data_model.ipynb`.
