+++
title = "Research"
menu = "main"
weight = 2
+++

# Research

## [Common ownership and institutional disclosure](/research/common-ownership/)

> the same handful of large asset managers now own most of the stake competing S&P 500 companies place weight on each other's profits through, and that concentration keeps rising.

I built a measure of how much overlap exists between different investors' stock holdings, essentially how much of the market is owned by the same handful of large funds, using real regulatory filings, after the standard institutional data sources turned out to be inaccessible to me. This replicates and extends a real published paper (Backus, Conlon & Sinkinson, 2019) through 2025, and getting an honest number required finding and fixing four separate, real problems in the underlying filing data along the way, not just running the formula once and trusting the output.

[Repository →](https://github.com/SouparneyaC/Common-Ownership-in-America-QK-Replication)

## [Strategic disclosure and reported fund size](/research/strategic-disclosure/)

> funds that revise their 13F filings show a real, one-quarter jump in reported size right when the revision happens, and that jump fully disappears within two quarters.

An ECON537 graduate econometrics paper asking whether funds that amend a previously filed ownership disclosure look different, in reported size, from funds that don't. Using a staggered difference-in-differences design on over 81,000 fund-quarter observations, the amendment quarter shows a real, statistically significant spike in reported assets under management, equal to more than half the treated group's typical quarterly growth, that vanishes within two quarters. The pattern is consistent with strategic disclosure: the amendment reveals previously under-reported positions, rather than reflecting genuine, sustained outperformance.

*No public repository; this is coursework, not a maintained project.*

## [Strategic opacity and the 13F disclosure window](/research/strategic-opacity/)

> the options-heavy funds that file their ownership disclosures right at the legal deadline turn their portfolios over about four to five times faster than funds that file early, exactly the group with the most reason to delay.

A comparison of institutions that consistently file their 13F disclosures right at the legal 45-day deadline against ones that file early. The deadline filers are almost entirely options market makers, and they turn over roughly 38% of their positions per quarter against 8% for the early filers, while holding roughly 61% of their book in options against 0% for the early group. Filing at the legal maximum isn't random: it's concentrated in exactly the funds whose positions change fastest and who have the most to lose by revealing them sooner than required.

[Repository →](https://github.com/SouparneyaC/Strategic-Opacity-and-the-13F-Disclosure-Window)

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
