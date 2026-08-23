+++
title = "STAT382 — Applying SAS to Data"
+++

# STAT382 — Applying SAS to Data

*A course entry within [Coursework](/coursework/).*

> a placeholder p-value from an early draft nearly went out wrong, caught only by comparing it against the actual regression output: 0.01743 for a result that was really p<2.2e-16.

## Overview

STAT382, Applying SAS to Data, Spring 2026, coordinated by Dr. Jennifer Pajda-De La O with sections taught by Dr. Duha Hamed and Dr. Dale Embers, the second course in the STAT381→382→481 sequence. The course is two tools built around one statistical arc: Units 1 through 4 extend STAT381's material into R, data management, graphics, regression, ANOVA, then Unit 5 pivots the identical statistical content onto SAS for the back half of the semester.

## What I actually built

Both graded Projects reuse the same medical dataset. Project 1, in R, stays descriptive. Project 2 extends it into inferential simple linear regression, pulse rate against age, in SAS, the same dataset carried across the language switch on purpose. A one-way ANOVA assignment tests whether NFL players' ages differ by position group, Defense, Offense, Special Teams, using a 2016 NFL dataset.

## Performance

A post-exam error analysis, built directly from the graded first exam, broke every lost point down by type rather than leaving it as a vague total: `rbind`/`cbind` confusion, `FALSE` written as `false`, a stray backslash in a file path, `==` used where `<-` was needed, missing commas in a vector. That breakdown fed directly into a self-made cheat sheet for the SAS final. Separately, a leftover placeholder Levene's-test p-value from an early homework draft got caught and corrected against the actual regression output before submission.

## Where this shows up elsewhere

The same simple-linear-regression skill this course applies in SAS, fit a model, check residuals, test significance, is what STAT381's Worksheet 6 previewed and what STAT481 goes further with. The habit of turning a graded mistake into a categorized, reusable checklist rather than just moving on shows up again later, in a live methodology bug caught and corrected in my [transportation mode detection research](/research/transportation-mode-detection/).

[Back to Coursework →](/coursework/)
