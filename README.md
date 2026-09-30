# practice

A tiny repo for practicing the full process of fixing a bug: clone,
reproduce, issue, problem analysis (Kepner-Tregoe), branch, plan, fix,
verify, pull request, review, merge, the lock test, and revert.

The repo is set up like a professional development environment: `main`
is locked, every change goes through a pull request that is reviewed
before it's merged, and problems are tracked as issues. What you practice
here is what you'd do in professional development work. Two things a
professional environment usually adds are not set up here: automated tests that run on every pull request, and a
second person who has to approve each pull request. See
[PROPOSAL.md](PROPOSAL.md).

**Start here:** [GUIDE.md](GUIDE.md). Before you begin, check its
**Prerequisites: what you need before you start** section.

## For the repo owner

Only one learner should do the exercise at a time.

### Before each learner starts

1. **Give them access.** Add them as a collaborator (**Settings →
   Collaborators → Add people**) so they can push branches and open pull
   requests. They accept the invitation from their email.
2. **Check that `main` is clean.** In your copy of the repo, run:
   ```
   git switch main
   git pull
   ```
   **Mac and Linux:**
   ```
   python3 hello.py Alex
   ```
   **Windows:**
   ```
   python hello.py Alex
   ```

   It must print `Hello, World`, which means the bug is there and ready
   for the learner. If it prints `Hello, Alex`, go to **If `main` isn't
   clean** below.

### If `main` isn't clean

`hello.py` is already fixed. That means the last learner merged their
fix but didn't finish Part 7 of the guide, which puts the bug back.

**Simplest fix:** do Part 7 of the guide yourself. Revert the last
learner's fix pull request (the newest merged one whose branch starts
with `fix/`), then run the check above again.

**If that doesn't work**, for example because several fixes were merged
or a revert went wrong, restore `hello.py` from the `clean-start` tag.

A **tag** is a permanent, named bookmark on one point in the repo's
history. `clean-start` marks the point where `hello.py` still had the
original bug, so that version can always be recovered, whatever has
happened since. `main` is locked, so the restored file still goes in
through a pull request.

Run these inside your copy of the repo:

```
git switch main
git pull
git fetch --tags
```
Get the latest `main`, and make sure your copy has the `clean-start` tag.

```
git switch -c reset/clean-start
```
Make a branch for the restore.

```
git checkout clean-start -- hello.py
```
Replace `hello.py` with the version saved at the `clean-start` tag. Only
that one file changes.

```
git commit -m "Restore hello.py from clean-start"
git push -u origin reset/clean-start
```
Save the change and send the branch to GitHub.

```
gh pr create --fill
```
Open a pull request. `--fill` uses the commit message as the pull
request's title and description.

```
gh pr merge --squash --delete-branch
git switch main
git pull
```
Merge the pull request, delete the branch, and update your copy.

Then run the check in **Before each learner starts** again.

Use the tag only to restore `hello.py`. The rest of the repo at that tag,
including `GUIDE.md` and `CLAUDE.md`, is an old version: they have been
improved since.

### Keep the repo public

The lock on `main` (below) only works on a free GitHub plan if the repo
is **public**. GitHub refused to create it while the repo was private,
with: "Upgrade to GitHub Pro or make this repository public to enable
this feature." If the repo is made private without a paid plan, direct
pushes to `main` are no longer blocked and Part 6 of the guide fails.

### The lock on `main`

The lock is a GitHub ruleset. To see or rebuild it, go to the repo's
**Settings → Rules → Rulesets**. Its settings:

- **Name:** Protect main
- **Enforcement:** Active
- **Target:** the default branch (`main`)
- **Bypass list:** empty, so the lock applies to the owner too
- **Restrict deletions:** on
- **Block force pushes:** on
- **Require a pull request before merging:** on
  - Required approvals: 0 (one person working alone can't approve their
    own pull request)
  - Dismiss stale pull request approvals when new commits are pushed: on

All other settings are left at their defaults.
