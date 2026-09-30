# Practice Guide: Fixing a Bug the Right Way

This repo has a small program with a bug in it. You will fix that bug using
the full process: issue, branch, fix, pull request, review, merge.
Claude Code does the work. You read, check and approve each step.

This guide is written for a Mac.

**Why the terminal:** this guide uses the terminal because every step is
visible there, which makes it the clearest way to learn the process.
Tools like VS Code run the same Git operations underneath (branch,
commit, push, pull request, merge, revert) using buttons and panels
instead of typed commands. The process, the order of the steps and the
reasons for them stay the same whichever tool you use. Only where you
click changes.

**How to read this guide:**
- **Do:** something you type or click
- **Say:** text you type to Claude, then press **Enter**
- **Read:** what you should see on screen
- **Check:** how to confirm the step worked

---

## Part 0: Before you start

### 0.1 Access to this repo
To create branches and pull requests here, you need **write access** to
this repo. If you're not the repo's owner, ask the owner to add you as a
collaborator, then accept the invitation GitHub emails you.

### 0.2 Open a terminal
1. Press **Cmd + Space** to open Spotlight search
2. Type `Terminal` and press **Enter**
3. If Terminal was already open, press **Cmd + N** to get a fresh window
4. You now have a window with a prompt ending in `%`

Everything this guide calls "the terminal" means this window.

### 0.3 Check your tools
Type each command below in the terminal and press **Enter**. Compare what
you see with the **Read** line.

```
git --version
```
**Read:** `git version` followed by a number.

```
python3 --version
```
**Read:** `Python 3.` followed by more numbers.

```
gh --version
```
**Read:** `gh version` followed by a number. (`gh` is GitHub's command
line tool. Claude uses it to create issues and pull requests.)

```
gh auth status
```
**Read:** `Logged in to github.com account` followed by your GitHub
username. If it says you're not logged in, type `gh auth login`, press
**Enter**, and follow the questions it asks. When it asks whether to
authenticate Git with your GitHub credentials, answer **Yes**.

```
gh auth setup-git
```
This makes Git use your GitHub login when it sends changes to GitHub.
Without it, sending changes can fail with a password or permission error.

```
git config --global user.name
```
**Read:** your name.
```
git config --global user.email
```
**Read:** your email address.

Git stamps every saved change with this name and email. If either
command prints nothing, set it, using your own details inside the quotes:
```
git config --global user.name "Your Name"
```
```
git config --global user.email "you@example.com"
```

```
claude --version
```
**Read:** a version number.

**If any command says `command not found`**, that tool isn't installed.
Install it before continuing.

### 0.4 Arrange your screen
You need three things visible at the same time. Put them side by side.
1. **This guide**, wherever you have it open: on GitHub in your browser,
   or in a text editor on your computer
2. **The repo's home page**, in your browser:
   1. Open your web browser. If this guide is already open in it, press
      **Cmd + T** to open a new tab.
   2. Click in the address bar at the top
   3. Type `github.com/jupiter-hlaj/practice` and press **Enter**
3. **The terminal**

**What you're looking at on the repo's home page:**
- At the top: `jupiter-hlaj / practice`, the repo name
- Just below that, a row of tabs: **Code**, **Issues**, **Pull requests**,
  and more. You'll use **Code**, **Issues** and **Pull requests**.
- In the middle: a list of files, including `CLAUDE.md`, `GUIDE.md`,
  `README.md` and `hello.py`

---

## Part 1: Get the code onto your computer

The repo lives on GitHub. To work on it, you need your own copy on your
computer, linked to GitHub. Making that copy is called **cloning**.

### 1.1 Copy the repo's address
On the repo's home page tab:
1. Click the green **`<> Code`** button above the file list
2. Make sure **HTTPS** is selected in the box that opens
3. Click the copy icon (two overlapping squares) next to the address.
   The address ends in `/practice.git`.

### 1.2 Choose where the copy goes
This guide puts it on your Desktop. In the terminal, type this and
press **Enter**:
```
cd ~/Desktop
```
(`cd` means "change directory": move into a folder.)

### 1.3 Clone
Type `git clone ` (with a space at the end), then press **Cmd + V** to
paste the address you copied, then press **Enter**. It will look like:
```
git clone https://github.com/<owner>/practice.git
```
**Read:** a few lines ending in `done`. There is now a folder called
`practice` on your Desktop.

**If it says `already exists`:** you cloned it before. That's fine. Go to
1.4, and after 1.4 type `git pull` and press **Enter** to get the latest
version.

### 1.4 Go into the folder
```
cd practice
```
**Read:** your prompt now shows `practice`. You're inside the repo.

### 1.5 Confirm it's linked to GitHub
```
git status
```
**Read:**
- `On branch main`: you're on the official copy
- `Your branch is up to date with 'origin/main'`: it matches GitHub
  (`origin` is Git's name for the GitHub copy)
- `nothing to commit, working tree clean`: no unsaved changes

If you see `fatal: not a git repository`, you're in the wrong folder.
Go back to 1.2.

---

## Part 2: See the bug yourself

### 2.1 Run the program
```
python3 hello.py Alex
```
**Read:** it prints `Hello, World`.

It should print `Hello, Alex`, because you gave it the name `Alex`.
**That's the bug you're going to fix.**

### 2.2 Read the rules Claude will follow
**Do:** on the repo's home page tab, click **`CLAUDE.md`** in the file list.

**Read:** a **Method** section, then 9 numbered steps. Claude reads this
file automatically every time it starts in this folder, and follows it.

The Method section says problems here are solved with **Kepner-Tregoe
(KT)**, a structured way to find the true cause of a problem before
fixing it. Claude does the KT analysis. You don't need to know KT; you
only answer plain questions if Claude asks them.

Read all 9 steps so you know what's coming.

**Do:** click **`practice`** in the repo name at the top to go back.

### 2.3 Look at the buggy code
**Do:** click **`hello.py`** in the file list.

**Read:** 5 lines. Line 3 works out the name. Line 5 prints the greeting.
You don't need to understand it yet; Claude will explain it.

**Do:** click **`practice`** at the top to go back.

---

## Part 3: Start Claude

### 3.1 Launch Claude in the repo folder
In the terminal (still inside `practice`):
```
claude
```
**Read:** a welcome message, and below it a box with a `>` in it. Claude
is running and waiting for you.

### 3.2 The trust question (first time only)
**Read:** Claude may ask whether you trust the files in this folder.

**Do:** choose **Yes** (use the arrow keys if needed, then press **Enter**).

### 3.3 How to talk to Claude
The `>` box at the bottom is where you type. Type a message, press
**Enter**, and Claude's reply appears above the box. If a reply is long,
scroll up with your mouse or trackpad to read all of it.

When this guide says **Say:** `go`, type `go` in the box and press **Enter**.

**Claude stops a lot, on purpose.** `CLAUDE.md` tells it to stop and wait
before each step. Inside one step it may stop more than once, for example
once before investigating and once before posting what it found. Every
time it stops and asks, read what it wrote, then **Say:** `go`. If you
don't understand what it wrote, ask it instead of saying `go`.

### 3.4 The permission box
Sometimes Claude needs to run a command. A box appears showing the
command and asking if it may run it, with options like **Yes** and **No**.
This can happen at any step, usually right after you say `go`.

**Every time this happens:**
1. Read the command in the box
2. Check it matches what Claude just told you it was going to do
3. If it does, choose **Yes** and press **Enter**
4. If it doesn't, choose **No** and ask Claude what it's doing

### 3.5 The `!` trick
A message that starts with `!` runs as a terminal command instead of
going to Claude. For example `! git branch`. You'll use this to check
Claude's work without leaving Claude.

### 3.6 Report the bug
**Say:**
```
There's an issue: running python3 hello.py Alex prints "Hello, World" instead of "Hello, Alex".
```

**Read:** Claude should NOT start fixing anything. It should say it's
starting at Step 1 and wait for you.

**If it jumps ahead and starts fixing, Say:**
```
Stop. Follow CLAUDE.md one step at a time.
```

---

## Step 1: The issue

**Read:** Claude shows draft text for a GitHub issue. It includes:
- A **deviation statement**: one sentence naming what's wrong, for example
  "hello.py prints 'Hello, World' instead of the name given"
- What should happen, and what actually happens
- The steps to reproduce it

**Check:** does it match what you saw in 2.1? If not, tell Claude what
to change. It will redraft.

**Say:** `go`

**Read:** Claude creates the issue and shows its number, for example
**#2**. Remember it. The rest of this guide calls it **#N**.
(GitHub numbers issues and pull requests from the same counter, so
your number depends on what's been created before.)

**Check in the browser:**
1. On the repo's home page tab, click the **Issues** tab near the top
2. Your issue is in the list. Click its title to open it.
3. You're on the issue page: title, number and description

Leave this issue page open. You'll come back to it.

---

## Step 2: Investigate

Claude now works out the true cause before anyone talks about a fix.
It reads the code, runs the program with different inputs, and looks at
the Git history.

**If Claude asks you a question**, like "Did this ever work?", answer it
in plain words. "I don't know" is a fine answer.

**Read:** Claude shows its analysis. It has these parts:
- **Deviation statement:** the one-sentence problem from Step 1
- **IS / IS NOT table:** where the problem happens and where it doesn't.
  For example, it IS wrong when a name is given, and it IS NOT wrong when
  no name is given (then "Hello, World" is correct).
- **Distinctions and changes:** what's different about the cases that
  fail, and what changed around them
- **Possible causes:** each one tested against every row of the table.
  Any cause that doesn't explain both the IS and IS NOT side is dropped.
- **Verified cause:** the one that's left, proven by running the program.
  It should be line 5 of `hello.py`, which prints the fixed words
  "Hello, World" and never uses the name.

There is no fix yet. That's on purpose.

**Check:** does each part make sense? If not, **Say:**
`explain that more simply`. Keep asking until it does.

**Say:** `go`

**Read:** Claude posts the analysis as a comment on the issue.

**Check in the browser:** on the issue page, press **Cmd + R** to
refresh, then scroll down. The analysis is there as a comment.

---

## Step 3: Branch

**Read:** Claude shows the commands it will run: one to update `main`,
and one to create a new branch with a name like `fix/N-greeting-name`,
with your issue's number in place of N.

**Say:** `go`

**Check:**
```
! git branch
```
**Read:** a list of branches. The one with a `*` next to it is the one
you're on. It should be the new branch, not `main`. You're now working
on your own copy.

---

## Step 4: Plan

**Read:** Claude's plan, before it writes any code. It has three parts:
- **Objectives:** what the fix must do. For example: print the name
  that's given, and still print "Hello, World" when no name is given.
- **Alternatives:** the realistic ways to fix the verified cause, and
  which one it picks. For a bug this small it may say there's only one
  realistic option, and why.
- **Potential problems:** what the fix could break, how to prevent it,
  and how Step 6 will check it. For example: the no-name case could
  break, so Step 6 will run the program without a name too.

The fix itself should be a small change to line 5 of `hello.py`.

**Check:** is it only about this bug? If it mentions changing anything
else, **Say:**
```
Only fix this bug, nothing else.
```

**Say:** `go`

---

## Step 5: Fix

**Read:** Claude makes the change and shows a **diff**, which shows what
changed:
- A line starting with `-` (often red) is the old line, being removed
- A line starting with `+` (often green) is the new line, being added

You should see the old `print("Hello, World")` line with a `-`, and a new
`print` line that uses `name`, with a `+`.

**Check:** if Claude's explanation of the diff doesn't make sense, ask it
to explain again.

**Say:** `go`

---

## Step 6: Verify

**Read:** Claude runs checks and shows the results:
- **The problem is gone:** `python3 hello.py Alex` now prints `Hello, Alex`
- **The IS NOT case still works:** `python3 hello.py` (no name) still
  prints `Hello, World`
- **The potential problems from Step 4 didn't happen**

**Check:** both outputs are as listed above. If either isn't, **Say:**
```
That's not fixed, investigate again.
```

**Say:** `go`

---

## Step 7: Pull request

### 7.1 See what's being saved
**Read:** Claude runs `git status` and shows the changed files. There
should be only one: `hello.py`.

**Check:** if any other file is listed, ask Claude what it is before
continuing.

**Say:** `go`

### 7.2 The pull request is created
**Read:** Claude saves the change (commit), sends the branch to GitHub
(push), and opens a pull request.

### 7.3 Look at the pull request
1. Go to the repo's home page tab in your browser
2. Click the **Pull requests** tab near the top
3. Click the pull request's title in the list

**Read:** the pull request page has its own row of tabs:
**Conversation**, **Commits**, **Checks**, **Files changed**.

**Check, Conversation tab** (you start here): the description contains
`Fixes #N` with your issue's number. This links the pull request to the
issue.

**Check, Files changed tab:** click it. You'll see the same diff as in
Step 5, in red and green.

---

## Step 8: Review

**Read:** Claude reviews the pull request and summarizes anything it
found. It uses the `/code-review` command if your Claude Code has it, and
otherwise reads the change itself. For a change this small it will
probably find nothing.

**Do:** in the browser, on the **Files changed** tab, read the change
yourself and ask: does this change do only what the issue asked for?
The review tool is a second pair of eyes, not a replacement for yours.

---

## Step 9: Merge

**Say:**
```
merge it
```

**Read:** Claude merges the pull request into `main`, deletes the branch,
switches you back to `main`, pulls the latest code, and confirms the
issue closed.

**Check in the browser:**
1. Refresh the pull request page (**Cmd + R**). Next to the title, a
   purple badge says **Merged**.
2. Click the **Issues** tab. Your issue is gone from the list, because
   the list shows open issues by default. Click **Closed** just above the
   list, then click your issue: it says **Closed** and links to the pull
   request.

**Check in the terminal:**
```
! git log --oneline
```
**Read:** a list of saved changes, newest at the top. The top one is
your fix.

```
! python3 hello.py Alex
```
**Read:** `Hello, Alex`. The fix is now in the official copy.

**Do:** leave Claude:
```
/exit
```
You're back at the normal terminal prompt ending in `%`.

---

## Part 4: Prove main is locked

`main` is protected on GitHub: changes can only get in through a pull
request. This part proves it.

### 4.1 Make a change directly on main
Type each line and press **Enter** after each:
```
git switch main
```
(It may say `Already on 'main'`. That's fine.)
```
echo "test" >> README.md
```
(This adds the word "test" to the end of `README.md`.)
```
git commit -am "Direct push test"
```
(This saves the change on your computer only.)

### 4.2 Try to push it straight to main
```
git push
```
**Read:** GitHub rejects it. The message includes these lines:
```
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: - Changes must be made through a pull request.
 ! [remote rejected] main -> main (push declined due to repository rule violations)
```
That's the lock doing its job.

### 4.3 Undo the test
```
git reset --hard origin/main
```
(This throws away your test change and makes your copy match GitHub's
`main` exactly.)

**Check:**
```
git status
```
**Read:** your branch is up to date and there's nothing to commit.

---

## Part 5: Reset for the next person

Your fix is now in `main`, so the bug is gone. Put it back so the next
person has something to fix. You do this by **reverting** your fix pull
request: GitHub creates a new pull request that undoes it, and you merge
that.

Do **either** 5.1 to 5.3 (on GitHub) **or** 5.A (in the terminal), then 5.4.

### 5.1 Find your fix pull request (on GitHub)
1. On the repo's home page tab, click the **Pull requests** tab
2. Just above the list, click **Closed**
3. Click your fix pull request (the one with `Fixes #N` in its description)

### 5.2 Revert it (on GitHub)
1. Scroll to the bottom of the **Conversation** tab. Next to the message
   saying the pull request was merged, there is a **Revert** button.
   Click it.
2. GitHub opens a new pull request page, already filled in with a title
   starting with `Revert`. Click the green **Create pull request** button.

### 5.3 Merge the revert (on GitHub)
1. In the box that says **No conflicts with base branch**, click the green
   **Squash and merge** button. (Not the **Ready to merge** button at the
   top right.) If that button says something else, like **Merge pull
   request**, click the arrow next to it and choose **Squash and merge**.
2. Click the green **Confirm squash and merge** button that appears in
   its place
3. Click **Delete branch** when it appears

Now go to 5.4.

### 5.A Revert in the terminal (instead of 5.1 to 5.3)
In the terminal, inside the `practice` folder:

**Find your fix pull request's number:**
```
gh pr list --state merged
```
**Read:** a list of merged pull requests with their number, title and
branch. Your fix is the one whose branch starts with `fix/`. Note its
number.

**Revert it** (use your fix pull request's number instead of `<number>`):
```
gh pr revert <number>
```
**Read:** a link to the new revert pull request. The number at the end
of the link is the revert pull request's number.

**Merge the revert** (use the revert pull request's number):
```
gh pr merge <number> --squash --delete-branch
```

Now go to 5.4.

### 5.4 Update your copy and check
In the terminal, type each line and press **Enter** after each:
```
git switch main
```
```
git pull
```
```
python3 hello.py Alex
```
**Read:** `Hello, World`. The bug is back, and the repo is ready for the
next person.

---

## What you just did

1. Got your own linked copy of the code (**clone**)
2. Saw the problem happen yourself (**reproduce**)
3. Reported it as a deviation statement (**issue**)
4. Found and proved the true cause with Kepner-Tregoe: IS / IS NOT,
   possible causes tested against the facts, verified cause
   (**problem analysis**)
5. Worked on your own copy (**branch**)
6. Decided how to fix it and what could go wrong, before any code was
   written (**plan**)
7. Made only the planned change (**fix**)
8. Confirmed the problem was gone and nothing else broke (**verify**)
9. Asked to put the change into the official copy (**pull request**)
10. Checked it (**review**)
11. Put it in (**merge**), which closed the issue automatically
12. Proved that `main` can't be changed any other way (**the lock**)
13. Undid your fix through a pull request so the next person has the
    bug to fix (**revert**)

The issue, the analysis, the pull request, the merge and the revert all
left a record on GitHub that you can go back and read later.
