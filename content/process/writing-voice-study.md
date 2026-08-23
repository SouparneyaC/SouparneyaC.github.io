+++
title = "The Writing Voice Study"
+++

# The Writing Voice Study

*A process entry within [Process](/process/).*

> the fix wasn't shorter sentences. The paper this project replicates writes sentences just as long. The difference was where it put its citations, and which of its own open questions it let stay open.

## Overview

Every research and coursework page on this site went through at least one full rewrite for voice, not content, aimed at a problem the site's own writing kept having: text that read as AI-written no matter how real the underlying research was. The first attempt at a fix was a guess, shorter and punchier sentences. It did not hold up under a real test. The method that did was slower and more literal: find the actual published source a page is built on, read its prose closely, and diff it against the page sentence by sentence.

## The test case

The first full pass compared [common-ownership.md](/research/common-ownership/)'s Data section against the paper it replicates, Backus, Conlon & Sinkinson's "Common Ownership in America: 1980-2017."[^bcs] Both sections were pulled in full, the paper's own PDF converted with `pdftotext -layout` rather than summarized, and read side by side before any diagnosis started.

The site's draft and the published paper differed less in vocabulary than in structure. The paper never opens a list item with a bolded run-in phrase; it narrates related findings as continuous paragraphs, one after another, in the order they were actually found. It does not force every issue it raises to resolve inside the same paragraph, an open question stays open. Its caveats, citations, and scope exceptions live in footnotes, not folded into the sentence carrying the main claim. And it states a number before editorializing about the number's size, "around 500 in 1980 . . . around 4000 by 2017" comes first, with no adjective in front of it doing the work the number should be doing on its own.

Eleven of these differences are logged individually, each against a quote from the paper and the matching line from the site, in a running comparison file.[^diffs] The most recent addition, found while extending this process to a fourth page, was a tic the earlier fixes had accidentally created. Having banned "real" and "genuine" as filler adjectives, a replacement crutch phrase, "worth stating directly," turned up on four separate pages instead. It got the same treatment: logged, and removed everywhere it appeared.

## The process, as steps

Find the actual source a page's claims trace back to, not a well-written example of the genre in general, the paper or repository the project draws from. Pull its text directly rather than a summary, cloning the repository or fetching the PDF and converting it to plain text. Match one section against its counterpart, a Data section against a Data section, not an abstract against a results section. Read both in full before annotating either. Diff at the level of sentence and paragraph structure first, list formatting, whether claims resolve, where caveats live, sentence compounding, not only at the level of individual word choice. Log every finding against the actual quote on both sides, so the pattern is checkable later rather than asserted. Only then write a fix rule, stated as an instruction rather than a description.

## What it caught, applied since

The same process has now run against every research page on this site, both crowd-belief-compression case studies, and the coursework pages, catching problems specific to each page rather than reapplying one generic fix everywhere. On [crowd-belief-compression.md](/research/crowd-belief-compression/), it caught an undersold citation. The page's paraphrase of a cited paper said a scoring rule performed "no worse than" an alternative. The cited paper's own worked example showed the scoring rule actively favoring the wrong model in a specific match scenario, a precision gap only visible by checking the source's own numbers rather than trusting a remembered paraphrase of its conclusion.[^cf]

[^bcs]: Backus, Conlon & Sinkinson, "Common Ownership in America: 1980-2017," 2019, the paper [common-ownership.md](/research/common-ownership/) replicates and extends.
[^diffs]: `Writing_Voice_Study/style_diffs.csv` in UIC_Wiki, one row per finding: category, the paper's own example, the site's own example, and the fix rule.
[^cf]: Constantinou & Fenton, 2012, cited on [crowd-belief-compression.md](/research/crowd-belief-compression/). The paper's Table 2, Match 5, shows Brier scores of 0.0240 against 0.3038 for the two models being compared.

## Where this shows up elsewhere

This process only exists because the underlying claim on every one of these pages was already checked against a source once, through [the verification loop](/process/verification-loop/). The writing pass checks how a true claim gets said, not whether it is true in the first place.

[Back to Process →](/process/)
