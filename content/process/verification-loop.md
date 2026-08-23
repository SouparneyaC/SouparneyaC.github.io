+++
title = "The Verification Loop"
+++

# The Verification Loop

*A process entry within [Process](/process/).*

> a self-verification pass with no outside tool caught almost nothing, an AUROC of 0.540, close to a coin flip. Giving the same check access to a search engine and a code interpreter pushed that number to 0.765.

## Overview

UIC_Wiki is the personal knowledge base behind the research and coursework on this site, hundreds of notes written while working through projects, papers, and data. A note written once, from a first pass through source material, can drift the way any single unchecked draft does. A number gets transposed. A citation attaches to the wrong file. A claim that sounded confident on first write turns out to trace back to nothing. The verification loop is the process built to catch that before it reaches this site, one claim at a time, checked against an actual tool rather than a second guess from the same source that wrote the claim.

## Why tools, not another pass

Three papers shaped the design. Self-Refine[^self-refine] has a model critique and revise its own output with no outside check involved. Chain-of-Verification[^cove] adds a step: generate independent verification questions first, answer them separately, then reconcile, an attempt to stop the check from simply re-confirming the original answer. CRITIC[^critic] goes further and hands the critique step an actual tool, a search engine, a code interpreter, a calculator, and lets the tool's output decide whether a claim holds, not another guess.

The paper's own numbers are why CRITIC won out over the other two. Self-verification with no tool access lands close to chance on the task the paper measures it against, an AUROC of 0.540. The same verification step, given a tool to check against, reaches 0.765. That gap is the paper's central finding: a model checking its own work without an outside reference mostly can't tell what it doesn't know.

The paper also reports a less comfortable result. Tool-based correction doesn't only fix errors. On a portion of its test cases it also broke answers that were already correct.[^diminishing] That finding is why this project caps correction rounds rather than looping until a check comes back clean. An unbounded loop can talk itself past a right answer as easily as it can find a wrong one.

## The adapted protocol

CRITIC's loop, generate an answer, verify it against a tool, correct what the tool contradicts, repeat, maps onto UIC_Wiki with a substitution at the tool step. There is no code interpreter checking arithmetic against a benchmark here. There is Bash and Read confirming a claim against the actual local file it describes, a line count, a grep hit, a git log entry, a query against the wiki's own search index, and WebSearch and WebFetch confirming a claim against the actual external source when the claim reaches outside the wiki, a paper's PDF, a repository's own README.

One departure from the paper: verify blind first. Before opening the note being checked, re-derive the claim independently, run the same file count or read the same source section cold, then compare. This borrows Chain-of-Verification's independence principle rather than CRITIC's own method. Checking against a fresh derivation catches a wrong number that reading the note first, then looking for confirmation, would let slide past.

Corrections are capped at three rounds per claim, following the diminishing-returns result above. A claim that survives blind re-derivation and a tool check is marked confirmed. One that doesn't gets fixed with a dated correction, never a silent edit. A claim that can't be checked either way, no primary source reachable, gets marked unverifiable and left flagged rather than assumed correct by default.

## What it has actually found

Ten runs of this loop have gone through the wiki as of August 2026, one branch at a time.[^runs] The Quant Kiosk branch's first real test found the loop's flagship catch: a note reporting regression statistics as read from a specific report, when the report contained no such numbers. The fabrication had already propagated into two other files by the time it was found; all three were corrected in the same pass, with a dated note rather than a silent rewrite.[^fab] A separate claim in the same branch caught a different kind of error, a row count reported as 1,073,648 against an actual count of 1,746,951, and turned up a finding nobody had gone looking for: a scheduled data-collection script that had been silently failing on a macOS permission error for weeks, which is why the file had stopped growing.

Not every branch produced fabrications. The JTC/SportsPredict branch's first pass found zero contradictions across its whole claim set, a contrast the log records directly rather than treating a clean result as itself suspicious. Its second pass caught one: a specific dollar figure attributed to a match's pricing data that did not exist anywhere in that match's actual files. The FIN456 branch found no fabrications either, but did find three separate misattributions, a fact stated correctly and pinned to the wrong source, a failure mode the log names separately from a fabricated fact. A later run caught a claim that a search process had found zero errors in a dataset; independent re-derivation found 27 recoverable errors the original pass had missed with a narrower search.

The largest run so far checked 132 notes across 21 branches in one pass, the first structured as parallel checks rather than one sequential run. Six claims came back contradicted. Every one was a mechanical transcription or counting error, not an invented statistic. Two came back unverifiable and were flagged as such rather than resolved either way.

[^self-refine]: Madaan et al., "Self-Refine: Iterative Refinement with Self-Feedback," 2023 (arXiv:2303.17651).
[^cove]: Dhuliawala et al., "Chain-of-Verification Reduces Hallucination in Large Language Models," 2023 (arXiv:2309.11495).
[^critic]: Gou et al., "CRITIC: Large Language Models Can Self-Correct with Tool-Interactive Critiquing," ICLR 2024 (arXiv:2305.11738).
[^diminishing]: The paper reports tool-based correction improving results overall while still degrading a minority of already-correct cases, the reason this project treats an unbounded correction loop as a risk rather than a safety net.
[^runs]: `_Verification/00_Verification_Log.md` in UIC_Wiki, an append-only log, one dated entry per run, each claim logged with its method, evidence, and verdict.
[^fab]: Two further instances of the same fabricated figure were found later, propagated into the project's own master index files, and corrected in the same session rather than left for a separate pass.

## Where this shows up elsewhere

This is the same discipline behind every dated correction footnoted across the [Research](/research/) pages on this site. An error found once by this loop, and reintroduced later by memory, does not need to happen a second time if the check that caught it the first time gets treated as a repeatable process rather than a one-off.

[Back to Process →](/process/)
