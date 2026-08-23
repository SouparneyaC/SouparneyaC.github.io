+++
title = "ECON537 — Time Series and Applied Economic Methods"
+++

# ECON537 — Time Series and Applied Economic Methods

*A course entry within [Coursework](/coursework/).*

> a class exercise built its own known ground truth first, then tested four estimation methods against it, and caught the exact reason two of them got the wrong answer.

## Overview

ECON537, Time Series and Applied Economic Methods, a PhD-level econometrics seminar taught by Professor Jacob A. Robbins, Spring 2026, open to undergraduates with the instructor's permission. Twice-weekly sessions built around assigned readings, with an explicit expectation that students arrive having read the paper and ready to critique it, not just summarize it. Grades split three ways: two problem sets plus a short in-class presentation for 30 percent, a referee report on a published paper for 20 percent, and a Virtual Paper, the course's substitute for a final exam, for the remaining half.

## What the course covered

Five lecture blocks carried the technical core: an introduction, a review of the mathematical preliminaries, ARMA models, forecasting, and a fifth block on multivariate time series and vector autoregressions, the model class most of the later problem sets build from.[^lectures]

The first problem set stayed on paper: identify what kind of process a given equation describes, work out when it settles down versus wanders off, its stationarity conditions, and sketch by hand what its autocorrelation pattern should look like.

The second problem set moved the same ideas onto the Federal Reserve's own real GDP series. The level of GDP doesn't settle down on its own; its autocorrelation fades so slowly, and so close to a straight line, that the standard diagnosis is a unit root, a series whose shocks never really fade out. Fitting an AR(1) model to it directly gives a coefficient of about 0.998, technically inside the range that counts as stable but close enough to the edge that the series behaves like a near-random walk in practice. Removing the trend and choosing a model for what's left produced a disagreement between two standard model-selection rules: AIC preferred a relatively rich model, BIC preferred none at all, essentially concluding that GDP growth is indistinguishable from noise. The two rules disagree because BIC penalizes extra parameters more harshly as the sample grows. The assignment's own answer sided with BIC's simpler model as the safer choice for predicting new data.

The third problem set was the most demanding of the three. It starts by making up a small, fully known economic system, two variables, one of which affects the other within the same period, not just with a lag, then generating fake data from that known system. Because the true relationship is known by construction, every method used afterward to try to recover it can be graded exactly rather than trusted on faith. A plain regression using only lagged values gets one coefficient's sign wrong, a sampling-noise artifact given how weak the true relationship is to begin with, not a sign anything is broken. A more targeted technique, instrumental variables, isolates a same-period effect by finding a stand-in variable related to the cause but not directly to the outcome; here it does much worse, since the stand-in barely predicts the thing it's supposed to represent, and the resulting estimate comes out more than ten times too small, a weak instrument, not a broken method. A third approach, Cholesky identification, assumes a specific order in which the two variables affect each other within the same period and uses that assumption to back out the whole system from how the variables move together; it recovers the true relationship closely. A bonus question builds a confidence interval around that last estimate by resampling the simulated data thousands of times, and finds the true value sitting just outside the resulting range, a small-sample quirk of that specific estimator, not an error in the exercise.

A separate assignment asked for a short in-class presentation of one result pulled from a current paper not on the syllabus. The referee report assignment applied the same kind of scrutiny to someone else's finished work: a full review of a published paper on options-market volatility and voluntary corporate disclosure, identifying three separate ways the paper's central estimate could be biased, a timing mismatch in when volatility gets measured relative to an earnings announcement, a plausible alternative explanation the paper never rules out, and a measurement concern specific to how options data behaves, each paired with a concrete, implementable fix rather than a general call for more robustness checks.

The Virtual Paper, the course's substitute for a final exam, became an applied paper of its own (below).

[^lectures]: Course identity and lecture sequence confirmed directly from the instructor's own slide decks, not just the syllabus's stated topic list: an introduction, mathematical preliminaries, ARMA models, prediction, and a 72-slide block on multivariate models and VARs.

## Performance

Both major written assignments hold up against something outside themselves rather than a grader's trust. The structural VAR problem set is checkable line by line against the true values built into its own simulation. The referee report is checkable against the paper it reviews and against the outside sources it leans on to back up each critique, Rogers et al. (2009) for the omitted-variable concern, Muravyev (2016) for the microstructure concern, not just asserted from general familiarity with the topic.

## Where this shows up elsewhere

The Virtual Paper became [Strategic disclosure and reported fund size](/research/strategic-disclosure/): a staggered difference-in-differences study of over 81,000 fund-quarter observations asking whether funds that amend a 13F filing look different in reported size from funds that don't. That page carries the full methodology, results, and falsification checks; this entry is the coursework context behind it.

[Back to Coursework →](/coursework/)
