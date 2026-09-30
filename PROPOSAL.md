# Proposal: Turn This Into a General Tool

**Status:** proposal for the future. Not started. The practice repo will
be run as it is first, and this proposal revisited afterwards.

## Goal

A tool that helps someone open a repo they have never seen, find a
reported problem, and fix it using the Kepner-Tregoe (KT) process, with
Claude Code doing the analysis and the work, and the person owning every
decision.

## Where things stand now

| Part | General or practice-only |
|---|---|
| `CLAUDE.md`: KT method, the 9 steps and the working rules | General. Works on any repo. Only its examples mention `hello.py`. |
| `GUIDE.md` Parts 0, 1 and 3 (setup, clone, starting Claude) | Mostly general |
| `GUIDE.md` Part 2, the examples in Steps 1 to 9, and Part 5 (reset) | Practice-only. Written around the `hello.py` bug. |
| `GUIDE.md` Part 4 (proving the lock works) | Practice-only. Proving the lock works is needed once, not on every fix. |
| `hello.py` | Practice-only |

## Proposed changes

### 1. Add a Situation Appraisal step before Step 1

In the practice repo the reader already knows what the program is, how
to run it, and what's wrong. In an unknown repo, none of that is known.
A new first step in `CLAUDE.md` (KT Situation Appraisal) would have
Claude:

- Work out what the repo is and what it does
- Work out how to install and run it, and confirm it runs
- Reproduce the reported problem, or say clearly that it can't
- If more than one concern comes up, list them separately, set priority,
  and agree with the person which one to work on first

### 2. Make `CLAUDE.md` repo-neutral

Remove the `hello.py` examples from `CLAUDE.md`, or replace them with
examples that aren't tied to one repo.

### 3. Split the guide in two

- **Using the tool on any repo:** setup, cloning, starting Claude, and
  what to expect at each step, without assuming a specific bug
- **Worked example:** the current `hello.py` exercise, kept as practice

### 4. Add automated tests

Real teams verify fixes mostly with automated tests that run by
themselves on every pull request, so a fix can't quietly break something
else. Here, Step 6 (Verify) is done by hand because `hello.py` has no
tests.

Proposed: add tests, run them in Step 6, and have them run automatically
on every pull request so a pull request can't be merged if they fail.

### 5. Require a second person to review

The lock on `main` currently requires 0 approvals, because one person is
working alone and GitHub doesn't let you approve your own pull request.

Proposed: once more than one person works in a repo, require at least 1
approval from someone other than the author before a pull request can
be merged.

## Decision needed: where `CLAUDE.md` lives for a blind repo

Claude Code only follows `CLAUDE.md` if it can find it. For a repo you
open blind, there are two places it could go:

| Option | How it works | Trade-off |
|---|---|---|
| Add it to the target repo | Copy `CLAUDE.md` into the repo's root folder | Changes that repo. It may clash with a `CLAUDE.md` the repo already has, and it would need to be kept out of any commits you don't want. |
| Keep it in your user settings | Put it in `~/.claude/CLAUDE.md`, which Claude Code loads in every repo on that computer | Nothing in the target repo changes, but it applies to every repo you open, not just the ones you're fixing. |

## Assumptions to recheck for unknown repos

The current setup assumes things that may not be true elsewhere:

- The repo is on **GitHub**. It may be on GitLab or elsewhere, where the
  `gh` commands in `CLAUDE.md` won't work.
- The person has **write access**. They may only be able to read it.
- **`main` is locked**. The target repo may have no protection at all.
- The program **runs locally** with one simple command. Real repos may
  need dependencies, secrets or services before anything runs.

## When to revisit

After running the practice exercise end to end at least once. Anything
that felt unclear or slow in the practice run should be fixed as part
of this work.
