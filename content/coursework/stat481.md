+++
title = "STAT481 — Applied Statistical Methods II"
+++

# STAT481 — Applied Statistical Methods II

> a monster-stats regression project dropped Challenge Rating as a predictor entirely, VIF = 37.28, choosing to explain outcomes through the underlying game mechanics the rating was built from instead.

## Overview

STAT481, Applied Statistical Methods II, Spring 2026, taught by Dr. Jennifer Pajda-De La O, the direct numbered sequel to STAT381 (requires a C or better in it). The course runs categorical data analysis through two-factor and randomized block ANOVA designs, and, later in the semester, logistic and Poisson regression drawn from Kutner's textbook, material beyond the syllabus's own brief unit descriptions.

## What the course covered

The course carries dual listing, three credit hours for undergraduates and four for graduate students sitting in the same room. Seven units run from a brief review of STAT381's confidence intervals and hypothesis tests straight into the F distribution and operating characteristic curves, tools for evaluating a test's power that STAT381 never covered. Categorical data analysis follows: chi-square tests across multiple groups and proportions, tests of independence run both the standard chi-square way and, for small samples, Fisher's exact test, and a goodness-of-fit test. Regression and generalized linear models come next, then two units of designed experiments, one-factor models with fixed, random, and Latin-square block designs, and two-factor models adding interaction plots and mixed, fixed, and random effects. A final unit on nested and hierarchical designs and full factorial experiments is explicitly contingent on time remaining in the semester.

Grading: homework 22 percent, a Gradescope quiz 8 percent, one project 10 percent, and three exams at 20 percent each.

## What I actually built

Project 1, with a classmate, analyzed a 401-entry Dungeons & Dragons fifth-edition monster dataset. Challenge Rating, the game's own built-in difficulty score, showed a variance inflation factor of 37.28 against the underlying mechanical stats it's derived from, armor class, hit points, attack bonus, and so on. The project dropped Challenge Rating from the model entirely and explained monster outcomes through the mechanics themselves instead of the summary score built from them.

## Performance

A gap surfaced after the first midterm: heavy on practice problems, thin on the underlying theory. That gap got named directly and closed before the second midterm, working the theory from the ground up rather than repeating the same practice-only approach. When Project 1 came back graded low, I went back through the actual submission against the actual rubric, line by line, rather than accept the grade or guess at what might have gone wrong.

## Where this shows up elsewhere

The VIF-driven predictor-elimination habit from Project 1, checking whether a variable is actually adding information or just restating something else in the model, is the same diagnostic that recurs independently in my [transportation mode detection research](/research/transportation-mode-detection/), where six separate feature-selection methods converge on the same conclusion: most of a feature set is redundant, not individually useless.

[Back to Coursework →](/coursework/)
