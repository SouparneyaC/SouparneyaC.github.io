+++
title = "MATH320 — Linear Algebra"
+++

# MATH320 — Linear Algebra

*A course entry within [Coursework](/coursework/).*

> the same eigenvalue-eigenvector pipeline used on an open-book linear algebra final turns out to be exactly what PCA and a common-ownership profit-weight formula are built on, in two unrelated research projects.

## Overview

MATH320, Linear Algebra, Spring 2026, taught by Dr. İzzet Coşkun. The course's own topic map runs eigenvalues through diagonalization: starting from a matrix \(A\), solve \(\det(A-\lambda I)=0\) for eigenvalues, find eigenvectors from \(\ker(A-\lambda I)\), then branch on whether algebraic and geometric multiplicity match. If they do, the matrix diagonalizes as \(A = PDP^{-1}\); if not, Jordan canonical form. A separate branch covers symmetric matrices: Gram-Schmidt orthogonalization, then an orthogonal \(P\) with \(P^TAP=D\). Determinants are treated geometrically, the volume-scaling factor of the linear map, alongside systems of linear equations with four and five unknowns.

## What the course covered

The midterm and final split the material rather than testing all of it at once. The midterm covers solving general systems of linear equations, computing a matrix's rank from bases for its row and column space, and determining whether a given subset actually qualifies as a subspace, checking closure and finding its dimension. The final's own instructions say to review the midterm material directly, then build past it: orthonormal bases under a chosen inner product, not just the standard dot product, one sample problem defines an inner product weighted 1, 2, and 3 across a symmetric matrix's three free entries, determinants computed by more than one method, permutation expansion, Gaussian elimination, Laplace expansion, and finally eigenvalues, diagonalization, and Jordan form.

The eigenvalue material is framed around a use for diagonalization, not just finding it: one sample problem asks for the matrix that diagonalizes a given 3x3, then for that 3x3 raised to the 90th power, computed directly from the diagonal form rather than by hand. A separate problem gives only a matrix's characteristic and minimal polynomials, an 8x8 matrix with characteristic polynomial \((2-\lambda)^5(3-\lambda)^3\) and minimal polynomial \((2-\lambda)^2(3-\lambda)^2\), and asks for every Jordan form consistent with those two polynomials alone, without the matrix itself ever appearing.

## What I actually built

An open-book final meant the preparation work was building the cheat sheet itself, not memorizing formulas. My own study material tracked a specific graded pattern from the midterm directly: informal proofs lost marks, every proof needed a theorem cited by name, and fed that lesson into how the final-exam sheet was organized.

## Performance

A self-diagnosed pattern from the midterm shaped the rest of the semester's preparation: I lost the most points on the easiest-looking question and scored close to full marks on the hardest one, evidence the traps were in the questions that looked simple, not the ones that looked hard. The cheat sheet built around that lesson, alongside the exam's own algorithm, eigenvalues through diagonalization or Jordan form, symmetric matrices through Gram-Schmidt, carried into the final.

## Where this shows up elsewhere

The eigenvalue and eigenvector machinery from this course is the same math underneath PCA, used directly in my [transportation mode detection research](/research/transportation-mode-detection/) to measure feature redundancy, and underneath the cosine-similarity decomposition in my [common ownership replication](/research/common-ownership/), where a firm's profit weight toward a rival factors into an inner product of two ownership vectors. Neither project derives the eigen-decomposition from scratch; both depend on understanding what it's actually doing.

[Back to Coursework →](/coursework/)
