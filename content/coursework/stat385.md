+++
title = "STAT385 — Elementary Statistical Techniques for Machine Learning and Big Data"
+++

# STAT385 — Elementary Statistical Techniques for Machine Learning and Big Data

*A course entry within [Coursework](/coursework/).*

> a live methodology bug in the final project's modeling pipeline was caught only because a fourth, independently-written script disagreed with the number every other script had been trusting.

## Overview

STAT385, Elementary Statistical Techniques for Machine Learning and Big Data, Summer 2026, taught by Ping-Shou Zhong, an accelerated eight-week summer course built around machine learning content following James, Witten, Hastie, and Tibshirani's *An Introduction to Statistical Learning*, a different arc from the applied classical statistics of the STAT381/382/481 sequence.

## What the course covered

The first stretch sets up the framework the rest of the semester builds on: supervised versus unsupervised learning, the \(Y = f(X) + \varepsilon\) model, and the bias-variance decomposition of test error. A one-page proof that the conditional mean is the best predictor under squared-error loss is the actual theoretical justification for treating regression as "estimate \(f\)" for everything that follows.[^best-pred] From there: least-squares regression and subset selection, best subset, forward and backward stepwise, judged by Mallow's Cp, AIC, BIC, and adjusted R², not raw R², worked on a 122-feature community dataset predicting violent crime rates.

Classification came next: logistic regression, multinomial logistic regression, Linear Discriminant Analysis, Naive Bayes, and K-nearest neighbors, compared side by side on the same datasets, blood-test features for hemophilia carrier detection, gene-expression data for a breast-cancer classification lab, alongside cross-validation and the bootstrap for estimating how much any of those results could actually be trusted. Support vector machines, linear and kernelized, came right after the midterm, explicitly excluded from it.

The back half covers regularization and dimension reduction, ridge regression, LASSO derived both through soft-thresholding and through the Least Angle Regression path algorithm, PCA, and Partial Least Squares, then tree-based methods: classification trees built from entropy and information gain, bagging and random forests, and boosting from AdaBoost through gradient boosting. One counterintuitive result from that stretch: on a radar-signal dataset, bagging without pruning individual trees beat bagging with pruning, 3.8% error against 7.5%, because averaging over many high-variance trees is the entire point of bagging in the first place. The course closes with K-means and hierarchical clustering.

[^best-pred]: The proof: if \(\hat f(x)\) minimizes \(E\{(Y-f(x))^2\}\), adding and subtracting \(E(Y|x)\) inside the square shows the cross term vanishes, leaving \(E\{(Y-\hat f(x))^2\} = E\{(Y-E(Y|x))^2\} + E\{(E(Y|x)-\hat f(x))^2\}\), minimized exactly when \(\hat f(x) = E(Y|x)\).

## The final project

The course's final project, with Michelle Segura, is a full applied machine learning study of a smartphone sensor dataset: which of 36 features actually carry information about a person's transportation mode, and which classifier identifies it best. Six feature-selection methods, nine classification methods, and a full validation pass make up the final report.

## Performance

Three separately written scripts each fit their final model on the wrong data subset, and the error went unnoticed until a fourth, independently-written script disagreed with the number every other script had been trusting. It was found and corrected before the project shipped.

## Where this shows up elsewhere

The full paper lives at [Detecting transportation mode from smartphone sensors](/research/transportation-mode-detection/): the complete data, literature review, models, validation, and results.

[Back to Coursework →](/coursework/)
