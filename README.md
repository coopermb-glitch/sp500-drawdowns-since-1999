# S&P 500 drawdowns since 1999

Two tables.

`drawdowns-since-1999.csv` has one row for each of the 343 current S&P 500 members that traded in 1999. Each row gives the stock's worst fall from a running high, the dates of that peak and its trough, the first date it climbed back above the peak, the years that took, and how far the stock sat below its high on October 13, 2026.

`drawdowns-1999-class.csv` has one row for each of the 500 stocks in the S&P 500 on January 4, 1999, including the 271 that stopped trading since. It answers the survivorship question the first table leaves open.

The numbers for stocks that still trade come from Yahoo Finance daily adjusted closes, pulled with the yfinance Python library, with dividends reinvested. The window runs from January 4, 1999 to October 13, 2026.

## What the data shows

- 317 of the 343 fell 50% or more at least once, and 108 fell 80% or more.
- The median worst fall was 71%. The index itself fell 55% at its worst, and 291 members fell further.
- 303 climbed back above their old high, with a median trip of 5.8 years. 87 of those needed more than a decade.
- 40 have not made it back as of October 13, 2026, Citigroup and AIG among them.

I counted these from unrounded values, so a count you take from the rounded CSV can differ by one or two.

## The class of 1999

A list of today's members holds only the companies that made it. The second table follows every stock in the index on January 4, 1999 until October 13, 2026 or its last day of trading.

- 179 are still in the S&P 500, 50 left the index and still trade, 238 left through a buyout or merger, and 33 went bankrupt.
- Between 74% and 80% fell 50% or more at least once, against 92% of today's members. Buyers took many of the departed before the 2008 crash could reach them.
- Between 36% and 41% never climbed back above the high before their worst fall, against 12% of today's members.
- The median worst fall sits between 69% and 73%, close to the 71% for today's members.

A stock stays in through renames, ticker changes and mergers in which its company was the buyer. It leaves when another company bought it, and the deal value counts as its last price. For those stocks the worst fall comes from the quarterly high and low prices in SEC filings and merger documents. Quarterly ranges run about 2 points deeper than daily closes with dividends at the median and about 6 points at the 90th percentile, so falls of 50% to 56% on that basis count as borderline, which is why the class figures are ranges. The 33 bankrupt stocks count as falls of 80% or more that never recovered. The January 1999 member list comes from github.com/fja05680/sp500, which warns that its early years may hold errors.

## Columns

| column | meaning |
|---|---|
| ticker | Yahoo Finance symbol |
| name | company name |
| sector | sector name |
| first_date | first trading day in the window |
| worst_drop_pct | largest fall from a running high, in percent |
| peak | date of the high before the worst fall |
| trough | date of the low of the worst fall |
| recovered | first date back above that peak, blank if the stock hasn't made it back |
| years_peak_to_recovery | years from the peak to the recovery date |
| now_vs_high_pct | distance from the running high on October 13, 2026, in percent |

### drawdowns-1999-class.csv

| column | meaning |
|---|---|
| record | ticker in the fja05680 membership data; a -YYYYMM suffix marks the month the ticker ended |
| company | company name in 1999, with the current name where it changed |
| fate | In the S&P 500, Left the index still trades, Bought out or merged, or Bankrupt |
| fate_month | month the stock stopped trading or the bankruptcy filing |
| acquirer_or_current_ticker | buyer and deal terms, or the current ticker of a renamed company |
| worst_fall_pct | worst fall in percent; daily adjusted closes or quarterly high and low, per measured_from |
| measured_from | basis of the worst fall |
| fell_by_half | yes, no, borderline or unknown |
| back_above_old_high | whether the stock climbed back above the high before its worst fall, before it stopped trading |
| years_to_recover | for stocks with daily prices |
| now_vs_high_pct | distance from the running high on October 13, 2026, for stocks that still trade |
| note | gaps, adjustments and judgment calls |
| source | filing or page behind the row |

## Quick look in Python

```python
import pandas as pd

df = pd.read_csv("drawdowns-since-1999.csv")
print(df["worst_drop_pct"].median())   # -71.3
print(df["recovered"].isna().sum())     # 40
print(df.sort_values("years_peak_to_recovery", ascending=False).head(10))
```

## Write-up and charts

The full write-up with charts lives at https://portfolioautopsy.pages.dev/research/drawdowns-since-1999. PortfolioAutopsy, the free tool behind the study, runs any mix of holdings through the same daily data at https://portfolioautopsy.pages.dev.

## License

I license this table under CC BY 4.0 (see LICENSE). Credit "PortfolioAutopsy, S&P 500 drawdowns since 1999" and link the write-up. The prices come from Yahoo Finance, and this repo holds only the derived summary table, with no raw prices.

## Changes

- October 14, 2026. Added the class of 1999 table. Corrected JCI (Tyco International before 2016), whose Yahoo series misses the 2007 Covidien and TE Connectivity spin-off; its worst fall is now 86.8% in 2002, recovered in 2014. No headline count changed.
