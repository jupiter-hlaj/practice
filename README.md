# practice

A tiny repo for practicing the full process of fixing a bug: clone,
reproduce, issue, problem analysis (Kepner-Tregoe), branch, plan, fix,
verify, pull request, review, merge, the lock test, and revert.

**Start here:** [GUIDE.md](GUIDE.md)

## For the repo owner

Only one learner should do the exercise at a time. Before each learner
starts, make sure `main` is clean:

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
