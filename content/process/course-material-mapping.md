+++
title = "Course Material Mapping"
+++

# Course Material Mapping

*A process entry within [Process](/process/).*

> reading in teaching order rather than by topic name is what surfaced the cross-lecture threads, the same diabetes dataset running through ridge, LASSO, PCR, and PLS across four separate weeks, that a topic list alone would never have shown.

## Overview

The [Coursework](/coursework/) pages on this site describe what a class actually covered, not only its flagship final project. That required going back into each class's local folder, lecture slides, assignment PDFs, submitted code, and reading it in the order the course actually ran, rather than writing from a general sense of what a class with that title usually covers.

## The STAT385 pilot

[STAT385's coursework page](/coursework/stat385/) was the first class built this way. A 521-line, file-by-file index of the raw course folder already existed from an earlier session, `COURSE_MATERIAL_MAP.md`, built once and reviewed by hand. Reading that index first, rather than re-deriving the same inventory from a raw directory listing, was the single biggest time saver in the pass. The syllabus came next, for the grading breakdown and the week-by-week topic sequence, the skeleton the rest of the notes hang on. Then the index's own contents, worked through in the order the semester actually ran, one unit at a time, not by which topic sounded most interesting to write about.

Reading in order surfaced connections a topic-by-topic pass would have missed. The same diabetes dataset runs through ridge regression, LASSO, principal components regression, and partial least squares across four consecutive weeks, one dataset used to teach four related but distinct techniques back to back. The same radar-signal dataset runs through pruning, bagging, and boosting later in the course, and produces a counterintuitive result along the way: bagging without pruning individual trees beat bagging with pruning, 3.8% error against 7.5%, because averaging over many high-variance trees is the entire mechanism bagging depends on.

## The process, as steps

Find the student's own pre-built index first, if one exists, before touching the raw folder directly. If none exists, building one from scratch is a separate, larger task, flagged to the user rather than assumed away. Read the syllabus entry specifically, for the grading structure and the week-by-week map everything else attaches to. Work through the existing index in the order the course actually ran, not alphabetically and not by topic name, since a course arc has connective tissue, a dataset reused across three lectures, a technique from week two a week six lab depends on, that only shows up by going through in sequence. Take notes continuously into a file while reading, not from memory afterward, one content-notes file per class: course facts, the arc in teaching order, and the handful of specific, checkable moments, a number, a counterintuitive result, a worked proof, that make a "what the course covered" section read as lived through rather than a syllabus paraphrase. Curate rather than transcribe. The index is already exhaustive; the notes file exists to select what actually belongs on the page.

One detail from the STAT385 pass carried more weight than expected: a handwritten one-page proof that the conditional mean is the best predictor under squared-error loss.[^proof] It was not only a topic on a slide. It is the actual theoretical justification for the framing the rest of the course builds on, treating regression as "estimate the function," and citing the proof directly did more for the page than describing the topic in general terms would have.

[^proof]: The proof: adding and subtracting \(E(Y|x)\) inside the squared-error expectation shows the cross term vanishes, leaving the expected loss as the sum of an achievable minimum plus a non-negative term that is zero exactly when the predictor equals the conditional mean, footnoted directly on [stat385.md](/coursework/stat385/).

## What's still open

STAT385 already had its index built from an earlier session. The next class run through this process without one will test whether building that index first is worth its own separate step, or whether a single-pass read of a smaller raw folder gets the same result without it. That question is open, not yet tried.

## Where this shows up elsewhere

[STAT385's full final project](/research/transportation-mode-detection/) is documented separately as its own research page; this process only covers what the class taught before that project began.

[Back to Process →](/process/)
