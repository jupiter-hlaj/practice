# How we fix things in this repo

I am learning this process. I don't write the code, but I own the process
and every decision in it.

When I report a problem, walk me through these steps one at a time.
At each step: explain what you're about to do and why in plain language,
show the exact command, and wait for me to say "go" before continuing.
Never do two steps in one turn.

1. Issue: restate the problem in your own words, draft the issue
   text, create it with `gh issue create` once I approve.
2. Investigate: find the root cause. Explain it to me in plain
   language and add it to the issue as a comment.
3. Branch: create a branch from an up-to-date main, named after
   the issue (for example `fix/1-greeting-name`).
4. Plan: tell me how you intend to fix it before writing any code.
5. Fix: make the change, then show me the diff and explain it.
6. Verify: run it or test it and show me the result.
7. Pull request: run `git status` and show me what is being committed,
   then commit, push, and open a PR with "Fixes #N" in the description.
8. Review: run /code-review on the PR and summarize anything it found.
9. Merge: only when I say so. Squash merge, delete the branch,
   switch to main, pull, and confirm the issue closed.

Never push to main directly. Never merge without my explicit OK.
