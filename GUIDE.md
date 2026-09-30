# Practice Guide: Fixing a Bug the Right Way

This repo has a small program with a bug in it. You will fix that bug using
the full process: clone, reproduce, issue, problem analysis (Kepner-Tregoe),
branch, plan, fix, verify, pull request, review, merge, the lock test, and
revert. Claude Code does the work. You read, check and approve each step.

## What you'll learn

By the end of this guide you will be able to:
1. Get a linked copy of a GitHub repo onto your computer and check that
   it matches GitHub
2. Reproduce a problem yourself before reporting it, and report it as a
   GitHub issue
3. Follow a Kepner-Tregoe problem analysis and check that it proves the
   true cause before any fix is made
4. Make every fix on its own branch, so `main` only changes through a
   pull request
5. Check a fix plan for its objectives, alternatives and potential
   problems
6. Check that a fix removed the problem without breaking anything else
7. Review a pull request's changes and decide when it's ready to merge
8. Explain what the lock on `main` does, and show that it blocks a
   direct push
9. Undo a merged change by reverting its pull request
10. Use the same process in any tool, because only the buttons change

This guide works on Mac, Windows and Linux. Where a step is different on
one of them, the step says so.

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
- **Mac:** press **Cmd + Space**, type `Terminal`, and press **Enter**
- **Windows:** open the **Start** menu, type `Terminal`, and press
  **Enter**. (On older Windows, type `PowerShell` instead.)
- **Linux:** press **Ctrl + Alt + T**, or open **Terminal** from your
  applications menu

If a terminal is already open, open a fresh one the same way.

You now have a window with a prompt where you can type. It ends in `%`
on Mac, `>` on Windows, and usually `$` on Linux.

Everything this guide calls "the terminal" means this window.

### 0.3 Check your tools
Type each command below in the terminal and press **Enter**. Compare what
you see with the **Read** line.

**If any command says `command not found`** (on Windows: `is not
recognized`), that tool isn't installed. Go to **Installing a missing
tool** at the end of 0.3, install it, then come back here and carry on.

```
git --version
```
**Read:** `git version` followed by a number.

**Mac and Linux:**
```
python3 --version
```
**Windows:**
```
python --version
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

### Installing a missing tool
Follow the part for your system. Install only what's missing.

**After installing anything, close the terminal and open a new one**
(a new window, not just a new tab), so it can find the new tools. Then
go back to the start of 0.3 and run the checks again.

#### Mac
Mac uses **Homebrew** to install tools. Check whether you have it:
```
brew --version
```
If that says `command not found`, install Homebrew first:
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
When it finishes, it may print **Next steps** with more commands to run.
Run those, then open a new terminal.

Git, GitHub CLI and Python:
```
brew install git gh python3
```

Claude Code:
```
curl -fsSL https://claude.ai/install.sh | bash
```

#### Windows
Run these in **Windows Terminal** or **PowerShell**.

Git:
```
winget install --id Git.Git -e --source winget
```

GitHub CLI:
```
winget install --id GitHub.cli --source winget
```

Python (this installs the Python install manager, which provides the
`python` command):
```
winget install 9NQ7512CXL7T -e --accept-package-agreements --disable-interactivity
```

Claude Code:
```
irm https://claude.ai/install.ps1 | iex
```

#### Linux (Ubuntu and Debian)
Git and Python:
```
sudo apt update
```
```
sudo apt install git python3 curl
```

GitHub CLI: don't use the `gh` package from Ubuntu's own list, which
GitHub says is out of date and broken. Copy this whole block, paste it
into the terminal, and press **Enter**:
```
(type -p wget >/dev/null || (sudo apt update && sudo apt install wget -y)) \
	&& sudo mkdir -p -m 755 /etc/apt/keyrings \
	&& out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
	&& cat $out | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	&& sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	&& sudo mkdir -p -m 755 /etc/apt/sources.list.d \
	&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
	&& sudo apt update \
	&& sudo apt install gh -y
```

Claude Code:
```
curl -fsSL https://claude.ai/install.sh | bash
```

For other Linux systems, use the official install pages listed below.

#### Official install pages
If a command above doesn't work, these pages have the current
instructions:
- Git: https://git-scm.com/install
- GitHub CLI: https://github.com/cli/cli#installation
- Python: https://www.python.org/downloads/
- Claude Code: https://code.claude.com/docs/en/setup

#### Logging in to Claude Code
Claude Code needs a paid Claude plan (Pro, Max, Team or Enterprise) or a
Claude Console account. The first time you run `claude`, it opens your
browser so you can log in.

### 0.4 Arrange your screen
You need three things visible at the same time. Put them side by side.
1. **This guide**, wherever you have it open: on GitHub in your browser,
   or in a text editor on your computer
2. **The repo's home page**, in your browser:
   1. Open your web browser. If this guide is already open in it, open
      a new tab: **Cmd + T** on Mac, **Ctrl + T** on Windows and Linux.
   2. Click in the address bar at the top
   3. Type the address of this repo's GitHub page and press **Enter**.
      It looks like `github.com/<owner>/practice`, where `<owner>` is the
      account or organization that owns the repo. If you don't know it,
      ask whoever gave you this guide.
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
This guide puts it in your home folder, which works the same on every
system. In the terminal, type this and press **Enter**:
```
cd ~
```
(`cd` means "change directory": move into a folder. `~` means your home
folder.)

### 1.3 Clone
Type `git clone ` (with a space at the end), then paste the address you
copied, then press **Enter**. To paste in the terminal: **Cmd + V** on
Mac, **Ctrl + V** on Windows, **Ctrl + Shift + V** in most Linux
terminals. It will look like:
```
git clone https://github.com/<owner>/practice.git
```
**Read:** a few lines ending in `done`. There is now a folder called
`practice` in your home folder.

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
**Mac and Linux:**
```
python3 hello.py Alex
```
**Windows:**
```
python hello.py Alex
```

**Read:** it prints `Hello, World`.

It should print `Hello, Alex`, because you gave it the name `Alex`.
**That's the bug you're going to fix.**

### 2.2 Read the rules Claude will follow
**Do:** on the repo's home page tab, click **`CLAUDE.md`** in the file list.

**Read:** a **Method** section, then 9 numbered steps, then a **Working
rules** section. Claude reads this file automatically every time it
starts in this folder, and follows it.

The Method section says problems here are solved with **Kepner-Tregoe
(KT)**, a structured way to find the true cause of a problem before
fixing it. Claude does the KT analysis. You don't need to know KT; you
only answer plain questions if Claude asks them.

The Working rules are how Claude behaves the whole time. For example: it
answers your questions without changing anything, does only what you
ask, and checks its work before saying it's done.

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
There's an issue: running hello.py with the name Alex prints "Hello, World" instead of "Hello, Alex".
```

**Read:** Claude should NOT start fixing anything. It should say it's
starting at Step 1 and wait for you.

**If it jumps ahead and starts fixing, Say:**
```
Stop. Follow CLAUDE.md one step at a time.
```

---

## Step 1: The issue

An **issue** is GitHub's record of a problem: what's wrong, how to see
it, and the discussion about it. The fix will be linked to it, and
merging the fix closes it.

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

**Check in the browser:** on the issue page, refresh (**Cmd + R** on
Mac, **Ctrl + R** on Windows and Linux), then scroll down. The analysis is there as a comment.

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
- **The problem is gone:** running `hello.py` with the name `Alex` now
  prints `Hello, Alex`
- **The IS NOT case still works:** running `hello.py` with no name still
  prints `Hello, World`
- **The potential problems from Step 4 didn't happen**

**Check:** both outputs are as listed above. If either isn't, **Say:**
```
That's not fixed, investigate again.
```

**Say:** `go`

---

## Step 7: Pull request

A **pull request** asks for the changes on your branch to be merged into
`main`. It shows exactly what changed, so it can be reviewed first.
Because `main` is locked, it's the only way a change can get in.

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

Claude merges with **squash and merge**: all the commits on your branch
are combined into one commit on `main`, so `main`'s history has one
entry per fix.

**Read:** Claude merges the pull request into `main`, deletes the branch,
switches you back to `main`, pulls the latest code, and confirms the
issue closed.

**Check in the browser:**
1. Refresh the pull request page (**Cmd + R** on Mac, **Ctrl + R** on
   Windows and Linux). Next to the title, a
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

**Mac and Linux:**
```
! python3 hello.py Alex
```
**Windows:**
```
! python hello.py Alex
```

**Read:** `Hello, Alex`. The fix is now in the official copy.

**Do:** leave Claude:
```
/exit
```
You're back at the normal terminal prompt.

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
3. Click your fix pull request. The list puts the newest at the top, and
   other people's past fixes may be further down, so pick the newest one
   about the greeting. Check that its description says `Fixes #N` with
   your issue's number.

### 5.2 Revert it (on GitHub)
1. Scroll to the bottom of the **Conversation** tab. Next to the message
   saying the pull request was merged, there is a **Revert** button.
   Click it.
2. GitHub opens a new pull request page, already filled in with a title
   starting with `Revert`. Click the green **Create pull request** button.

### 5.3 Merge the revert (on GitHub)
1. In the box that says **No conflicts with base branch**, click the green
   **Squash and merge** button, not the **Ready to merge** button at the
   top right. (Squash is explained in Step 9.) If that button says
   something else, like **Merge pull request**, click the arrow next to
   it and choose **Squash and merge**.
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
branch, newest at the top. Your fix is the newest one whose branch
starts with `fix/` (older ones are other people's past fixes). Note its
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
**Mac and Linux:**
```
python3 hello.py Alex
```
**Windows:**
```
python hello.py Alex
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
