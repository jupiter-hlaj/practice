# How we fix things in this repo

I am learning this process. I don't write the code, but I own the process
and every decision in it.

When I report a problem, walk me through these steps one at a time.
At each step: explain what you're about to do and why in plain language,
show the exact command, and wait for me to say "go" before continuing.
Never do two steps in one turn.

## Method: Kepner-Tregoe

Problems in this repo are solved with Kepner-Tregoe (KT). You run the
KT analysis yourself. The person reporting the problem does not need to
know KT.

- Gather facts yourself first: read the code, run the program with
  different inputs, and check the Git history (`git log`, `git diff`).
- Ask the reporter only for facts you can't get that way. Ask in plain
  language, one question at a time, for example "Does it happen with
  every name, or only some?" or "Did this work last week?"
- Never jump from a symptom to a fix. Verify the true cause first, then
  fix the cause, not the symptom.

## Steps

1. Issue: write a KT deviation statement: the object and what is wrong
   with it (for example "hello.py prints 'Hello, World' instead of the
   name given"). Include what should happen, what actually happens, and
   the steps to reproduce. Draft the issue text and create it with
   `gh issue create` once I approve.

2. Investigate (KT Problem Analysis). Do not propose a fix in this step.
   a. Specify the problem as IS / IS NOT for each dimension:
      - WHAT: which object has the problem, and which similar objects
        don't; what the defect is, and what it isn't
      - WHERE: where in the code and with which inputs it happens, and
        where it doesn't
      - WHEN: when it was first seen and since which commit, and when
        it wasn't happening
      - EXTENT: how many cases, how often, and whether it's getting worse
   b. Distinctions: what is true of the IS side but not the IS NOT side.
   c. Changes: what changed in or around each distinction (use Git history).
   d. Possible causes: list the causes the distinctions and changes suggest.
   e. Test each possible cause against every IS and IS NOT fact. A cause
      must explain both sides. Drop any cause that doesn't.
   f. Verify the most probable cause by demonstrating it, for example by
      reproducing it with a specific input, before calling it the true cause.
   Show me the deviation statement, the IS / IS NOT table, the
   distinctions and changes, each possible cause with its test result,
   and the verified cause. Then post the same analysis to the issue as
   a comment.

3. Branch: create a branch from an up-to-date main, named after
   the issue (for example `fix/2-greeting-name` for issue #2).

4. Plan (KT Decision Analysis and Potential Problem Analysis), before
   writing any code:
   a. Objectives: what the fix must do, and what would be good to have.
   b. Alternatives: the realistic ways to fix the verified cause, and
      which one best meets the objectives. If there is only one realistic
      option, say so and why.
   c. Potential problems: what the chosen fix could break or cause. For
      each one: how likely it is, what prevents it, and how step 6 will
      check that it didn't happen.

5. Fix: make only the planned change, then show me the diff and explain it.

6. Verify: show that the deviation is gone (the steps to reproduce now
   give the expected result), that the IS NOT cases still behave the same,
   and that none of the potential problems from step 4 happened.

7. Pull request: run `git status` and show me what is being committed,
   then commit, push, and open a PR with "Fixes #N" in the description.

8. Review: run /code-review on the PR if it's available; otherwise review
   the PR diff yourself for bugs. Summarize anything found.

9. Merge: only when I say so. Squash merge, delete the branch,
   switch to main, pull, and confirm the issue closed.

Never push to main directly. Never merge without my explicit OK.

## Working rules

- A question is not an instruction. When I ask a question, answer it and
  don't change anything or how you work. If the question hints that I
  might want something done differently, ask me in one line.
- Do only what was asked. No extra changes or actions. If something else
  looks needed or risky, tell me instead of doing it.
- Check your work before saying it's done. Review it once as the person
  using it would: does it do what was asked, and will it work for them?
  Fix anything that would make it fail or confuse them. Leave polish
  alone. When you report, say what you checked and what you didn't.
