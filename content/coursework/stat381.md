+++
title = "STAT381 — Applied Statistical Methods I"
+++

# STAT381 — Applied Statistical Methods I

*A course entry within [Coursework](/coursework/).*

> a two-sample confidence interval built with the wrong variance assumption got corrected mid-course, from pooled to Welch's unequal-variance method, the same distinction that turns up again in an unrelated computer-science project a semester later.

## Overview

STAT381, Applied Statistical Methods I, Fall 2025, co-taught by Dr. Jennifer Pajda-De La O and Dr. Duha Hamed, the first course in a three-course sequence under Pajda-De La O that continues into STAT382 and STAT481. The syllabus runs one connected arc: probability foundations, discrete random variables, continuous random variables, sampling distributions, then confidence intervals and hypothesis testing. Coursework: three exams, online homework, five R Projects built around Swirl modules, six graded worksheets, and a final project.

## What I actually built

Six R-script worksheets, each built around its own dataset: laptop specs, 2019 MLB batting stats, a "Future Optimism" survey, an animal gestation-and-height regression. Worksheet 6 runs a full simple-linear-regression workflow, Pearson's r, residual diagnostics, a significance test, even though regression was never formally part of the syllabus's own unit list, an early preview of what STAT481 goes deeper into with the same instructor.

R Project 5 uses the Central Limit Theorem against a 5,000-row Chicago taxi-trip dataset, working through the sampling distribution of the mean.

The final project, worked individually, uses a 257-row Pokémon dataset, base stats, region, evolution stage, to build a confidence interval and run a hypothesis test comparing base Attack stat between early- and late-evolution Pokémon.

## Performance

Midway through the confidence-interval unit, an initial two-sample interval built with a pooled, equal-variance assumption turned out to be wrong for the specific problem, which called for the unequal-variance, Welch approach instead. The standard error, degrees of freedom, and critical value all changed once that was caught, a correction to the actual method, not a typo fix. All six worksheets, five R Projects, the final project, and three exams were completed across the semester.

## Where this shows up elsewhere

The pooled-versus-Welch distinction caught here is the same one that turns up, unnamed, in an [MCS160 programming project](/coursework/mcs160/) the following semester: a "hedge fund strategy comparison" assignment that implements a two-sample significance test from scratch. STAT481, taught by the same instructor, goes considerably further into the regression territory Worksheet 6 previewed.

[Back to Coursework →](/coursework/)
