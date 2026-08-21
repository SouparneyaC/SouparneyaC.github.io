+++
title = "Research"
menu = "main"
weight = 2
+++

# Research

## Common ownership and institutional disclosure

> funds that revise their ownership filings show a real, measurable jump in reported size right when the revision happens, not random noise.

I built a measure of how much overlap exists between different investors' stock holdings, essentially how much of the market is owned by the same handful of large funds, using real regulatory filings, after the standard institutional data sources turned out to be inaccessible to me. A related question I looked at: does the *timing* of a fund's ownership disclosures reveal anything real about what's happening inside the fund. It does: funds that file a revision show a genuine, statistically tested spike in their own reported size that quarter, and that result holds up under several honest robustness checks, not just the first pass.

[Repository →](https://github.com/SouparneyaC/Common-Ownership-in-America-QK-Replication)

## [Crowd belief compression in a live forecasting competition](/research/crowd-belief-compression/)

> the crowd's own consensus is systematically too cautious, and correcting for that, not building a smarter model, was the actual source of the edge.

Built a full forecasting system for the Probability Competition FIFA WC2026, run by Jump Trading and Sports Predict. The central finding: the crowd consensus is biased toward uncertainty, holding back from confident predictions even when the evidence clearly supports one, and that bias was stable even after the number of predictions used to measure it doubled. I also tested, rigorously and more than once, whether a machine-learning model could improve on a hand-built statistical approach. It couldn't, at this amount of data, a tested conclusion, not an assumption.

[Repository →](https://github.com/SouparneyaC/sportspredict-jtc)

## Independent quantitative research

> crypto volatility is genuinely predictable; the direction of the next price move is not, and knowing which is which matters more than chasing the harder question.

Self-directed research spanning cryptocurrency markets and derivatives: predicting how much an asset's price will swing (a real, working result) as distinct from predicting which way it'll move (honestly reported as close to unpredictable); how "fat-tailed" extreme price moves really are compared to a standard benchmark; whether a neural network can hedge an option position better than the standard textbook formula; and an adaptive pairs-trading strategy that adjusts itself as market conditions shift.

[Repository →](https://github.com/SouparneyaC/quant-research-lab)

## Prediction market infrastructure

Trading system design for Kalshi and Polymarket prediction markets: real-time data collection, automated market-making logic, and backtesting infrastructure to test strategies before they'd ever run live.

*No public repository, never version-controlled; what survives was recovered from local editor history.*
