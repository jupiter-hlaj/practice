# practice

A tiny repo for practicing the full process of fixing a bug: clone,
reproduce, issue, problem analysis (Kepner-Tregoe), branch, plan, fix,
verify, pull request, review, merge, the lock test, and revert.

The repo is set up the way a real development team's repo is: `main` is
locked, every change goes through a pull request that is reviewed before
it's merged, and problems are tracked as issues. What you practice here
is what you'd do on a real team. Two things a real team usually adds are
not set up here: automated tests that run on every pull request, and a
second person who has to approve each pull request. See
[PROPOSAL.md](PROPOSAL.md).

**Start here:** [GUIDE.md](GUIDE.md)

## For the repo owner

Only one learner should do the exercise at a time. Before each learner
starts, add them as a collaborator (**Settings → Collaborators → Add
people**) so they can push branches and open pull requests. They accept
the invitation from their email.

Then make sure `main` is clean:

```
python3 hello.py Alex
```
(On Windows, type `python` instead of `python3`.)

It must print `Hello, World`. If it prints `Hello, Alex`, the last fix
wasn't reverted.

The tag `clean-start` marks the original buggy `hello.py`. To restore it,
run these inside the repo folder. `main` is locked, so the restore goes
through a pull request:

```
git switch main
git pull
git fetch --tags
git switch -c reset/clean-start
git checkout clean-start -- hello.py
git commit -m "Restore hello.py from clean-start"
git push -u origin reset/clean-start
gh pr create --fill
gh pr merge --squash --delete-branch
git switch main
git pull
```

Then run the check above again.

Use the tag only to restore `hello.py`. The rest of the repo at that tag,
including `GUIDE.md` and `CLAUDE.md`, gets out of date as they're improved.

### Keep the repo public

The lock on `main` (below) only works on a free GitHub plan if the repo
is **public**. GitHub refused to create it while the repo was private,
with: "Upgrade to GitHub Pro or make this repository public to enable
this feature." If the repo is made private without a paid plan, direct
pushes to `main` are no longer blocked and Part 4 of the guide fails.

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
