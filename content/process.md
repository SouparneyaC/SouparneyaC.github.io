+++
title = "Process"
menu = "main"
weight = 4
+++

# Process

Everything else on this site is a research finding, a data set, a piece of code. This section is about how it got built. I use AI tools as working infrastructure across this site's research, coursework, and repositories, and I keep a written record of that work in a personal knowledge base, [UIC_Wiki](/process/verification-loop/), the same way the rest of this site keeps a record of everything else. These four entries document the methods I actually run, not a general description of what AI tools can do.

## [The Verification Loop](/process/verification-loop/)

> a self-verification pass with no outside tool caught almost nothing, an AUROC of 0.540, close to a coin flip. Giving the same check access to a search engine and a code interpreter pushed that number to 0.765.

A tool-grounded fact-checking process, adapted from a published method (Gou et al., ICLR 2024), run repeatedly across UIC_Wiki. Ten runs so far have checked well over a hundred notes against their primary sources, caught a fabricated statistic that had spread into three files, and found a handful of smaller citation and transcription errors along the way.

## [The Writing Voice Study](/process/writing-voice-study/)

> the fix wasn't shorter sentences. The paper this project replicates writes sentences just as long. The difference was where it put its citations, and which of its own open questions it let stay open.

Every page on this site went through a rewrite for voice, diagnosed by reading the actual published source each project is built on and diffing its real prose against the site's own draft, sentence by sentence, rather than guessing at what makes writing sound AI-generated.

## [Course Material Mapping](/process/course-material-mapping/)

> reading in teaching order rather than by topic name is what surfaced the cross-lecture threads, the same diabetes dataset running through ridge, LASSO, PCR, and PLS across four separate weeks, that a topic list alone would never have shown.

The process behind the [Coursework](/coursework/) pages: indexing a class's raw local folder file by file, then reading it back in the order the semester actually ran, so the page describes what was actually taught, not a syllabus paraphrase.

## [Building the MCS160 Repository](/process/mcs160-repo-build/)

> the O'Hare weather dataset behind one of three projects doesn't survive anywhere on this machine. The README says so directly, in its own line, rather than treating the gap as if it never existed.

Turning three recovered course projects into a public repository with a README built to the standard of a real published replication package, including an honest, stated data gap rather than a silently missing file.
