+++
title = "Building the MCS160 Repository"
+++

# Building the MCS160 Repository

*A process entry within [Process](/process/).*

> the O'Hare weather dataset behind one of three projects doesn't survive anywhere on this machine. The README says so directly, in its own line, rather than treating the gap as if it never existed.

## Overview

[MCS160's three graded projects](/coursework/mcs160/) existed as working code and recovered assignment PDFs scattered across a personal machine, not as a repository. Turning that into something a working academic repository looks like, one another developer could clone and actually read, meant locating every source file first, then writing a README to the same standard as a published replication package rather than a plain list of what each folder contains.

## Locating the source files

The three projects' original course folder held only blank prompt PDFs. The submitted code had been lost from its original location and had to be recovered separately, from local editor history rather than any preserved course archive.[^recovery] The third project's data file, a daily O'Hare weather dataset, never turned up, not in the recovered editor history, not in any backup, not anywhere else on the machine, after a broad search rather than a quick one. That gap is stated directly in both the coursework page and the repository's own README, not fabricated and not quietly left unmentioned.

## The README standard

The model for the README was one already studied closely during [the writing voice study](/process/writing-voice-study/): `chrisconlon/CommonOwnerReplication`'s own README, terse and functional, a table mapping each file to what it produces, install and run instructions, nothing written to sound impressive rather than to be used. Building against an actual example already read in full carried the same lesson the voice study established for prose elsewhere on this site: a checked source beats a remembered impression of the genre.

## The result

The repository holds one subfolder per project, each with its recovered code and its assignment PDF, files renamed to plain descriptive names with their content otherwise untouched. It is public, at `github.com/SouparneyaC/mcs160-projects`.[^repo] A separate notes file documents where every file actually came from, the data gap in Project 3, and the specific decisions behind the README's own structure, kept apart from the repository itself so the repository stays as clean as the standard it was built against.[^notes]

[^recovery]: Documented in `Course_Content_Process/MCS160_Repo_Prep/MCS160_Repo_Notes.md` in UIC_Wiki: the recovery method and the exact files it produced, checked against the original prompt PDFs before anything was renamed.
[^repo]: [github.com/SouparneyaC/mcs160-projects](https://github.com/SouparneyaC/mcs160-projects), created public and pushed the same day the repository was built.
[^notes]: `Course_Content_Process/MCS160_Repo_Prep/00_Process_Log.md` in UIC_Wiki, the append-only record of this pass, starting from the original request and ending with the repository's creation and push.

## Where this shows up elsewhere

The same recovered code is what [MCS160's coursework page](/coursework/mcs160/) draws its own account from. The repository is the primary source; the page is the narrative built on top of it.

[Back to Process →](/process/)
