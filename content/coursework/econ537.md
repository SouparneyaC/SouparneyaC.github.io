+++
title = "ECON537 — Time Series and Applied Economic Methods"
+++

# ECON537 — Time Series and Applied Economic Methods

*A course entry within [Coursework](/coursework/).*

> a class exercise built its own known ground truth first, then tested four estimation methods against it, catching a weak-instrument failure and a small-sample bias in a bootstrap confidence band along the way.

## Overview

ECON537, Time Series and Applied Economic Methods, a graduate course taught by Dr. Jacob A. Robbins, Spring 2026. The course covers time-series foundations, unit roots and ARMA model selection, structural VAR identification, weak instruments, and bootstrap inference, graded through problem sets, a referee report assignment, and a Virtual Paper in place of a final exam.

## What I actually built

Problem Set 3 builds a fully known structural system, two variables, a specified contemporaneous link, simulates data from it, then tests four separate estimation methods against that known truth: reduced-form OLS, two-step instrumental variables, Cholesky identification, and a bootstrap confidence band around the Cholesky impulse response. The IV step surfaces a weak-instrument failure: a first-stage \(R^2\) of 0.0068 sends the estimated coefficient off by more than an order of magnitude, correctly diagnosed as the instrument's fault, not the method's. The bootstrap band on the Cholesky estimate misses the true impact value by a small margin, correctly read as evidence of small-sample bias in that specific estimator rather than a coding mistake.

The referee report assignment applies the same scrutiny to someone else's published work, a formal review of a paper's identification strategy and evidence.

The Virtual Paper, the course's in-lieu-of-final-exam assignment, became an applied paper of its own (below).

## Performance

Every claim in Problem Set 3 is checkable against the known ground truth built into the simulation, and every source of estimation error gets explained mechanically rather than just reported: why OLS is unbiased but noisy here, why weak instruments break IV specifically, why Cholesky's triangular structure encodes its own identifying assumption. Nothing in the write-up is asserted without a reason attached.

## Where this shows up elsewhere

The Virtual Paper became [Strategic disclosure and reported fund size](/research/strategic-disclosure/): a staggered difference-in-differences study of over 81,000 fund-quarter observations asking whether funds that amend a 13F filing look different in reported size from funds that don't. That page carries the full methodology, results, and falsification checks; this entry is the coursework context behind it.

[Back to Coursework →](/coursework/)
