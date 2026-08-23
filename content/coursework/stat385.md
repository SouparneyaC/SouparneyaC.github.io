+++
title = "STAT385 — Elementary Statistical Techniques for Machine Learning and Big Data"
+++

# STAT385 — Elementary Statistical Techniques for Machine Learning and Big Data

*A course entry within [Coursework](/coursework/).*

> a live methodology bug in the final project's modeling pipeline was caught only because a fourth, independently-written script disagreed with the number every other script had been trusting.

## Overview

STAT385, Elementary Statistical Techniques for Machine Learning and Big Data, Summer 2026, an online course held over Zoom and taught by Ping-Shou Zhong, meeting Monday and Wednesday for lecture and Friday for a computer lab, an accelerated eight-week summer course built around machine learning content following James, Witten, Hastie, and Tibshirani's *An Introduction to Statistical Learning*, a different arc from the applied classical statistics of the STAT381/382/481 sequence. Grades split four ways: homework 25 percent, a midterm exam 20 percent, a required team project 30 percent, and in-class exercises and participation 25 percent.

## What the course covered

The first week sets up the framework the rest of the course builds on: supervised versus unsupervised learning, the \(Y = f(X) + \varepsilon\) model, and how to actually judge whether a fitted model is any good, the bias-variance decomposition of test error. A one-page proof that the conditional mean is the best predictor under squared-error loss is the actual theoretical justification for treating regression as "estimate \(f\)" for everything that follows.[^best-pred]

Linear regression and subset selection came next: fitting a straight-line model, then choosing which predictors actually belong in it, judged by Mallow's Cp, AIC, BIC, and adjusted R², not raw R², worked on a 122-feature community dataset predicting violent crime rates.

Classification took over the following two weeks. Logistic regression predicts a class probability directly. Linear Discriminant Analysis instead assumes each class's predictors follow a Gaussian distribution with a shared shape and classifies a point by which class's distribution it looks most likely to have come from. Naive Bayes makes the same Bayes'-rule argument but assumes the predictors are independent of each other given the class, a simplification that trades some accuracy for speed in high dimensions. K-nearest neighbors skips fitting a model at all and classifies a point by a vote among its closest neighbors in the training data. All four were compared side by side on the same datasets, blood-test features for hemophilia carrier detection, gene-expression data for a breast-cancer classification lab, alongside cross-validation and the bootstrap for estimating how much any of those comparisons could actually be trusted. Support vector machines, linear and kernelized, came right after the midterm, explicitly excluded from it.

The back half covers regularization, dimension reduction, and tree-based methods. Ridge regression and LASSO both shrink a regression's coefficients toward zero to fight overfitting; LASSO, derived both through soft-thresholding and through the Least Angle Regression path algorithm, can push a coefficient all the way to zero and drop it from the model, ridge only ever shrinks it. PCA finds the axes that capture the most variance in the predictors alone; Principal Component Regression fits on those axes instead of the raw predictors, and Partial Least Squares builds its own axes using the response variable too, not just the predictors. Classification trees split on entropy and information gain; bagging averages many trees, each grown on a resampled version of the data, to cut variance, and boosting instead grows trees one at a time, each one correcting what the ensemble so far got wrong. One counterintuitive result from this stretch: on a radar-signal dataset, bagging without pruning individual trees beat bagging with pruning, 3.8% error against 7.5%, because averaging over many high-variance trees is the entire point of bagging in the first place. The course closes with K-means and hierarchical clustering.

[^best-pred]: The proof: if \(\hat f(x)\) minimizes \(E\{(Y-f(x))^2\}\), adding and subtracting \(E(Y|x)\) inside the square shows the cross term vanishes, leaving \(E\{(Y-\hat f(x))^2\} = E\{(Y-E(Y|x))^2\} + E\{(E(Y|x)-\hat f(x))^2\}\), minimized exactly when \(\hat f(x) = E(Y|x)\).

## The final project

The course's final project, with Michelle Segura, is a full applied machine learning study of a smartphone sensor dataset: which of 36 features actually carry information about a person's transportation mode, and which classifier identifies it best. Six feature-selection methods, nine classification methods, and a full validation pass make up the final report.

## Performance

Three separately written scripts each fit their final model on the wrong data subset, and the error went unnoticed until a fourth, independently-written script disagreed with the number every other script had been trusting. It was found and corrected before the project shipped.

## Where this shows up elsewhere

The full paper lives at [Detecting transportation mode from smartphone sensors](/research/transportation-mode-detection/): the complete data, literature review, models, validation, and results.

[Repository →](https://github.com/SouparneyaC/transportation-mode-detection)

[Back to Coursework →](/coursework/)
