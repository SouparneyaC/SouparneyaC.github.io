+++
title = "MATH320 — Linear Algebra"
+++

# MATH320 — Linear Algebra

*A course entry within [Coursework](/coursework/).*

> the same eigenvalue-eigenvector pipeline used on an open-book linear algebra final turns out to be exactly what PCA and a common-ownership profit-weight formula are built on, in two unrelated research projects.

## Overview

MATH320, Linear Algebra, Spring 2026, taught by Dr. İzzet Coşkun. The course's own topic map runs eigenvalues through diagonalization: starting from a matrix \(A\), solve \(\det(A-\lambda I)=0\) for eigenvalues, find eigenvectors from \(\ker(A-\lambda I)\), then branch on whether algebraic and geometric multiplicity match. If they do, the matrix diagonalizes as \(A = PDP^{-1}\); if not, Jordan canonical form. A separate branch covers symmetric matrices: Gram-Schmidt orthogonalization, then an orthogonal \(P\) with \(P^TAP=D\). Determinants are treated geometrically, the volume-scaling factor of the linear map, alongside systems of linear equations with four and five unknowns.

## What I actually built

An open-book final meant the preparation work was building the cheat sheet itself, not memorizing formulas. My own study material tracked a specific graded pattern from the midterm directly: informal proofs lost marks, every proof needed a theorem cited by name, and fed that lesson into how the final-exam sheet was organized.

## Performance

A self-diagnosed pattern from the midterm shaped the rest of the semester's preparation: I lost the most points on the easiest-looking question and scored close to full marks on the hardest one, evidence the traps were in the questions that looked simple, not the ones that looked hard. The cheat sheet built around that lesson, alongside the exam's own algorithm, eigenvalues through diagonalization or Jordan form, symmetric matrices through Gram-Schmidt, carried into the final.

## Where this shows up elsewhere

The eigenvalue and eigenvector machinery from this course is the same math underneath PCA, used directly in my [transportation mode detection research](/research/transportation-mode-detection/) to measure feature redundancy, and underneath the cosine-similarity decomposition in my [common ownership replication](/research/common-ownership/), where a firm's profit weight toward a rival factors into an inner product of two ownership vectors. Neither project derives the eigen-decomposition from scratch; both depend on understanding what it's actually doing.

[Back to Coursework →](/coursework/)
