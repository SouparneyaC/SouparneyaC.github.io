+++
title = "STAT481 — Applied Statistical Methods II"
+++

# STAT481 — Applied Statistical Methods II

*A course entry within [Coursework](/coursework/).*

> a monster-stats regression project dropped Challenge Rating as a predictor entirely, VIF = 37.28, choosing to explain outcomes through the underlying game mechanics the rating was built from instead.

## Overview

STAT481, Applied Statistical Methods II, Spring 2026, taught by Dr. Jennifer Pajda-De La O, the direct numbered sequel to STAT381 (requires a C or better in it). The course runs categorical data analysis through two-factor and randomized block ANOVA designs, and, later in the semester, logistic and Poisson regression drawn from Kutner's textbook, material beyond the syllabus's own brief unit descriptions.

## What I actually built

Project 1, with a classmate, analyzed a 401-entry Dungeons & Dragons fifth-edition monster dataset. Challenge Rating, the game's own built-in difficulty score, showed a variance inflation factor of 37.28 against the underlying mechanical stats it's derived from, armor class, hit points, attack bonus, and so on. The project dropped Challenge Rating from the model entirely and explained monster outcomes through the mechanics themselves instead of the summary score built from them.

## Performance

A gap surfaced after the first midterm: heavy on practice problems, thin on the underlying theory. That gap got named directly and closed before the second midterm, working the theory from the ground up rather than repeating the same practice-only approach. When Project 1 came back graded low, I went back through the actual submission against the actual rubric, line by line, rather than accept the grade or guess at what might have gone wrong.

## Where this shows up elsewhere

The VIF-driven predictor-elimination habit from Project 1, checking whether a variable is actually adding information or just restating something else in the model, is the same diagnostic that recurs independently in my [transportation mode detection research](/research/transportation-mode-detection/), where six separate feature-selection methods converge on the same conclusion: most of a feature set is redundant, not individually useless.

[Back to Coursework →](/coursework/)
