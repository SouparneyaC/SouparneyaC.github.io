+++
title = "Crypto microstructure: the predictability gap"
+++

# Crypto microstructure: the predictability gap

> the size of Bitcoin's next one-minute move is forecastable well enough to beat a naive average; which direction it moves in is not.

## Abstract and introduction

Two separate questions can be asked of the same minute-by-minute price data: which way will the price move next, and how much will it move, regardless of direction. This project asks both, on four cryptocurrencies at one-minute resolution using nothing but each coin's own price and volume history, no news, no order book, no on-chain data, and finds an asymmetry between them. Direction sits at essentially chance: a Logistic Regression and a Random Forest both land within a percentage point or two of 50% accuracy across Bitcoin, Ethereum, Solana, and Dogecoin. Magnitude does not. A Ridge regression predicting the size of the next minute's move reaches an out-of-sample R² of 0.274 for Bitcoin and 0.221 for Ethereum, and the autocorrelation of absolute returns stays above 0.25 even thirty minutes out. Volatility has memory a trader could act on; direction, on this evidence, does not.

## Data

One-minute open/high/low/close/volume bars, pulled directly from OKX's public exchange API, for four symbols, each covering a different history rather than one shared window: Bitcoin from October 2024 through November 2025 (586,800 rows), Ethereum from August 2023 (1,183,500 rows), Solana from September 2020, the deepest history of the four (2,701,394 rows), and Dogecoin, the shortest, from June 2025 (222,900 rows). All four series end within the same week in November 2025, when the data was pulled.

## Literature review

Volatility clustering, the observation that large price moves tend to be followed by more large moves, of either sign, is one of the oldest documented regularities in financial return series, traced to Mandelbrot (1963).[^mandelbrot] The standard way to formalize that memory is the ARCH/GARCH family, Engle (1982)[^arch] and Bollerslev (1986),[^garch] which models the conditional variance of a return series as a function of its own past squared or absolute values. This project doesn't fit a GARCH model directly; it tests the same underlying claim non-parametrically, letting a Ridge regression and a Random Forest use rolling volatility, momentum, and volume-based features to predict the size of the next move, at a frequency and on an asset class GARCH's original applications never covered. The competing claim, that returns themselves should be close to unpredictable, follows from the efficient market hypothesis (Fama, 1970);[^fama] the direction results here are consistent with it.

## Models

Two feature groups feed both problems. The first is a standard return-based set: the one-minute return itself, rolling volatility over 5, 15, and 30-minute windows, and price momentum over the same three windows. The second is built around how the trades themselves behave rather than just the price level: the sign of each one-minute return, a directional run-length counting how many consecutive minutes the price has moved the same way, volume signed by that same direction, a 60-minute volume z-score, and 60-minute realized volatility. Two targets are built from the same forward one-minute return: a binary direction label, whether the next minute's return is positive, and the absolute value of that same return, the magnitude target.

Both targets share an identical feature set and an identical 70/15/15 train, validation, and test split, chronological rather than shuffled, with feature scaling fit only on the training period so no information from the validation or test window leaks into the transform. Direction is modeled with a Logistic Regression and a Random Forest Classifier; magnitude is modeled with a Ridge Regression and a Random Forest Regressor.

## Backtesting

Every model is scored only on the final 15 percent of each asset's own history, data held out from both fitting and any tuning decision, in time order rather than a random split. This project stops at measuring predictive accuracy; it does not convert either model into a trading rule with an entry threshold or position sizing, unlike the pairs-trading project above.[^no-pnl]

A sharper, more direct test of the volatility claim groups the Bitcoin test period into five quintiles by how volatile the market was right before each prediction, then checks the average size of what actually happened next. The relationship is close to monotonic: the average absolute next-minute return rises from 0.00018 in the calmest quintile to 0.00079 in the most volatile one, a roughly 4.5-times difference an unconditional average across the whole period would completely miss.

![Bitcoin's rolling 60-minute return volatility over a three-day window, showing volatility arriving in clusters rather than at a constant level](/images/research/crypto-microstructure/btc_rolling_volatility.png)

## Results

**Direction (Table 1)**

| Symbol | Model | Accuracy | F1 | Signal |
|---|---|---|---|---|
| BTCUSDT | Logistic Regression | 0.502 | 0.49 | Noise |
| ETHUSDT | Logistic Regression | 0.498 | 0.48 | Noise |
| SOLUSDT | Random Forest | 0.511 | 0.50 | Weak |
| DOGEUSDT | Random Forest | 0.505 | 0.49 | Noise |

**Volatility magnitude (Table 2)**

| Symbol | Model | R² | RMSE | Persistence |
|---|---|---|---|---|
| BTCUSDT | Ridge | 0.274 | 0.00012 | High |
| ETHUSDT | Ridge | 0.221 | 0.00015 | High |
| SOLUSDT | Random Forest | 0.198 | 0.00019 | Medium |
| DOGEUSDT | Random Forest | 0.172 | 0.00025 | Medium |

![Autocorrelation of Bitcoin's absolute one-minute forward return, staying above 0.25 through a 30-minute lag](/images/research/crypto-microstructure/btc_acf_abs_returns.png)

No direction model clears 51.1%, against a 50% coin-flip baseline; the magnitude models, in contrast, explain over a quarter of Bitcoin's next-minute move size out of sample, and the autocorrelation plot above shows why: the memory in absolute returns barely decays across the full 30-minute window tested, starting near 0.37 at a one-minute lag and still above 0.25 at thirty.

## Limitations

The feature set here is built entirely from OHLCV bars, open, high, low, close, and traded volume, not the limit-order-book data, bid-ask depth, order flow imbalance, quote-level updates, that market microstructure research classically studies; the project's own title should be read with that scope in mind. The four assets carry different amounts of history, from Dogecoin's five months to Solana's five years, so the results aren't a comparison across one uniform window. That absolute returns are easier to predict than signed returns is itself a well-established statistical regularity, not something unique to crypto markets; this project confirms it holds here, at one-minute resolution, rather than discovering it. Finally, no result here has been tested as an actual trading rule net of transaction costs and slippage, only as a prediction-accuracy exercise.

[^mandelbrot]: Mandelbrot, B., "The Variation of Certain Speculative Prices," *Journal of Business*, 1963.
[^arch]: Engle, R.F., "Autoregressive Conditional Heteroscedasticity with Estimates of the Variance of United Kingdom Inflation," *Econometrica*, 1982.
[^garch]: Bollerslev, T., "Generalized Autoregressive Conditional Heteroskedasticity," *Journal of Econometrics*, 1986.
[^fama]: Fama, E.F., "Efficient Capital Markets: A Review of Theory and Empirical Work," *Journal of Finance*, 1970.
[^no-pnl]: The [adaptive pairs-trading project](/research/adaptive-pairs-trading/) does convert a statistical signal into an executed trading rule with a reported Sharpe ratio; this project's scope stops one step earlier, at whether the underlying signal exists at all.

[Repository →](https://github.com/SouparneyaC/crypto-microstructure-predictability-gap)

[Back to Research →](/research/)
