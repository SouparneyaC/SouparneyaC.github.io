+++
title = "Research"
menu = "main"
weight = 2
+++

# Research

**QuantKiosk**

Built a research pipeline on SEC 13F institutional-holdings data from the QuantKiosk API, covering 311 S&P 500 firms from 2013 through 2025, and audited the raw filing data — correcting XBRL unit errors, post-split share restatements, and an amendment double-count, then repairing corrupted security identifiers with a fuzzy entity-resolution step validated at 100% precision — before computing any measure on top of it. That pipeline became three projects:  
[Common ownership and institutional disclosure](/research/common-ownership/)  
[Strategic disclosure and reported fund size](/research/strategic-disclosure/)  
[Strategic opacity and the 13F disclosure window](/research/strategic-opacity/)

**[SportsPredict.com — Jump Trading Probability Competition, FIFA World Cup 2026](/research/crowd-belief-compression/)**

Built a statistical forecasting pipeline for 49,000+ international matches back to 1872, ingesting live match data and real-time odds from exchange APIs, computing point-in-time Elo ratings to remove look-ahead bias. Modeled goal rates with a Poisson regression using a Dixon-Coles low-score correction and Negative Binomial overdispersion, alongside an ordered logit match-result model. Quantified a structural compression of the crowd consensus toward 50% and finished in the top 1.1% of ~3,900 forecasters.

[Repository →](https://github.com/SouparneyaC/sportspredict-jtc)

**[Detecting Transportation Mode from Smartphone Sensors](/research/transportation-mode-detection/)**

Built a classification pipeline on the TMD smartphone-sensor dataset (Carpineti et al., 2018) to identify transportation mode from 36 features across nine sensors, with Michelle Segura. Benchmarked nine classifiers on an identical train/test split, from classification trees and PLS-DA through random forests and XGBoost, with tuned XGBoost reaching 97.29% test accuracy. Ran six feature-selection methods — ANOVA, PCA, LASSO, Boruta, RFE, and conditional permutation importance — and showed 14 of the 36 features recover near-peak accuracy.

[Repository →](https://github.com/SouparneyaC/transportation-mode-detection)

**[Adaptive Pairs Trading Across a Structural Break](/research/adaptive-pairs-trading/)**

Designed and backtested a two-layer regime-adaptive pairs-trading strategy on the MSCI Australia and MSCI Canada ETFs: a Kalman filter re-estimates the hedge ratio daily instead of fixing it once, gated by a Random Forest classifier that decides whether a wide spread is actually likely to revert before a trade is placed. The strategy trades only 72 days across six years, reaches an annualized Sharpe ratio of 0.36, and preserves capital through a 2024 structural divergence a fixed hedge ratio has no way to see coming.

[Repository →](https://github.com/SouparneyaC/adaptive-pairs-trading)

**[Crypto microstructure: Forecasting to beat the naive average](/research/crypto-microstructure/)**

Found and quantified a real predictability asymmetry across Bitcoin, Ethereum, Solana, and Dogecoin at one-minute resolution: price direction sits at essentially chance, every classifier landing within a point or two of a coin flip, while the size of the next move is forecastable — a Ridge regression reaches an out-of-sample R² of 0.274 for Bitcoin, and the autocorrelation of absolute returns stays above 0.25 even thirty minutes out. Published as a formal research package built entirely independently, with no course or competition requiring it.

[Repository →](https://github.com/SouparneyaC/crypto-microstructure-predictability-gap)

**[Prediction market infrastructure: Kalshi and Polymarket](/research/prediction-market-infrastructure/)**

Trading system design for Kalshi and Polymarket prediction markets: a self-healing, unattended data-collection pipeline, two independently-derived barrier-crossing fair-value models, an Avellaneda-Stoikov market maker, and a half-Kelly capital allocator. No backtest or live result exists, traced directly to a scheduled collector job that never executed successfully.
