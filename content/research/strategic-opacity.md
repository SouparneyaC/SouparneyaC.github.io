+++
title = "Strategic opacity and the 13F disclosure window"
+++

# Strategic opacity and the 13F disclosure window

> the options-heavy funds that file their ownership disclosures right at the legal deadline turn their portfolios over about four to five times faster than funds that file early, exactly the group with the most reason to delay.

## Abstract and introduction

By the time the public sees a fund's 13F filing, up to 45 days may have passed since the reported quarter ended, and the fund's actual positions may already have moved on. This project asks whether the funds that use the full legal window, filing right at the deadline rather than early, look systematically different from funds that file promptly, in exactly the way a fund with the most to hide by delaying disclosure would be expected to. Comparing three chronic deadline filers (Susquehanna International Group, Parallax Volatility Advisers, and Wolverine Trading, all options market makers) against two chronic early filers (Baillie Gifford and the Swiss National Bank, both slow-moving, size-matched controls), deadline filers turn over roughly 38% of their portfolio positions per quarter against 8% for early filers, a 4.6 times difference, while holding roughly 61% of their reported positions in options against 0% for the early group.

## Data

Holdings and filing-date data come from the QUANTkiosk 13F API, covering every institutional filer's reported filing date and reporting period. Filing lag is computed directly as the filed date minus the reporting period end date, in calendar days; the legal window is 0 to 45 days, with filings outside 0 to 50 days excluded as either impossible values or foreign administrative backfill unrelated to genuine strategic timing. Across the full universe of 11,869 institutions with clean filings, funds are classified by their own consistency across at least eight quarters: a chronic deadline hugger files at day 43 or later in at least 75% of its quarters, while a chronic early filer does so in at most 25%, with early filers additionally ranked by assets under management so the comparison group is not small filers by construction. Five funds were selected for detailed position-level analysis this way: the three most options-heavy chronic deadline huggers, and two large, chronic early filers as a size-matched control, tracked across eight quarters ending 2025 Q1.

## Literature review

The closest academic anchor is Agarwal, Jiang, Tang, and Yang (2013), who study hedge funds that request confidential treatment for specific 13F positions, hiding them from public disclosure entirely until the SEC grants release, and find these funds earn roughly 6.4 percentage points of annualized alpha above non-confidential funds, concentrated in the hidden positions themselves. This project studies a related but distinct and far more common behavior: not hiding a position outright, which requires an SEC application, but simply using the full ordinary disclosure window rather than filing early, a legal, low-friction form of the same underlying incentive to delay when fast-moving positions become public. Kacperczyk, Sialm, and Zheng's (2008) "return gap," the systematic difference between a fund's realized returns and the returns implied by its disclosed holdings, is the same idea from a different angle: funds that do more between disclosure dates than is ever fully observed tend to be exactly the ones with a real return advantage to protect.

## Models

Filing lag for filing \( i \) is

\[
\text{lag}_i = \text{filed\_date}_i - \text{period\_end\_date}_i
\]

measured in calendar days, with a fund classified as a deadline hugger in a given quarter when \( \text{lag}_i \geq 43 \). For the five selected funds, quarterly position turnover, value turnover, and options exposure are computed directly from position-level 13F detail:

\[
\text{turnover\_pct} = \frac{\text{new positions} + \text{deleted positions}}{\text{total positions}}, \qquad
\text{value\_turnover} = \frac{\sum |\Delta_{\text{QoQ}}\,\text{value}|}{\sum |\text{value}|}, \qquad
\text{opt\_pct} = \frac{\text{options positions}}{\text{total positions}}
\]

Position-level rather than aggregated data matters here specifically because it exposes sub-manager detail that a fund's aggregated 13F summary would hide, the same kind of granularity the confidential-treatment literature above treats as informative.

## Backtesting

The equivalent of an out-of-sample check for this kind of comparison is asking whether the five-fund case study's pattern also holds at the level of the full 11,869-institution universe, rather than being an artifact of which five funds happened to be chosen. It only partly does. Tracked annually from 2013 through 2025, the share of all institutions filing on day 43 to 45 fell from roughly 54% to roughly 38% over the period, and the universe-wide average options share fell over the same period too, from roughly 5.4% to roughly 2.2%, both series declining together rather than the relationship strengthening over time. That is a real, honest complication: the five-fund case study is a cross-sectional snapshot showing a sharp, clean split between two groups of funds at a point in time, while the full-universe trend shows both quantities falling in parallel across fifteen years, which is not the same claim and should not be read as one. One further documented exception at the individual-fund level: Simplex Trading shows a high options share (79% of positions) while filing consistently early, around 24 days on average, a controlled counter-example flagged directly rather than dropped from the dataset, since it will need its own explanation (a different regulatory regime or a different strategic calculus) before the deadline-hugging story can be treated as general.

## Results

![Quarterly position turnover by fund, deadline huggers versus early filers](/images/research/strategic-opacity/fig1_turnover_boxplot.png)

*Quarterly position turnover for the five selected funds, 2023 Q2 through 2025 Q1. The three deadline huggers (SIG, Parallax, Wolverine) cluster between roughly 30% and 45% turnover; the two early filers (Swiss National Bank, Baillie Gifford) cluster below 15%. The Parallax versus Swiss National Bank comparison alone is a 4.6 times difference in typical quarterly turnover.*

![Filing lag against portfolio turnover rate, colored by filing behavior](/images/research/strategic-opacity/fig2_lag_scatter.png)

*Every fund-quarter observation for the five selected funds, filing lag in days on the horizontal axis against position turnover rate on the vertical axis. The two groups form visually distinct clusters with almost no overlap, deadline huggers at roughly 30 to 47 days of lag and 30% to 47% turnover, early filers at roughly 24 to 45 days of lag and under 20% turnover, rather than a single continuous relationship running through both.*

![Deadline-hugging rate and average options exposure across the full 11,869-institution universe, 2013 to 2025](/images/research/strategic-opacity/fig4_annual_trend.png)

*The full-universe honesty check on the five-fund story above: both the share of institutions filing at the legal deadline and the universe-wide average options exposure declined over the same 2013 to 2025 period, moving together rather than diverging, which is a different and more complicated pattern than the clean cross-sectional split the case study shows.*

Aggregated across all eight tracked quarters, the three deadline huggers average 44.96 days of filing lag (essentially the legal maximum), 37.7% quarterly position turnover, 40.2% value turnover, and 61.1% of positions held in options. The two early filers average 35.69 days of lag, 8.1% position turnover, 16.8% value turnover, and 0% options exposure. Every one of these four measures points the same direction, and none of the four is a restatement of another: turnover, value turnover, and options share are all independently computed from position-level detail, not derived from each other.

## What this doesn't show

Five funds is a real but small, hand-selected sample, chosen deliberately from the extremes of the filing-lag distribution rather than drawn at random, so the size of the gap between groups (4.6 times turnover, 61 percentage points of options exposure) should be read as a demonstration that the extremes differ sharply, not as an estimate of the average effect across the full population of filers. The full-universe annual trend above is the direct evidence that the relationship does not scale up cleanly to all 11,869 institutions in its simplest form. And filing lag alone cannot distinguish a fund that is genuinely delaying strategically from one whose custodian or fund administrator is simply slow; the Simplex Trading exception is a reminder that filing behavior and options exposure do not always travel together at the individual-fund level.

## Conclusion

At the extremes of the filing-lag distribution, the pattern is real and consistent across four independent measures: options-heavy funds that file at the legal deadline turn over their portfolios several times faster than funds that file early. That pattern does not yet extend cleanly to the full population of 13F filers, where deadline-hugging and options exposure have both been declining together rather than the relationship intensifying, and one documented individual exception shows the two behaviors can separate at the fund level. The five-fund result and the full-universe trend are both real; they are simply answering different questions, and only the narrower one currently has a clean answer.

## References

Agarwal, V., Jiang, W., Tang, Y., and Yang, B. (2013). Uncovering hedge fund skill from the portfolio holdings they hide. Journal of Finance, 68(2), 739 to 783.

Kacperczyk, M., Sialm, C., and Zheng, L. (2008). Unobserved actions of mutual funds. Review of Financial Studies, 21(6), 2379 to 2416.

[Repository →](https://github.com/SouparneyaC/Strategic-Opacity-and-the-13F-Disclosure-Window)
