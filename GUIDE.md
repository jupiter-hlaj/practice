# Practice Guide: Fixing a Bug the Right Way

This repo has a small program with a bug in it. You will fix that bug using
the full process: issue, branch, fix, pull request, review, merge.
Claude Code does the work. You read, check and approve each step.

This guide is written for a Mac.

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
1. **This guide**, in your browser
2. **The repo's home page**, in a second browser tab: right-click
   **`practice`** in the repo name at the top of this page and choose
   **Open Link in New Tab**
3. **The terminal**

**What you're looking at on the repo's home page:**
- At the top: `<owner> / practice`, the repo name
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

**Read:** 9 numbered steps. Claude reads this file automatically every
time it starts in this folder, and follows it. Read all 9 so you know
what's coming.

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

**Read:** Claude shows draft text for a GitHub issue: a title and a
description of the problem.

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

**Read:** Claude explains why the bug happens, in plain language. It
should point at line 5 of `hello.py` and say it prints the fixed words
"Hello, World" instead of using the name.

**Check:** if the explanation doesn't make sense, **Say:**
`explain that more simply`. Keep asking until it does.

**Say:** `go`

**Read:** Claude posts that explanation as a comment on the issue.

**Check in the browser:** on the issue page, press **Cmd + R** to
refresh, then scroll down. The explanation is there as a comment.

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

**Read:** Claude describes how it will fix the bug, before writing any
code. It should be a small change to line 5 of `hello.py`.

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

**Read:** Claude runs `python3 hello.py Alex` and shows the output.

**Check:** the output is now `Hello, Alex`. If it isn't, **Say:**
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
person has something to fix. This goes through a pull request too,
because `main` is locked.

### 5.1 Start a branch from the latest main
Type each line and press **Enter** after each:
```
git switch main
```
```
git pull
```
```
git switch -c reset/plant-bug
```

### 5.2 Put the original code back
Copy this whole block, paste it into the terminal, and press **Enter**:
```
cat > hello.py <<'EOF'
import sys

name = sys.argv[1] if len(sys.argv) > 1 else "World"

print("Hello, World")
EOF
```
(This replaces `hello.py` with the original buggy version.)

**Check:**
```
python3 hello.py Alex
```
**Read:** `Hello, World`. The bug is back.

### 5.3 Send it through a pull request and merge it
Type each line and press **Enter** after each:
```
git commit -am "Put the practice bug back"
```
```
git push -u origin reset/plant-bug
```
```
gh pr create --title "Put the practice bug back" --body "Resets hello.py for the next person."
```
```
gh pr merge --squash --delete-branch
```
```
git switch main
```
(It may say `Already on 'main'`. That's fine.)
```
git pull
```

**Check:**
```
python3 hello.py Alex
```
**Read:** `Hello, World`. The repo is ready for the next person.

---

## What you just did

1. Got your own linked copy of the code (**clone**)
2. Reported a problem (**issue**)
3. Found why it happened (**root cause**)
4. Worked on your own copy (**branch**)
5. Asked to put the change into the official copy (**pull request**)
6. Checked it (**review**)
7. Put it in (**merge**), which closed the issue automatically
8. Proved that `main` can't be changed any other way (**the lock**)

Every step left a record on GitHub that you can go back and read later.
