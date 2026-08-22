+++
title = "Strategic disclosure and reported fund size"
+++

# Strategic disclosure and reported fund size

> funds that revise their 13F filings show a real, one-quarter jump in reported size right when the revision happens, and that jump fully disappears within two quarters.

## Abstract and introduction

When an institutional investor amends a previously filed Form 13F, submitting a 13F-HR/A to the SEC, it is correcting or supplementing its own prior disclosure of equity holdings. These amendments are not rare: across 11,869 filers in the QuantKiosk 13F universe from 2013 to 2025, roughly one in four funds has filed at least one. Yet the basic question of whether amending funds look different from non-amending ones, in terms of reported size, had not been asked directly. This page documents a graduate econometrics project (ECON537) built to answer it.

Two reasons this matters. First, 13F disclosure quality underpins most academic and practitioner research on institutional ownership; if amendments are systematically associated with funds that are growing faster, or that manage strategically when their positions become public, then treating amended and unamended filings as equivalent introduces a selection problem. Second, the SEC's 45-day filing window already creates a gap between when a position is taken and when it is disclosed, and an amendment extends that gap further. A staggered difference-in-differences design on 81,121 fund-quarter observations, spanning 4,254 treated and 3,522 matched control funds, estimates the effect of a fund's first amendment on its reported assets under management. The amendment quarter carries a real, statistically significant spike of 1.7 percentage points of extra reported AUM growth, equal to 59% of the treated group's own average pre-period quarterly growth, and that spike fully dissipates within two quarters. The unconditional post-amendment effect is indistinguishable from zero.

## Data

All data comes from the QuantKiosk 13F institutional ownership universe, covering every Form 13F, 13F-NT, and 13F-HR/A filing submitted to SEC EDGAR from 2013 Q2 through 2025 Q1, restricted to filers with at least one quarter of valid, positive reported AUM. A fund is defined as treated in the first calendar quarter it submits an amendment. Of 11,869 unique filers, 4,314 amendment filings were submitted by 2,929 distinct funds, 1.73% of all filings; after excluding funds with missing or zero AUM and applying the event window, the estimation sample contains 4,254 treated funds and 81,121 fund-quarter observations in total.

The outcome variable is the quarter-over-quarter log change in total reported AUM, winsorized at the 1st and 99th percentiles to limit the influence of extreme reporting changes. This captures a mix of real investment returns, net capital flows, and the mechanical reporting effect this project is trying to isolate, a conflation that cannot be separated using 13F data alone and is treated as the analysis's central limitation rather than glossed over. Never-amending funds are matched to treated funds by AUM size decile in the treatment quarter; this addresses the mechanical relationship between fund size and AUM volatility but does not control for fund type, investment strategy, or reporting culture. Treated funds do grow faster even before their first amendment, 2.8 percentage points per quarter in the pre-period against 0.25 for controls, a level difference that reflects genuine selection into amending and is absorbed by fund fixed effects in the regression below rather than left to bias the result.

![Mean quarter-over-quarter change in log fund AUM by event time, treated versus matched control funds](/images/research/strategic-disclosure/fig01_raw_means_aum.png)

*Raw quarterly means of the change in log fund AUM, before any regression adjustment, plotted against event time relative to a fund's first amendment (k = 0). The pre-period level gap between treated and control funds reflects selection and is absorbed by fund fixed effects in the regression. The spike at k = 0 in the treated group, with no corresponding movement in the control group, is the motivating observation for the whole analysis.*

## Literature review

Agarwal, Jiang, Tang, and Yang (2013) study hedge funds that request confidential treatment for certain 13F positions, hiding them from public view until the SEC grants disclosure, and find that these funds earn roughly 6.4 percentage points of annualized alpha above non-confidential funds, with the hidden positions themselves generating most of that outperformance. This project is the amendment analog to their confidential-treatment study: where they study positions intentionally hidden at the original filing, this project studies funds that return afterward to correct what they filed. The mechanisms differ, confidential treatment requests are strategic omissions while amendments may be either strategic additions or genuine clerical corrections, but the underlying question about what disclosure behavior reveals is the same.

Kacperczyk, Sialm, and Zheng (2008) introduce the "return gap," the difference between a fund's reported net returns and the returns implied by its disclosed quarterly holdings, and show that funds with larger return gaps, meaning more happens between disclosure dates than is ever fully observed, consistently earn higher subsequent returns. The AUM-based outcome used here is cruder than their return-gap measure, but the conceptual link is direct: an amendment, like a positive return gap, signals real activity between disclosure windows that only becomes partially visible once it surfaces.

## Models

The theoretical prediction being tested is specific. If amendments are strategic, some share of them reflect a fund building a position, filing its original 13F without that holding or with an understatement, then amending once the accumulation is complete and the advantage from concealment has already been exploited; under this story, the amendment quarter should show a mechanical jump in reported AUM with no necessary improvement in actual performance, since the fund is not growing faster, it is simply revealing more of what it already held. The competing null is that amendments are purely administrative, clerical errors or late custodian data, in which case the amendment-quarter coefficient should be indistinguishable from the pre-period baseline.

The primary specification is a staggered two-way fixed-effects event study:

\[
\Delta \log(\text{AUM})_{it} = \alpha_i + \gamma_t + \sum_{k \neq -1} \beta_k \cdot \mathbf{1}[\text{event\_time}_{it} = k] \cdot \text{Treated}_i + \varepsilon_{it}
\]

where \( \alpha_i \) are fund fixed effects, \( \gamma_t \) are calendar-quarter fixed effects, and the event window runs from \( k = -8 \) to \( k = 8 \) quarters around the first amendment, with \( k = -1 \) as the omitted reference period. Standard errors are clustered at the fund level throughout. Fund fixed effects remove every time-invariant difference between funds, and calendar-quarter fixed effects absorb market-wide shocks common to all funds in a given quarter; what survives is within-fund, within-quarter variation in growth relative to the matched control group. A simple binary difference-in-differences estimator, a single coefficient on a post-treatment indicator, would average over a spike-and-reversion trajectory and mask it entirely: the estimated single POST coefficient here is \( \hat\beta = -0.001 \) (p = 0.68), which on its own would incorrectly suggest no effect at all. The event-study specification is what actually reveals the spike.

Because funds amend at different calendar times, the two-way fixed-effects estimate is a weighted average of every 2x2 difference-in-differences comparison implicit in the panel, including comparisons where already-treated funds serve as controls for later-treated funds. Goodman-Bacon (2021) shows this can bias the estimate when treatment effects are heterogeneous across timing cohorts, exactly the condition here, since the effect is transient rather than permanent. A Goodman-Bacon decomposition assigns 54.0% of identifying weight to the cleaner treated-versus-never-treated comparisons, 16.9% to earlier-versus-later comparisons, and 29.1% to the potentially contaminated later-versus-earlier comparisons, a real limitation reported directly rather than smoothed over; Callaway and Sant'Anna's (2021) heterogeneity-robust estimator is the natural next step to close this gap.

## Backtesting

Four falsification checks test whether the headline result survives scrutiny rather than being an artifact of the research design. A pre-trend Wald test on the joint hypothesis that the eight pre-period coefficients equal zero gives \( F = 1.347 \) (p = 0.233), failing to reject; the parallel-trends assumption the identification strategy relies on cannot be rejected. Manually detrending each fund's own AUM growth series with its own linear trend before re-estimating leaves the spike essentially unchanged, \( \hat\beta_0 = +0.0168 \) (SE = 0.0077, p = 0.028) against the main estimate of \( +0.0172 \), ruling out the concern that amending funds were simply on a different growth trajectory all along. A placebo test that randomly reassigns each treated fund's amendment quarter, drawn from the real empirical distribution of amendment timing, produces a null result at the placebo's own k = 0, \( \hat\beta_0 = -0.012 \) (SE = 0.013, p = 0.33), ruling out the possibility that the spike is a construction artifact. Finally, re-running the entire specification with a fund's options share of AUM as the outcome, instead of total AUM growth, produces no corresponding spike, evidence against the alternative story that the AUM jump is driven by high-notional options positions being reclassified rather than equity positions entering the public record for the first time.

## Results

![Event-study estimates of the effect of a first 13F amendment on log fund AUM, with robustness checks](/images/research/strategic-disclosure/fig03_es_combined.png)

*Event-study coefficients from the main specification (solid), the same specification with fund-specific linear trends removed (dashed), and the placebo specification with randomly reassigned treatment dates (dotted). The pre-period coefficients for both real specifications sit tightly around zero, consistent with the pre-trend test; a sharp spike appears at k = 0 in both real specifications and is absent from the placebo; both real specifications revert toward and below zero afterward.*

The main result: \( \hat\beta_0 = +0.017 \) (SE = 0.008, p = 0.027), a 95% confidence interval of [+0.002, +0.033]. In the quarter a fund first files an amendment, its reported AUM grows 1.7 additional percentage points above its own pre-event average and above the calendar-quarter average for matched controls, a spike equal to 59% of the treated group's typical pre-period quarterly growth. The spike does not persist: by k = 2 the coefficient is \( \hat\beta_2 = -0.013 \) (SE = 0.007), and by k = 4 it is \( \hat\beta_4 = -0.016 \) (SE = 0.007), both below the pre-period baseline rather than merely reverting to it. The aggregate post-period effect, measured as a single coefficient, is statistically indistinguishable from zero. Taken together, a significant spike in the main and trend-adjusted specifications, a flat placebo, and a null aggregate post-effect are all consistent with the strategic disclosure mechanism: the amendment quarter marks the moment previously undisclosed positions enter the public record, producing a one-time jump with no subsequent operational advantage.

## What this doesn't show

The AUM outcome conflates investment returns, net capital flows, and the mechanical reporting effect this analysis is trying to isolate; the ideal outcome, a risk-adjusted return computed from position-level holdings matched to equity prices, would require price data for the full universe of securities held by all 11,869 filers, well beyond the current scope. The control group is matched only on AUM size; differences in fund type, hedge fund against pension fund against mutual fund, investment strategy, and institutional reporting culture remain unaddressed confounders. And the 29.1% of identifying variation flagged by the Goodman-Bacon decomposition as coming from potentially contaminated comparisons means the true effect could differ meaningfully by amendment-timing cohort in a way this specification cannot separate out.

## Conclusion

Amending funds are not a random draw from the population of 13F filers, and their reported size behaves in a way that is hard to explain administratively: a sharp, statistically significant, one-quarter spike at the moment of amendment, surviving four separate falsification checks, that fully dissipates within two quarters and leaves no measurable, lasting advantage. That pattern, spike without persistence, is what the strategic disclosure hypothesis predicts and what a purely clerical explanation does not.

## References

Agarwal, V., Jiang, W., Tang, Y., and Yang, B. (2013). Uncovering hedge fund skill from the portfolio holdings they hide. Journal of Finance, 68(2), 739 to 783.

Callaway, B., and Sant'Anna, P. H. C. (2021). Difference-in-differences with multiple time periods. Journal of Econometrics, 225(2), 200 to 230.

Goodman-Bacon, A. (2021). Difference-in-differences with variation in treatment timing. Journal of Econometrics, 225(2), 254 to 277.

Kacperczyk, M., Sialm, C., and Zheng, L. (2008). Unobserved actions of mutual funds. Review of Financial Studies, 21(6), 2379 to 2416.

*No public repository; this is coursework, not a maintained project.*
