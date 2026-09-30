# Practice Guide: Fixing a Bug the Right Way

This repo has a small program with a bug in it. You will fix that bug using
the full process: clone, reproduce, issue, problem analysis (Kepner-Tregoe),
branch, plan, fix, verify, pull request, review, merge, the lock test, and
revert. Claude Code does the work. You read, check and approve each step.

## Prerequisites: what you need before you start

- **A GitHub account.** It's free. Sign up at github.com if you don't
  have one.
- **Write access to this repo.** The repo's owner adds you as a
  collaborator (see 1.1).
- **A paid Claude plan** (Pro, Max, Team or Enterprise) or a Claude
  Console account. Claude Code doesn't work on the free plan.
- **A Mac, Windows or Linux computer where you're allowed to install
  software.** Work computers sometimes block this, so check first.
- **Git, the GitHub CLI, Python 3 and Claude Code.** If you don't have
  them yet, Part 1.3 shows how to check and install them.

## About this guide

This guide works on Mac, Windows and Linux. Where a step is different on
one of them, the step says so.

Read it on GitHub. Some of its formatting, like the colored notes and the
key names, only displays properly there.

**Why the terminal:** this guide uses the terminal because every step is
visible there, which makes it the clearest way to learn the process.
Tools like VS Code run the same Git operations underneath (branch,
commit, push, pull request, merge, revert) using buttons and panels
instead of typed commands. The process, the order of the steps and the
reasons for them stay the same whichever tool you use. Only where you
click changes.

**How to read this guide:** each step is numbered and starts with where
you do it: in the terminal, in Claude's `>` box, or in the browser. What
you should see comes right after, in the same step. To **run** a command
means to type it and press <kbd>Enter</kbd>.

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
10. Follow the same workflow in other Git tools, such as VS Code or
    GitHub Desktop, which run the same Git steps behind their menus and
    buttons

---

## Part 1: Get set up

### 1.1 Access to this repo
To create branches and pull requests here, you need **write access** to
this repo. If you're not the repo's owner, ask the owner to add you as a
collaborator, then accept the invitation GitHub emails you.

### 1.2 Open a terminal

1. On your computer, open a terminal:
   - **Mac:** press <kbd>Cmd</kbd> + <kbd>Space</kbd>, type `Terminal`,
     and press <kbd>Enter</kbd>
   - **Windows:** open the **Start** menu, type `Terminal`, and press
     <kbd>Enter</kbd>. (On older Windows, type `PowerShell` instead.)
   - **Linux:** press <kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>T</kbd>,
     or open **Terminal** from your applications menu

   If a terminal is already open, open a fresh one the same way. A window
   opens with a prompt where you can type. It ends in `%` on Mac, `>` on
   Windows, and usually `$` on Linux.

Everything this guide calls "the terminal" means this window.

### 1.3 Check your tools

Run each command below and compare what you see.

> [!NOTE]
> If any command says `command not found` (on Windows: `is not
> recognized`), that tool isn't installed. Open **Installing a missing
> tool** at the end of 1.3, install it, then come back here and carry on.

1. In the terminal, run:

   ```
   git --version
   ```
   You see `git version` followed by a number.

2. In the terminal, run the command for your system:

   **Mac and Linux:**
   ```
   python3 --version
   ```
   **Windows:**
   ```
   python --version
   ```
   You see `Python 3.` followed by more numbers.

3. In the terminal, run:

   ```
   gh --version
   ```
   You see `gh version` followed by a number. (`gh` is GitHub's command
   line tool. Claude uses it to create issues and pull requests.)

4. In the terminal, run:

   ```
   gh auth status
   ```
   You see `Logged in to github.com account` followed by your GitHub
   username. If it says you're not logged in, run `gh auth login` and
   follow the questions it asks. When it asks whether to authenticate Git
   with your GitHub credentials, answer **Yes**.

5. In the terminal, run:

   ```
   gh auth setup-git
   ```
   This makes Git use your GitHub login when it sends changes to GitHub.
   Without it, sending changes can fail with a password or permission
   error.

6. In the terminal, run:

   ```
   git config --global user.name
   ```
   You see your name.

7. In the terminal, run:

   ```
   git config --global user.email
   ```
   You see your email address.

   Git stamps every saved change with this name and email. If step 6 or
   7 printed nothing, set it, using your own details inside the quotes:
   ```
   git config --global user.name "Your Name"
   ```
   ```
   git config --global user.email "you@example.com"
   ```

8. In the terminal, run:

   ```
   claude --version
   ```
   You see a version number.

<details>
<summary><strong>Installing a missing tool</strong> (click to open)</summary>

Follow the part for your system. Install only what's missing.

> **Important:** after installing anything, close the terminal and open a new one (a new
> window, not just a new tab), so it can find the new tools. Then go back
> to the start of 1.3 and run the checks again.

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

GitHub CLI:

> **Warning:** don't use the `gh` package from Ubuntu's own list. GitHub says it's out
> of date and broken.

Copy this whole block, paste it into the terminal, and press
<kbd>Enter</kbd>:
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

</details>

### 1.4 Arrange your screen

You need three things visible at the same time, side by side.

1. In the browser, open **this guide** on GitHub.
2. In the browser, open **the repo's home page** in a second tab:
   1. Open a new tab: <kbd>Cmd</kbd> + <kbd>T</kbd> on Mac,
      <kbd>Ctrl</kbd> + <kbd>T</kbd> on Windows and Linux.
   2. Click in the address bar at the top.
   3. Type the address of this repo's GitHub page and press
      <kbd>Enter</kbd>. It looks like `github.com/<owner>/practice`, where
      `<owner>` is the account or organization that owns the repo. If you
      don't know it, ask whoever gave you this guide.

   On the repo's home page you see:
   - At the top: `<owner> / practice`, the repo name
   - Just below that, a row of tabs: **Code**, **Issues**, **Pull
     requests**, and more. You'll use **Code**, **Issues** and **Pull
     requests**.
   - In the middle: a list of files, including `CLAUDE.md`, `GUIDE.md`,
     `README.md` and `hello.py`
3. On your computer, place **the terminal** next to the browser.

---

## Part 2: Get the code onto your computer

The repo lives on GitHub. To work on it, you need your own copy on your
computer, linked to GitHub. Making that copy is called **cloning**.

### 2.1 Copy the repo's address

1. In the browser, on the repo's home page tab, click the green
   **`<> Code`** button above the file list.
2. In the box that opens, make sure **HTTPS** is selected.
3. Click the copy icon (two overlapping squares) next to the address. The
   address is copied. It ends in `/practice.git`.

### 2.2 Choose where the copy goes

This guide puts the copy in your home folder, which works the same on
every system. `cd` means "change directory": move into a folder. `~`
means your home folder.

1. In the terminal, run:

   ```
   cd ~
   ```

### 2.3 Clone

1. In the terminal, type `git clone ` (with a space at the end), paste the
   address you copied, and press <kbd>Enter</kbd>. To paste in the
   terminal: <kbd>Cmd</kbd> + <kbd>V</kbd> on Mac, <kbd>Ctrl</kbd> +
   <kbd>V</kbd> on Windows, <kbd>Ctrl</kbd> + <kbd>Shift</kbd> +
   <kbd>V</kbd> in most Linux terminals. It looks like:

   ```
   git clone https://github.com/<owner>/practice.git
   ```
   You see a few lines ending in `done`. There is now a folder called
   `practice` in your home folder.

> [!NOTE]
> If it says `already exists`, you cloned it before. That's fine. Go to
> 2.4, and after 2.4 run `git pull` to get the latest version.

### 2.4 Go into the folder

1. In the terminal, run:

   ```
   cd practice
   ```
   Your prompt now shows `practice`. You're inside the repo.

### 2.5 Confirm it's linked to GitHub

1. In the terminal, run:

   ```
   git status
   ```
   You see:
   - `On branch main`: you're on the official copy
   - `Your branch is up to date with 'origin/main'`: it matches GitHub
     (`origin` is Git's name for the GitHub copy)
   - `nothing to commit, working tree clean`: no unsaved changes

   If you see `fatal: not a git repository`, you're in the wrong folder.
   Go back to 2.2.

---

## Part 3: See the bug yourself

### 3.1 Run the program

1. In the terminal, run the command for your system:

   **Mac and Linux:**
   ```
   python3 hello.py Alex
   ```
   **Windows:**
   ```
   python hello.py Alex
   ```
   It prints `Hello, World`.

It should print `Hello, Alex`, because you gave it the name `Alex`.
**That's the bug you're going to fix.**

### 3.2 Read the rules Claude will follow

`CLAUDE.md` holds the rules Claude follows in this repo. Claude reads it
automatically every time it starts in this folder. Its **Method** section
says problems here are solved with **Kepner-Tregoe (KT)**, a structured
way to find the true cause of a problem before fixing it. Claude does the
KT analysis. You don't need to know KT; you only answer plain questions
if Claude asks them. Its **Working rules** are how Claude behaves the
whole time. For example: it answers your questions without changing
anything, does only what you ask, and checks its work before saying it's
done.

1. In the browser, on the repo's home page tab, click **`CLAUDE.md`** in
   the file list. You see a **Method** section, then 9 numbered steps,
   then a **Working rules** section.
2. Read all 9 steps so you know what's coming.
3. Click **`practice`** in the repo name at the top to go back.

### 3.3 Look at the buggy code

1. In the browser, click **`hello.py`** in the file list. You see 5
   lines: line 3 works out the name, and line 5 prints the greeting. You
   don't need to understand it yet; Claude will explain it.
2. Click **`practice`** at the top to go back.

---

## Part 4: Start Claude

### 4.1 Launch Claude in the repo folder

1. In the terminal (still inside `practice`), run:

   ```
   claude
   ```
   You see a welcome message, and below it a box with a `>` in it.
   Claude is running and waiting for you.

### 4.2 The trust question (first time only)

1. In Claude, if it asks whether you trust the files in this folder,
   choose **Yes** (use the arrow keys if needed, then press
   <kbd>Enter</kbd>).

### 4.3 How to talk to Claude

The `>` box at the bottom is where you type. Type a message, press
<kbd>Enter</kbd>, and Claude's reply appears above the box. If a reply is
long, scroll up with your mouse or trackpad to read all of it.

**Claude stops a lot, on purpose.** `CLAUDE.md` tells it to stop and wait
before each step. Inside one step it may stop more than once, for example
once before investigating and once before posting what it found. Every
time it stops and asks, read what it wrote, then type `go` in Claude's
`>` box and press <kbd>Enter</kbd>. If you don't understand what it
wrote, ask it instead of typing `go`.

### 4.4 The permission box

Sometimes Claude needs to run a command. A box appears showing the
command and asking if it may run it, with options like **Yes** and
**No**. This can happen at any step, usually right after you type `go`.
Every time it happens:

1. Read the command in the box.
2. Check it matches what Claude just told you it was going to do.
3. If it does, choose **Yes** and press <kbd>Enter</kbd>.
4. If it doesn't, choose **No** and ask Claude what it's doing.

### 4.5 The `!` trick

A message that starts with `!` runs as a terminal command instead of
going to Claude. For example `! git branch`. You'll use this to check
Claude's work without leaving Claude.

### 4.6 Report the bug

1. In Claude's `>` box, type this and press <kbd>Enter</kbd>:

   ```
   There's an issue: running hello.py with the name Alex prints "Hello, World" instead of "Hello, Alex".
   ```
   Claude doesn't start fixing anything. It says it's starting at Step 1
   and waits for you.

> [!WARNING]
> If Claude jumps ahead and starts fixing, type this in Claude's `>` box
> and press <kbd>Enter</kbd>:
> ```
> Stop. Follow CLAUDE.md one step at a time.
> ```

---

## Part 5: Fix the bug

These are the 9 steps from `CLAUDE.md`. Claude uses the same numbers, so
when it says "Step 2", it means Step 2 here.

### Step 1: The issue

An **issue** is GitHub's record of a problem: what's wrong, how to see
it, and the discussion about it. The fix will be linked to it, and
merging the fix closes it.

Claude shows draft text for a GitHub issue. It includes:
- A **deviation statement**: one sentence naming what's wrong, for example
  "hello.py prints 'Hello, World' instead of the name given"
- What should happen, and what actually happens
- The steps to reproduce it

1. In Claude's `>` box: if the draft doesn't match what you saw in 3.1,
   tell Claude what to change, and it will redraft. If it matches, type
   `go` and press <kbd>Enter</kbd>. Claude creates the issue and shows its
   number, for example **#2**. Remember it. The rest of this guide calls
   it **#N**. (GitHub numbers issues and pull requests from the same
   counter, so your number depends on what's been created before.)
2. In the browser, on the repo's home page tab, click the **Issues** tab
   near the top, then click your issue's title. You see the issue page,
   with its title, number and description. Leave it open; you'll come
   back to it.

### Step 2: Investigate

Claude now works out the true cause before anyone talks about a fix. It
reads the code, runs the program with different inputs, and looks at the
Git history. If it asks you a question, like "Did this ever work?",
answer it in Claude's `>` box in plain words. "I don't know" is a fine
answer.

Claude shows its analysis. It has these parts:
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

> [!NOTE]
> There is no fix yet. That's on purpose.

1. In Claude's `>` box: if any part doesn't make sense, type
   `explain that more simply` and press <kbd>Enter</kbd>. Keep asking
   until it does. Then type `go` and press <kbd>Enter</kbd>. Claude posts
   the analysis as a comment on the issue.
2. In the browser, on the issue page, refresh (<kbd>Cmd</kbd> +
   <kbd>R</kbd> on Mac, <kbd>Ctrl</kbd> + <kbd>R</kbd> on Windows and
   Linux), then scroll down. The analysis is there as a comment.

### Step 3: Branch

Claude shows the commands it will run: one to update `main`, and one to
create a new branch with a name like `fix/N-greeting-name`, with your
issue's number in place of N.

1. In Claude's `>` box, type `go` and press <kbd>Enter</kbd>.
2. When it's done, in Claude's `>` box, run:

   ```
   ! git branch
   ```
   You see a list of branches. The one with a `*` next to it is the one
   you're on. It should be the new branch, not `main`. You're now working
   on your own copy.

### Step 4: Plan

Claude shows its plan, before it writes any code. It has three parts:
- **Objectives:** what the fix must do. For example: print the name
  that's given, and still print "Hello, World" when no name is given.
- **Alternatives:** the realistic ways to fix the verified cause, and
  which one it picks. For a bug this small it may say there's only one
  realistic option, and why.
- **Potential problems:** what the fix could break, how to prevent it,
  and how Step 6 will check it. For example: the no-name case could
  break, so Step 6 will run the program without a name too.

The fix itself should be a small change to line 5 of `hello.py`.

1. In Claude's `>` box: if the plan mentions changing anything other than
   this bug, type this and press <kbd>Enter</kbd>:

   ```
   Only fix this bug, nothing else.
   ```
   If the plan is only about this bug, type `go` and press
   <kbd>Enter</kbd>.

### Step 5: Fix

Claude makes the change and shows a **diff**, which shows what changed:
- A line starting with `-` (often red) is the old line, being removed
- A line starting with `+` (often green) is the new line, being added

The old `print("Hello, World")` line has a `-`, and a new `print` line
that uses `name` has a `+`.

1. In Claude's `>` box: if Claude's explanation of the diff doesn't make
   sense, ask it to explain again. When it does, type `go` and press
   <kbd>Enter</kbd>.

### Step 6: Verify

Claude runs checks and shows the results:
- **The problem is gone:** running `hello.py` with the name `Alex` now
  prints `Hello, Alex`
- **The IS NOT case still works:** running `hello.py` with no name still
  prints `Hello, World`
- **The potential problems from Step 4 didn't happen**

1. In Claude's `>` box: if either output isn't as listed above, type this
   and press <kbd>Enter</kbd>:

   ```
   That's not fixed, investigate again.
   ```
   If both are right, type `go` and press <kbd>Enter</kbd>.

### Step 7: Pull request

A **pull request** asks for the changes on your branch to be merged into
`main`. It shows exactly what changed, so it can be reviewed first.
Because `main` is locked, it's the only way a change can get in.

Claude runs `git status` and shows the changed files. There is only one:
`hello.py`.

1. In Claude's `>` box: if any other file is listed, ask Claude what it
   is before continuing. If it's only `hello.py`, type `go` and press
   <kbd>Enter</kbd>. Claude saves the change (commit), sends the branch
   to GitHub (push), and opens a pull request.
2. In the browser, go to the repo's home page tab, click the
   **Pull requests** tab near the top, then click the pull request's
   title. You see the pull request page, with its own row of tabs:
   **Conversation**, **Commits**, **Checks**, **Files changed**. On the
   **Conversation** tab (you start here), the description contains
   `Fixes #N` with your issue's number. This links the pull request to
   the issue.
3. In the browser, click the **Files changed** tab. You see the same diff
   as in Step 5, in red and green.

### Step 8: Review

Claude reviews the pull request and summarizes anything it found. It
uses the `/code-review` command if your Claude Code has it, and otherwise
reads the change itself. For a change this small it will probably find
nothing. The review tool is a second pair of eyes, not a replacement for
yours.

1. In the browser, on the **Files changed** tab, read the change yourself
   and ask: does this change do only what the issue asked for?

### Step 9: Merge

Claude merges with **squash and merge**: all the commits on your branch
are combined into one commit on `main`, so `main`'s history has one
entry per fix.

1. In Claude's `>` box, type this and press <kbd>Enter</kbd>:

   ```
   merge it
   ```
   Claude merges the pull request into `main`, deletes the branch,
   switches you back to `main`, pulls the latest code, and confirms the
   issue closed.
2. In the browser, refresh the pull request page (<kbd>Cmd</kbd> +
   <kbd>R</kbd> on Mac, <kbd>Ctrl</kbd> + <kbd>R</kbd> on Windows and
   Linux). Next to the title, a purple badge says **Merged**.
3. In the browser, click the **Issues** tab. Your issue is gone from the
   list, because the list shows open issues by default.
4. Click **Closed** just above the list, then click your issue. It says
   **Closed** and links to the pull request.
5. In Claude's `>` box, run:

   ```
   ! git log --oneline
   ```
   You see a list of saved changes, newest at the top. The top one is
   your fix.
6. In Claude's `>` box, run the command for your system:

   **Mac and Linux:**
   ```
   ! python3 hello.py Alex
   ```
   **Windows:**
   ```
   ! python hello.py Alex
   ```
   You see `Hello, Alex`. The fix is now in the official copy.
7. In Claude's `>` box, run this to leave Claude:

   ```
   /exit
   ```
   You're back at the normal terminal prompt.

---

## Part 6: Prove main is locked

`main` is protected on GitHub: changes can only get in through a pull
request. This part proves it.

### 6.1 Make a change directly on main

1. In the terminal, run:

   ```
   git switch main
   ```
   It may say `Already on 'main'`. That's fine.
2. In the terminal, run this to add the word "test" to the end of
   `README.md`:

   ```
   echo "test" >> README.md
   ```
3. In the terminal, run this to save the change on your computer only:

   ```
   git commit -am "Direct push test"
   ```

### 6.2 Try to push it straight to main

1. In the terminal, run:

   ```
   git push
   ```
   GitHub rejects it. The message includes these lines:
   ```
   remote: error: GH013: Repository rule violations found for refs/heads/main.
   remote: - Changes must be made through a pull request.
    ! [remote rejected] main -> main (push declined due to repository rule violations)
   ```
   That's the lock doing its job.

### 6.3 Undo the test

1. In the terminal, run this to throw away your test change and make your
   copy match GitHub's `main` exactly:

   ```
   git reset --hard origin/main
   ```
2. In the terminal, run:

   ```
   git status
   ```
   Your branch is up to date and there's nothing to commit.

---

## Part 7: Reset for the next person

Your fix is now in `main`, so the bug is gone. Put it back so the next
person has something to fix. You do this by **reverting** your fix pull
request: GitHub creates a new pull request that undoes it, and you merge
that.

Do **either** 7.1 to 7.3 (on GitHub) **or** 7.4 (in the terminal), then 7.5.

### 7.1 Find your fix pull request (on GitHub)

1. In the browser, on the repo's home page tab, click the
   **Pull requests** tab.
2. Just above the list, click **Closed**.
3. Click your fix pull request. The list puts the newest at the top, and
   other people's past fixes may be further down, so pick the newest one
   about the greeting. Its description says `Fixes #N` with your issue's
   number.

### 7.2 Revert it (on GitHub)

1. In the browser, scroll to the bottom of the **Conversation** tab. Next
   to the message saying the pull request was merged, click the
   **Revert** button. GitHub opens a new pull request page, already
   filled in with a title starting with `Revert`.
2. Click the green **Create pull request** button.

### 7.3 Merge the revert (on GitHub)

> [!WARNING]
> Use the **Squash and merge** button in the box that says **No conflicts
> with base branch**, not the **Ready to merge** button at the top right.

1. In the browser, in the box that says **No conflicts with base
   branch**, click the green **Squash and merge** button. (Squash is
   explained in Step 9.) If that button says something else, like
   **Merge pull request**, click the arrow next to it and choose
   **Squash and merge**.
2. Click the green **Confirm squash and merge** button that appears in
   its place.
3. Click **Delete branch** when it appears.

Now go to 7.5.

### 7.4 Revert in the terminal (instead of 7.1 to 7.3)

1. In the terminal, inside the `practice` folder, run this to find your
   fix pull request's number:

   ```
   gh pr list --state merged
   ```
   You see a list of merged pull requests with their number, title and
   branch, newest at the top. Your fix is the newest one whose branch
   starts with `fix/` (older ones are other people's past fixes). Note
   its number.
2. In the terminal, run this to revert it, using your fix pull request's
   number instead of `<number>`:

   ```
   gh pr revert <number>
   ```
   You see a link to the new revert pull request. The number at the end
   of the link is the revert pull request's number.
3. In the terminal, run this to merge the revert, using the revert pull
   request's number:

   ```
   gh pr merge <number> --squash --delete-branch
   ```

Now go to 7.5.

### 7.5 Update your copy and check

1. In the terminal, run:

   ```
   git switch main
   ```
2. In the terminal, run:

   ```
   git pull
   ```
3. In the terminal, run the command for your system:

   **Mac and Linux:**
   ```
   python3 hello.py Alex
   ```
   **Windows:**
   ```
   python hello.py Alex
   ```
   You see `Hello, World`. The bug is back, and the repo is ready for
   the next person.

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
