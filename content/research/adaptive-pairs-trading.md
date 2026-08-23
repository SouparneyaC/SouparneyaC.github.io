+++
title = "Adaptive pairs trading across a structural break"
+++

# Adaptive pairs trading across a structural break

*A research entry within [Research](/research/).*

> the "smart" spread stays within a few units of zero through a 2024 divergence that pushes the naive spread past minus sixty.

## Abstract and introduction

Pairs trading pairs two economically linked assets and bets on their price relationship reverting after it drifts apart. The standard version of that bet assumes the relationship itself is fixed, a single hedge ratio estimated once and held constant, which works until the relationship itself changes. This project tests a pairs trade between the MSCI Australia (EWA) and MSCI Canada (EWC) ETFs, a commodity-linked pair whose two economies have historically moved together, from January 2020 through December 2025, a period that includes exactly the kind of structural break a fixed hedge ratio cannot survive: a divergence beginning in 2024 that pushes Canada's index well above Australia's and never really reverts. A Kalman filter re-estimates the hedge ratio every day instead of fixing it once, and a Random Forest classifier decides whether a given wide spread is actually likely to revert before a trade is placed at all. The result trades only 72 days out of roughly six years, reaches an annualized Sharpe ratio of 0.36, and ends with a cumulative profit of 3.48 units, most of it earned by staying out of the market during the 2024 divergence rather than by trading through it.

## Data

Both ETF price series, plus the CBOE Volatility Index (VIX) as a macro-regime feature, come from Yahoo Finance, daily closes from January 1, 2020 through December 31, 2025. Prices are normalized to a common starting value of 100 so the two series are comparable despite trading at different absolute price levels, following equation (1) in the paper's own notation, \(\tilde P_t = 100 \times (P_t / P_0)\). The final version of the model adds two more series pulled the same way: the front-month crude oil future (CL=F) and the front-month copper future (HG=F), used as five-day trend features rather than price levels.

The structural break the whole paper is built around is visible directly in the raw data, no model needed to see it. The simple spread, EWA's normalized price minus EWC's, oscillates within about ten units of zero from 2020 through 2023. Starting in 2024 it breaks that pattern and falls almost monotonically, reaching close to negative sixty by the end of 2025.

![Normalized EWA and EWC prices (top) and their simple spread (bottom), 2020 through 2025, showing the spread's persistent decline after 2024](/images/research/adaptive-pairs-trading/price_spread_divergence.png)

## Literature review

The Kalman filter itself traces to Kalman (1960),[^kalman] a recursive method for estimating a hidden state that evolves over time from a sequence of noisy observations, originally developed for engineering control problems and adopted widely in finance wherever a relationship between two series is suspected to drift rather than stay fixed. Here the hidden state is the hedge ratio between EWA and EWC.

The "traditional pairs trading" the paper's own introduction argues against, a fixed hedge ratio found once via a cointegration test and held constant, traces to the standard two-step cointegration methodology of Engle and Granger (1987)[^engle-granger] and to the empirical pairs-trading literature that followed it, most notably Gatev, Goetzmann, and Rouwenhorst's (2006)[^gatev] large-sample test of relative-value pairs strategies. Both frameworks assume the long-run relationship between two series is stable enough to estimate once; this project's own Figure above is a direct example of that assumption failing. The classifier gating trade entries is a Random Forest, an ensemble of decision trees introduced by Breiman (2001).[^breiman]

## Models

The hedge ratio is estimated as a state-space model, following equations (2) and (3) in the paper's own notation: the hedge ratio \(\beta_t\) follows a random walk, \(\beta_t = \beta_{t-1} + w_t\), and the observed EWA price relates to EWC's price through that hedge ratio plus measurement noise, \(y_t = X_t\beta_t + v_t\). The Kalman filter recursively updates the estimate \(\hat\beta_{t|t}\) each day using the Kalman gain \(K_t\), per equations (4) and (5). The resulting Kalman-filtered spread, \(S_t^{Kalman} = y_t - \hat\beta_t x_t\), absorbs the drift that would otherwise accumulate in a fixed-ratio spread.[^state-dim]

A trade signal fires when the normalized spread, a rolling ten-day z-score of the Kalman spread per equation (6), exceeds 2 in absolute value. That signal alone isn't enough to trade on; a Random Forest classifier decides separately whether the wide spread is actually likely to close within five days, and a trade is only placed when both conditions hold. The feature set that produces the paper's own reported results, matching equation (7), is five-fold: ten-day spread volatility, the current VIX level, the Kalman spread itself, and five-day trend in both oil and copper futures, the macro leads the paper's introduction frames as the reason a pairs trade this exposed to commodity-linked economies needs a macro signal at all, not just a statistical one.[^iteration] Daily strategy P&L, per equation (8), is the previous day's position multiplied by the day's change in the Kalman spread.

![The naive spread (pink), drifting to nearly negative sixty by 2025, against the Kalman-filtered spread (blue), held near zero throughout by a hedge ratio that adjusts daily](/images/research/adaptive-pairs-trading/kalman_vs_static_spread.png)

## Backtesting

The Random Forest is fit on the first 80 percent of the sample and evaluated on the remaining 20 percent, a single chronological split rather than a walk-forward or repeated cross-validation. The reported Sharpe ratio reflects performance on one held-out period, not an average over several.

The paper directly tests whether trading more often, at a lower conviction bar, would have helped. An alternative configuration lowers the entry threshold from 2 standard deviations to 1.25 and shortens the oil and copper trend window from five days to one, which raises the trade count more than fourfold, from 72 days to 323, while the annualized Sharpe ratio falls from 0.36 to 0.29 and the maximum drawdown widens from -3.54 to -5.10 units. More trades, at lower conviction, cost more than they added.

## Results

| Metric | Value |
|---|---|
| Sample period | 2020 – 2025 |
| Asset pair | EWA / EWC |
| Annualized Sharpe ratio | 0.36 |
| Cumulative return | 3.48 units |
| Maximum drawdown | -3.54 units |
| Active trading days | 72 |
| Maximum peak P&L | 3.73 units |

![The strategy's cumulative P&L, staying flat through most of the sample and compounding gains mainly after 2024, when the ML gatekeeper had the most opportunities to reject non-reverting signals](/images/research/adaptive-pairs-trading/macro_enhanced_equity_curve.png)

Most of the equity curve's gain arrives after 2024, exactly the period the naive spread was diverging hardest. The strategy's selectivity is the mechanism, not a side effect: by trading only 72 days, it avoids the stretches where the Kalman filter's own residuals stop behaving like noise and start behaving like a trend, the "falling knife" a static pairs trade has no way to see coming.

## Limitations

Seventy-two active trading days across six years is a small sample to estimate a Sharpe ratio from, and the figure should be read with that in mind rather than treated as a precise estimate. The strategy is tested on exactly one asset pair; nothing here shows whether the same Kalman-plus-classifier structure holds up on a different commodity-linked pair. The 80/20 split is a single train/test boundary, not repeated across multiple splits, so the out-of-sample result reflects one specific holdout period rather than an average over several. The paper's own conclusion treats the current feature set as a starting point, not a finished one, and names interest rate differentials as the next feature to test.

[^kalman]: R.E. Kalman, "A New Approach to Linear Filtering and Prediction Problems," *Journal of Basic Engineering*, 1960.
[^engle-granger]: Engle, R.F. and Granger, C.W.J., "Co-integration and Error Correction: Representation, Estimation, and Testing," *Econometrica*, 1987.
[^gatev]: Gatev, E., Goetzmann, W.N., and Rouwenhorst, K.G., "Pairs Trading: Performance of a Relative-Value Arbitrage Rule," *Review of Financial Studies*, 2006.
[^breiman]: Breiman, L., "Random Forests," *Machine Learning*, 2001.
[^state-dim]: The paper's own equations describe a single time-varying hedge ratio. The actual fitted model estimates a two-dimensional state, a time-varying hedge ratio and a time-varying intercept together, not the hedge ratio alone; the extra intercept term is what lets the filtered spread re-center near zero rather than just re-scale.
[^iteration]: Two earlier scripts in the same project fit the classifier without the oil and copper features, using only spread volatility, VIX, and the Kalman spread. The oil-and-copper feature set is the one that produced the paper's own reported Sharpe ratio, confirmed directly against the script's own saved output; the alternative configuration in the backtesting comparison above was run by changing that same script's threshold and lag parameters rather than saved as a separate file.

[Repository →](https://github.com/SouparneyaC/adaptive-pairs-trading)

[Back to Research →](/research/)
