+++
title = "Crowd belief compression in a live forecasting competition"
+++

# Crowd belief compression in a live forecasting competition

> the crowd's own consensus is systematically too cautious — and correcting for that, not building a smarter model, was the actual source of the edge.

## How the scoring works

The competition (JumpTheCrowd, World Cup 2026) asks for a probability, 0–100%, on 10–15 questions per match — everything from the match winner to whether a specific player records a shot on target. Each answer is scored against the actual outcome using a Brier score, then converted to Relative Brier Points (RBP): how much closer to the truth a submission lands than the platform's own crowd-consensus estimate on the same question. RBP is a proper scoring rule — the only way to maximize it, in expectation, is to submit your actual belief. Sandbagging or overstating confidence both cost points.

## The crowd-compression regression

The central, tested claim: the crowd consensus is biased toward the middle. It doesn't fully commit to confident predictions even when the evidence clearly supports one. That claim is a regression, not an impression:

```
crowd_estimate ≈ 0.511 + 0.60 × (our_estimate − 0.5)
```

A slope of 1.0 would mean the crowd tracks a well-researched estimate one-for-one. A slope of 0.60 means the crowd only moves 60 cents for every dollar of conviction the underlying evidence supports — it compresses roughly 40% of the way back toward 50/50, regardless of how one-sided the real evidence is.

This isn't a small-sample artifact. The coefficient was first estimated at n=85 (slope 0.61, wide confidence interval), then re-estimated at n=384 once the group stage finished (slope 0.60, 95% CI [0.559, 0.641], p < 2.2 × 10⁻¹⁶). The slope moved by one point across a 4.5× increase in sample size — the confidence interval tightened without the estimate itself moving, which is what a real, stable effect looks like, as opposed to noise that happened to look structural early on. The 95% CI comfortably excludes both 0 (no compression at all) and 1 (perfect crowd tracking), which is what makes this a statistically robust finding rather than a directional guess.

## Does the record hold up, or is it luck?

![Cumulative RBP by match, with a drawdown panel showing distance from the running peak](/images/research/crowd-belief/cumulative_rbp_drawdown.png)

*Cumulative RBP across 83 settled matches (921 individually-scored questions), plus a drawdown panel showing how far the running total ever fell from its own peak. Total: +3,246.04 RBP. Worst single-match result: −58.35 (Switzerland–Canada, match #43) — a real loss, left in the chart rather than trimmed out.*

The honest caveat that belongs here: separating genuine forecasting skill from luck needs a lot of individually-scored questions — MacKay (2025) derives roughly 100 for a one-standard-deviation separation and roughly 400 for 95% confidence. This campaign's 921 scored questions clear that bar. But this particular chart's own x-axis is the coarser 83-match aggregate, which is a different, noisier unit than the 921-question count the confidence claim is actually about — so the visual trend here is suggestive of skill, not a statistical proof of it by itself.

## Are the submitted probabilities actually calibrated?

![Reliability diagram comparing submitted probabilities to observed outcome frequencies, with a 1000-resample bootstrap band](/images/research/crowd-belief/calibration_reliability.png)

*A reliability diagram, not a track-record chart — a different question from the ones above. It asks: of everything submitted at, say, 70% confidence, did those calls resolve true about 70% of the time? The isotonic-regression curve (CORP-style binning, no manually chosen bin count — Dimitriadis, Gneiting & Raftery 2021, PNAS) tracks the perfect-calibration diagonal closely across the middle of the range, with a 1000-resample bootstrap band showing where the estimate could plausibly be off given the sample size. n=786 individually-scored questions across 72 matches.*

## Testing whether a smarter model would have done better

Two live tests, both against the temptation to add complexity for its own sake:

**Platt scaling** (a standard logistic recalibration correction) was tested at n=246. The fitted correction coefficient (b=0.51, 95% CI [0.12, 1.29]) included b=1 — meaning "no correction needed" was statistically indistinguishable from the fitted correction. Worse, a walk-forward holdout test showed applying Platt scaling actively worsened the Brier score by 0.028, because the correction's own parameters were unstable at that sample size. Decision: don't apply it until the sample is large enough (roughly n≈350) for the correction itself to be trustworthy. Adding a correction that isn't statistically justified would have made the forecasts worse, not better — a real, tested result, not a cautious assumption.

**A machine-learning model** was built and compared against the hand-built statistical approach more than once as the season progressed. It did not outperform the simpler model at this amount of data. This tracks a broader pattern already visible in this dataset: at small sample sizes, a well-specified simple model reliably beats a more flexible one that has more room to overfit.

## Every settled match, not a highlight reel

![Bar chart of RBP for every settled match, colored by sign, best and worst matches annotated](/images/research/crowd-belief/match_rbp_waterfall.png)

*Every one of the 83 settled matches, win or loss, sign-coded and shown in full — the best (Brazil–Norway, +179.27) and worst (Argentina–Austria, −42.23) are labeled, but nothing is excluded from the chart. The point of showing every match is the same point behind grading every submitted question: a track record built by omitting the losses isn't a track record.*

## What this doesn't show

The crowd-compression regression pools every question type into one slope — match winners, player props, count props — on the theory that the average effect is the most defensible current estimate. It's plausible the real compression differs meaningfully by question type (a stratified regression is the natural next step, not yet done). The calibration diagram similarly answers "are these probabilities well-calibrated" as a pooled, question-level question — it doesn't by itself establish that the skill shown in the cumulative chart above is durable rather than lucky; that claim rests on the question count clearing MacKay's threshold, not on any single chart.

[Repository →](https://github.com/SouparneyaC/sportspredict-jtc)
