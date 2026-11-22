+++
title = "Research"
menu = "main"
weight = 2
+++

# Research

**[Designing a low-latency market data system](/research/prediction-market-infrastructure/)**

Built a live order book service for Kalshi's prediction markets: one Python process on a $12-a-month server in the same Amazon region as the exchange, with no database in the path. A book is updated a median 0.01 ms after the process receives a message, a sequence counter finds lost updates and a fresh snapshot repairs the book, and a timer on every stage from the exchange's timestamp to the browser feeds a once-a-minute measurement store. Measured with a 48-hour test, an overnight capacity test up to 1,000 books and an evening CPU profile.

[Repository →](https://github.com/SouparneyaC/kalshi-orderbook-dashboard)

**[Detecting Transportation Mode from Smartphone Sensors](/research/transportation-mode-detection/)**

Built a classification pipeline on the TMD smartphone-sensor dataset (Carpineti et al., 2018) to identify transportation mode from 36 features across nine sensors. Benchmarked nine classifiers on an identical train/test split, from classification trees and PLS-DA through random forests and XGBoost, with tuned XGBoost reaching 97.29% test accuracy. Ran six feature-selection methods — ANOVA, PCA, LASSO, Boruta, RFE, and conditional permutation importance — and showed 14 of the 36 features recover near-peak accuracy.

[Repository →](https://github.com/SouparneyaC/transportation-mode-detection)

**[SportsPredict.com — Jump Trading Probability Competition, FIFA World Cup 2026](/research/crowd-belief-compression/)**

Built a statistical forecasting pipeline for 49,000+ international matches back to 1872, ingesting live match data and real-time odds from exchange APIs, computing point-in-time Elo ratings to remove look-ahead bias. Modeled goal rates with a Poisson regression using a Dixon-Coles low-score correction and Negative Binomial overdispersion, alongside an ordered logit match-result model. Quantified a structural compression of the crowd consensus toward 50% and finished in the top 1.1% of ~3,900 forecasters.

[Repository →](https://github.com/SouparneyaC/sportspredict-jtc)

**QuantKiosk**

Built a research pipeline on SEC 13F institutional-holdings data from the QuantKiosk API, covering 311 S&P 500 firms from 2013 through 2025, and audited the raw filing data — correcting XBRL unit errors, post-split share restatements, and an amendment double-count, then repairing corrupted security identifiers with a fuzzy entity-resolution step validated at 100% precision — before computing any measure on top of it. That pipeline became three projects:  
[Common ownership and institutional disclosure](/research/common-ownership/)  
[Strategic disclosure and reported fund size](/research/strategic-disclosure/)  
[Strategic opacity and the 13F disclosure window](/research/strategic-opacity/)

**[Adaptive Pairs Trading Across a Structural Break](/research/adaptive-pairs-trading/)**

Designed and backtested a two-layer regime-adaptive pairs-trading strategy on the MSCI Australia and MSCI Canada ETFs: a Kalman filter re-estimates the hedge ratio daily instead of fixing it once, gated by a Random Forest classifier that decides whether a wide spread is actually likely to revert before a trade is placed. The strategy trades only 72 days across six years, reaches an annualized Sharpe ratio of 0.36, and preserves capital through a 2024 structural divergence a fixed hedge ratio has no way to see coming.

[Repository →](https://github.com/SouparneyaC/adaptive-pairs-trading)

**[Crypto microstructure: Forecasting to beat the naive average](/research/crypto-microstructure/)**

Found and quantified a real predictability asymmetry across Bitcoin, Ethereum, Solana, and Dogecoin at one-minute resolution: price direction sits at essentially chance, every classifier landing within a point or two of a coin flip, while the size of the next move is forecastable — a Ridge regression reaches an out-of-sample R² of 0.274 for Bitcoin, and the autocorrelation of absolute returns stays above 0.25 even thirty minutes out. Published as a formal research package built entirely independently, with no course or competition requiring it.

[Repository →](https://github.com/SouparneyaC/crypto-microstructure-predictability-gap)
