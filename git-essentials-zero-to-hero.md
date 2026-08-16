# Git Essentials — Zero to Hero

A practical Git handbook covering the concepts, commands, syntax, examples, workflows, recovery techniques, and advanced tools you need to use Git confidently.

---

## Table of Contents

1. [What Is Git?](#1-what-is-git)
2. [Git vs GitHub](#2-git-vs-github)
3. [Install and Configure Git](#3-install-and-configure-git)
4. [Core Git Concepts](#4-core-git-concepts)
5. [Create or Clone a Repository](#5-create-or-clone-a-repository)
6. [The Essential Git Workflow](#6-the-essential-git-workflow)
7. [Inspect Changes](#7-inspect-changes)
8. [Working With Commits](#8-working-with-commits)
9. [Ignoring Files](#9-ignoring-files)
10. [Branches](#10-branches)
11. [Merging](#11-merging)
12. [Merge Conflicts](#12-merge-conflicts)
13. [Remote Repositories](#13-remote-repositories)
14. [Push, Pull, and Fetch](#14-push-pull-and-fetch)
15. [Tracking Branches](#15-tracking-branches)
16. [Undoing Changes Safely](#16-undoing-changes-safely)
17. [git reset](#17-git-reset)
18. [git revert](#18-git-revert)
19. [git restore](#19-git-restore)
20. [git stash](#20-git-stash)
21. [git rebase](#21-git-rebase)
22. [Interactive Rebase](#22-interactive-rebase)
23. [Cherry-Pick](#23-cherry-pick)
24. [Tags](#24-tags)
25. [Viewing History](#25-viewing-history)
26. [Searching Git History](#26-searching-git-history)
27. [Git Diff](#27-git-diff)
28. [Git Blame](#28-git-blame)
29. [Reflog — Recover "Lost" Work](#29-reflog--recover-lost-work)
30. [Cleaning Untracked Files](#30-cleaning-untracked-files)
31. [Removing and Moving Files](#31-removing-and-moving-files)
32. [Aliases](#32-aliases)
33. [Useful Configuration](#33-useful-configuration)
34. [SSH With Git Hosting](#34-ssh-with-git-hosting)
35. [Common Team Workflow](#35-common-team-workflow)
36. [Feature Branch Workflow](#36-feature-branch-workflow)
37. [Pull Request Workflow](#37-pull-request-workflow)
38. [Rebase vs Merge](#38-rebase-vs-merge)
39. [Reset vs Revert vs Restore](#39-reset-vs-revert-vs-restore)
40. [Common Problems and Fixes](#40-common-problems-and-fixes)
41. [Advanced Git Commands](#41-advanced-git-commands)
42. [Recommended Commit Practices](#42-recommended-commit-practices)
43. [Repository Hygiene](#43-repository-hygiene)
44. [Practice Exercises](#44-practice-exercises)
45. [Git Command Cheat Sheet](#45-git-command-cheat-sheet)
46. [Mental Model](#46-mental-model)

---

# 1. What Is Git?

Git is a **distributed version control system**.

It tracks changes to files over time so that you can:

- see what changed
- see who changed it
- restore previous versions
- work on multiple features independently
- collaborate with other developers
- experiment without destroying stable code
- merge work from multiple people

Git stores project history as a graph of **commits**.

A commit is essentially a saved snapshot of your project plus metadata such as:

- author
- timestamp
- commit message
- parent commit(s)

Example history:

```text
A---B---C---D
```

Each letter represents a commit.

---

# 2. Git vs GitHub

Git and GitHub are not the same thing.

## Git

Git is the version control software running on your machine.

Examples:

```bash
git status
git add .
git commit -m "Add login validation"
```

## GitHub / GitLab / Bitbucket

These are services that host Git repositories online.

They add features such as:

- pull requests
- code review
- issue tracking
- CI/CD
- access control
- project management

You can use Git without GitHub.

---

# 3. Install and Configure Git

Check whether Git is installed:

```bash
git --version
```

Example:

```text
git version 2.x.x
```

## Configure your name

```bash
git config --global user.name "Your Name"
```

## Configure your email

```bash
git config --global user.email "you@example.com"
```

## View configuration

```bash
git config --list
```

## View a specific setting

```bash
git config user.name
git config user.email
```

## Configuration levels

Git supports multiple configuration scopes.

### System

Applies to every user on the machine.

```bash
git config --system ...
```

### Global

Applies to your user account.

```bash
git config --global ...
```

### Local

Applies only to the current repository.

```bash
git config --local ...
```

Local configuration overrides global configuration.

---

# 4. Core Git Concepts

Before memorizing commands, understand the Git data flow.

Git commonly involves these areas:

```text
Working Directory
      |
      | git add
      v
Staging Area
      |
      | git commit
      v
Local Repository
      |
      | git push
      v
Remote Repository
```

## Working directory

The actual files you edit.

Example:

```text
app.py
README.md
package.json
```

## Staging area

A preparation area for the next commit.

You decide exactly which changes belong in the next commit.

```bash
git add app.py
```

## Local repository

Commits stored in your `.git` directory.

```bash
git commit -m "Update app"
```

## Remote repository

A repository stored somewhere else, usually GitHub, GitLab, or Bitbucket.

```bash
git push
```

---

# 5. Create or Clone a Repository

## Create a new repository

```bash
mkdir my-project
cd my-project
git init
```

Git creates a hidden directory:

```text
.git/
```

That directory contains repository history and metadata.

Check repository status:

```bash
git status
```

---

## Clone an existing repository

Syntax:

```bash
git clone <repository-url>
```

Example:

```bash
git clone https://github.com/example/project.git
```

Clone into a custom directory:

```bash
git clone https://github.com/example/project.git my-app
```

---

# 6. The Essential Git Workflow

The basic workflow is:

```text
edit -> status -> add -> commit -> push
```

Example:

```bash
git status
git add app.py
git commit -m "Add input validation"
git push
```

## Step 1: Edit files

Example:

```bash
echo "# My Project" > README.md
```

## Step 2: Inspect status

```bash
git status
```

Possible output:

```text
Untracked files:
  README.md
```

## Step 3: Stage changes

Stage one file:

```bash
git add README.md
```

Stage multiple files:

```bash
git add app.py README.md
```

Stage everything in the current directory:

```bash
git add .
```

Stage all modifications, deletions, and new files:

```bash
git add -A
```

## Step 4: Commit

```bash
git commit -m "Add project README"
```

## Step 5: Push

```bash
git push
```

---

# 7. Inspect Changes

## git status

Shows the state of your working tree and staging area.

```bash
git status
```

Short format:

```bash
git status -s
```

Example:

```text
 M app.py
A  README.md
?? notes.txt
```

Meaning:

```text
M   modified
A   added
D   deleted
??  untracked
```

---

# 8. Working With Commits

## Create a commit

```bash
git commit -m "Add authentication middleware"
```

## Commit tracked files without manually staging them

```bash
git commit -am "Fix validation bug"
```

Important:

`-a` does **not** include brand-new untracked files.

---

## Amend the previous commit

Change the last commit message:

```bash
git commit --amend -m "Better commit message"
```

Add forgotten changes to the last commit:

```bash
git add forgotten-file.txt
git commit --amend --no-edit
```

Use caution if the commit has already been pushed and other developers may depend on it.

---

# 9. Ignoring Files

Git uses `.gitignore` to exclude files that should not be tracked.

Example `.gitignore`:

```gitignore
# Dependencies
node_modules/

# Python
__pycache__/
*.pyc
.venv/

# Environment variables
.env

# Build output
dist/
build/

# IDE
.vscode/
.idea/

# OS files
.DS_Store
```

If Git already tracks a file, adding it to `.gitignore` does not automatically stop tracking it.

Use:

```bash
git rm --cached .env
```

Then commit the removal:

```bash
git commit -m "Stop tracking environment file"
```

---

# 10. Branches

A branch is a movable pointer to a commit.

Branches allow independent lines of development.

Example:

```text
main
  |
A---B---C
         \
          D---E
              |
           feature
```

## List branches

```bash
git branch
```

Show local and remote branches:

```bash
git branch -a
```

## Create a branch

```bash
git branch feature-login
```

## Switch to a branch

Modern syntax:

```bash
git switch feature-login
```

Older syntax:

```bash
git checkout feature-login
```

## Create and switch in one command

```bash
git switch -c feature-login
```

Older equivalent:

```bash
git checkout -b feature-login
```

## Rename current branch

```bash
git branch -m new-name
```

## Delete a merged branch

```bash
git branch -d feature-login
```

Force delete an unmerged branch:

```bash
git branch -D feature-login
```

---

# 11. Merging

Merging combines changes from another branch into your current branch.

Suppose you have:

```text
main
feature-login
```

Switch to the branch that should receive the changes:

```bash
git switch main
```

Merge:

```bash
git merge feature-login
```

---

## Fast-forward merge

Before:

```text
A---B main
     \
      C---D feature
```

After:

```text
A---B---C---D
            |
        main, feature
```

Git simply moves the `main` pointer forward.

---

## Three-way merge

History may look like:

```text
      C---D feature
     /
A---B---E---F main
```

After merging:

```text
      C---D
     /     \
A---B---E---F---M
                |
               main
```

`M` is a merge commit.

---

# 12. Merge Conflicts

A conflict happens when Git cannot automatically determine which version should win.

Example conflict:

```text
<<<<<<< HEAD
console.log("Hello from main");
=======
console.log("Hello from feature");
>>>>>>> feature
```

You manually edit the file into the desired final version:

```javascript
console.log("Hello from merged code");
```

Then stage the resolved file:

```bash
git add app.js
```

Complete the merge:

```bash
git commit
```

Abort the merge:

```bash
git merge --abort
```

---

# 13. Remote Repositories

View configured remotes:

```bash
git remote -v
```

Typical output:

```text
origin  git@github.com:user/project.git (fetch)
origin  git@github.com:user/project.git (push)
```

## Add a remote

```bash
git remote add origin https://github.com/user/project.git
```

## Change remote URL

```bash
git remote set-url origin git@github.com:user/project.git
```

## Remove a remote

```bash
git remote remove origin
```

## Rename a remote

```bash
git remote rename origin upstream
```

---

# 14. Push, Pull, and Fetch

These commands are related but different.

---

## git fetch

Downloads remote branch information and commits without changing your working branch.

```bash
git fetch
```

Fetch a specific remote:

```bash
git fetch origin
```

Think:

```text
download remote information only
```

---

## git pull

Usually performs:

```text
fetch + integrate
```

Example:

```bash
git pull origin main
```

Depending on configuration, integration may use merge or rebase.

A common explicit alternative:

```bash
git fetch origin
git merge origin/main
```

---

## git push

Uploads local commits to a remote repository.

```bash
git push origin main
```

First push of a new branch:

```bash
git push -u origin feature-login
```

After setting upstream:

```bash
git push
```

---

# 15. Tracking Branches

A local branch may track a remote branch.

Example:

```text
local:  main
remote: origin/main
```

Set upstream:

```bash
git branch --set-upstream-to=origin/main main
```

Or during the first push:

```bash
git push -u origin main
```

View tracking information:

```bash
git branch -vv
```

---

# 16. Undoing Changes Safely

Git has several undo commands.

Use the right command based on **where the change currently exists**.

```text
Working tree only      -> git restore
Staging area           -> git restore --staged
Local commit/history   -> git reset
Shared/public history  -> git revert
```

---

# 17. git reset

`git reset` moves the current branch pointer and can also update the staging area and working directory.

Assume history:

```text
A---B---C  HEAD
```

## Soft reset

```bash
git reset --soft HEAD~1
```

Result:

```text
A---B  HEAD
```

Changes from commit `C` remain staged.

Use when:

- you want to redo the previous commit
- you want to combine commits

---

## Mixed reset

Default mode:

```bash
git reset HEAD~1
```

Equivalent:

```bash
git reset --mixed HEAD~1
```

Changes remain in your working directory but become unstaged.

---

## Hard reset

```bash
git reset --hard HEAD~1
```

This resets:

- commit pointer
- staging area
- working tree

Potentially destructive.

Use carefully.

---

## Reset a file from staging

Traditional syntax:

```bash
git reset HEAD file.txt
```

Modern preferred syntax:

```bash
git restore --staged file.txt
```

---

# 18. git revert

`git revert` creates a **new commit** that reverses an earlier commit.

Syntax:

```bash
git revert <commit>
```

Example:

```bash
git revert a1b2c3d
```

History:

```text
A---B---C---R
```

`R` reverses the changes introduced by `C`.

This is usually safer than reset for commits already shared with teammates.

---

# 19. git restore

Restore a modified file from the current commit:

```bash
git restore file.txt
```

This discards unstaged changes in that file.

Unstage a file:

```bash
git restore --staged file.txt
```

Restore from another commit:

```bash
git restore --source=<commit> file.txt
```

Example:

```bash
git restore --source=HEAD~2 config.yaml
```

---

# 20. git stash

Stashing temporarily stores uncommitted changes.

Useful when you need to switch tasks without creating a commit.

## Stash changes

```bash
git stash
```

With a message:

```bash
git stash push -m "WIP login form"
```

Include untracked files:

```bash
git stash -u
```

## View stashes

```bash
git stash list
```

Example:

```text
stash@{0}: On main: WIP login form
stash@{1}: On main: API experiment
```

## Restore latest stash

```bash
git stash apply
```

## Restore and remove latest stash

```bash
git stash pop
```

## Apply a specific stash

```bash
git stash apply stash@{1}
```

## Delete a stash

```bash
git stash drop stash@{0}
```

## Delete all stashes

```bash
git stash clear
```

---

# 21. git rebase

Rebase moves or reapplies commits onto another base.

Example before:

```text
      C---D feature
     /
A---B---E---F main
```

Run:

```bash
git switch feature
git rebase main
```

Conceptual result:

```text
A---B---E---F---C'---D'
```

Git recreates the feature commits on top of `main`.

Benefits:

- linear history
- easier history reading
- fewer merge commits

Important:

Avoid rebasing shared commits that other people are already using unless your team explicitly coordinates that workflow.

---

## Rebase conflict

If a conflict occurs:

1. Fix the file.
2. Stage it.

```bash
git add file.txt
```

3. Continue:

```bash
git rebase --continue
```

Skip the problematic commit:

```bash
git rebase --skip
```

Abort:

```bash
git rebase --abort
```

---

# 22. Interactive Rebase

Interactive rebase lets you rewrite recent history.

Example:

```bash
git rebase -i HEAD~4
```

You may see:

```text
pick a111111 Add API client
pick b222222 Fix typo
pick c333333 Add validation
pick d444444 Update tests
```

Commands commonly include:

```text
pick    keep commit
reword  change commit message
edit    stop and modify commit
squash  combine with previous commit
fixup   combine and discard this message
drop    remove commit
```

Example:

```text
pick a111111 Add API client
fixup b222222 Fix typo
squash c333333 Add validation
pick d444444 Update tests
```

Interactive rebase is excellent for cleaning local history before opening a pull request.

---

# 23. Cherry-Pick

Cherry-pick copies a specific commit onto your current branch.

Syntax:

```bash
git cherry-pick <commit>
```

Example:

```bash
git switch main
git cherry-pick a1b2c3d
```

Use cases:

- copy a bug fix between branches
- backport a commit
- recover a commit from another branch

Abort a conflicted cherry-pick:

```bash
git cherry-pick --abort
```

Continue after fixing conflicts:

```bash
git add .
git cherry-pick --continue
```

---

# 24. Tags

Tags mark important commits, often releases.

Examples:

```text
v1.0.0
v1.1.0
v2.0.0
```

## List tags

```bash
git tag
```

## Lightweight tag

```bash
git tag v1.0.0
```

## Annotated tag

```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
```

## Tag an older commit

```bash
git tag -a v1.0.0 a1b2c3d -m "Release v1.0.0"
```

## Push one tag

```bash
git push origin v1.0.0
```

## Push all tags

```bash
git push origin --tags
```

## Delete local tag

```bash
git tag -d v1.0.0
```

## Delete remote tag

```bash
git push origin --delete v1.0.0
```

---

# 25. Viewing History

## Basic log

```bash
git log
```

## Compact log

```bash
git log --oneline
```

Example:

```text
e3ab123 Add payment endpoint
c91d021 Fix login bug
81a20f0 Initial commit
```

## Graph view

```bash
git log --oneline --graph --decorate --all
```

Example:

```text
* 8b3813a (HEAD -> main) Merge feature-login
|\
| * 2ba98e1 Add login form
| * 1a845aa Add auth API
|/
* e552a1b Initial project
```

## Show a specific commit

```bash
git show <commit>
```

Example:

```bash
git show e3ab123
```

---

# 26. Searching Git History

Search commit messages:

```bash
git log --grep="login"
```

Search for commits that changed a specific string:

```bash
git log -S "calculateTotal"
```

Search changes matching a regular expression:

```bash
git log -G "TODO|FIXME"
```

History for one file:

```bash
git log -- app.py
```

Show patches:

```bash
git log -p -- app.py
```

---

# 27. Git Diff

`git diff` compares versions.

## Working directory vs staging area

```bash
git diff
```

Shows unstaged changes.

## Staging area vs latest commit

```bash
git diff --staged
```

Also:

```bash
git diff --cached
```

## Compare two commits

```bash
git diff <commit1> <commit2>
```

Example:

```bash
git diff a1b2c3d e4f5g6h
```

## Compare branches

```bash
git diff main..feature-login
```

## Diff one file

```bash
git diff -- app.py
```

---

# 28. Git Blame

`git blame` shows who last modified each line.

```bash
git blame app.py
```

Example:

```text
a1b2c3d Alice  10) def login():
e4f5g6h Bob    11)     validate_user()
```

It is useful for understanding history, not for assigning blame.

---

# 29. Reflog — Recover "Lost" Work

`git reflog` records movements of `HEAD` and branch references in your local repository.

```bash
git reflog
```

Example:

```text
ab12cd3 HEAD@{0}: reset: moving to HEAD~2
ef45ab6 HEAD@{1}: commit: Add payment processing
98fc123 HEAD@{2}: commit: Add checkout
```

Suppose you accidentally run:

```bash
git reset --hard HEAD~2
```

Find the old commit:

```bash
git reflog
```

Recover it by creating a branch:

```bash
git branch recovery ef45ab6
```

Or reset back:

```bash
git reset --hard ef45ab6
```

Reflog is one of Git's most valuable recovery tools.

---

# 30. Cleaning Untracked Files

Preview what Git would delete:

```bash
git clean -n
```

Delete untracked files:

```bash
git clean -f
```

Delete untracked directories too:

```bash
git clean -fd
```

Delete ignored files too:

```bash
git clean -fdx
```

Be careful: `git clean` can permanently delete untracked files.

Always preview first when possible:

```bash
git clean -nd
```

---

# 31. Removing and Moving Files

## Remove a tracked file

```bash
git rm file.txt
```

Then commit:

```bash
git commit -m "Remove obsolete file"
```

## Stop tracking but keep locally

```bash
git rm --cached file.txt
```

## Rename or move

```bash
git mv old.txt new.txt
```

Equivalent to moving the file and staging the result.

---

# 32. Aliases

Aliases reduce repetitive typing.

Example:

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
```

Now:

```bash
git st
git br
```

Useful log alias:

```bash
git config --global alias.lg "log --oneline --graph --decorate --all"
```

Then:

```bash
git lg
```

---

# 33. Useful Configuration

## Set default branch name

```bash
git config --global init.defaultBranch main
```

## Configure default editor

VS Code:

```bash
git config --global core.editor "code --wait"
```

Vim:

```bash
git config --global core.editor vim
```

## Enable helpful colors

```bash
git config --global color.ui auto
```

## Configure pull behavior explicitly

Merge:

```bash
git config --global pull.rebase false
```

Rebase:

```bash
git config --global pull.rebase true
```

Fast-forward only:

```bash
git config --global pull.ff only
```

Choose according to your team's workflow.

---

# 34. SSH With Git Hosting

SSH lets Git authenticate without repeatedly entering passwords.

## Generate an SSH key

Modern example:

```bash
ssh-keygen -t ed25519 -C "you@example.com"
```

Start the SSH agent:

```bash
eval "$(ssh-agent -s)"
```

Add the private key:

```bash
ssh-add ~/.ssh/id_ed25519
```

Print the public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Add the **public** key to your Git hosting account.

Never share:

```text
~/.ssh/id_ed25519
```

That is your private key.

---

# 35. Common Team Workflow

A typical team workflow:

## 1. Update main

```bash
git switch main
git pull
```

## 2. Create a branch

```bash
git switch -c feature/payment-api
```

## 3. Make changes

```bash
git status
git diff
```

## 4. Stage changes

```bash
git add .
```

## 5. Commit

```bash
git commit -m "Add payment API endpoint"
```

## 6. Push branch

```bash
git push -u origin feature/payment-api
```

## 7. Open a pull request

Use GitHub/GitLab/Bitbucket.

## 8. Address review feedback

```bash
git add .
git commit -m "Address review feedback"
git push
```

## 9. Merge after approval

Usually through the hosting platform.

## 10. Clean up locally

```bash
git switch main
git pull
git branch -d feature/payment-api
```

---

# 36. Feature Branch Workflow

Keep `main` stable.

Create one branch per feature or fix:

```text
main
├── feature/login
├── feature/payments
├── fix/session-timeout
└── chore/update-dependencies
```

Example branch names:

```text
feature/user-profile
fix/null-pointer
hotfix/payment-crash
chore/update-eslint
docs/api-guide
refactor/auth-service
```

A common pattern is:

```text
<type>/<short-description>
```

---

# 37. Pull Request Workflow

Before opening a pull request:

```bash
git status
git log --oneline
git diff main...HEAD
```

Optional: synchronize your branch with the latest `main`.

Merge approach:

```bash
git fetch origin
git merge origin/main
```

Rebase approach:

```bash
git fetch origin
git rebase origin/main
```

After a rebase on a branch already pushed by you, updating the remote may require:

```bash
git push --force-with-lease
```

Prefer:

```bash
git push --force-with-lease
```

over:

```bash
git push --force
```

`--force-with-lease` adds a safety check so you are less likely to overwrite someone else's remote work.

---

# 38. Rebase vs Merge

Both integrate branches.

## Merge

```bash
git merge feature
```

Advantages:

- preserves exact branch history
- does not rewrite existing commits
- safe for shared branches

Possible disadvantage:

- more merge commits
- history may become noisy

---

## Rebase

```bash
git rebase main
```

Advantages:

- clean linear history
- easy-to-follow commit sequence

Possible disadvantages:

- rewrites commit identities
- risky when used on shared history

---

## Practical rule

For public/shared history:

```text
prefer merge or carefully coordinated rebase
```

For cleaning your own local feature branch:

```text
rebase is often useful
```

---

# 39. Reset vs Revert vs Restore

This distinction is extremely important.

| Command | Main Purpose | Rewrites History? | Typical Use |
|---|---|---:|---|
| `git restore` | Restore files / unstage | No | Discard working changes |
| `git reset` | Move branch pointer / unstage | Yes, potentially | Rewrite local history |
| `git revert` | Reverse a commit with a new commit | No | Undo shared history |

Examples:

Discard local file edits:

```bash
git restore app.py
```

Unstage a file:

```bash
git restore --staged app.py
```

Undo last local commit but keep changes:

```bash
git reset --soft HEAD~1
```

Undo a pushed commit safely:

```bash
git revert <commit>
```

---

# 40. Common Problems and Fixes

## "I committed on the wrong branch"

Suppose the commit is only local.

Create the correct branch at the current commit:

```bash
git branch correct-branch
```

Move the current branch back:

```bash
git reset --hard HEAD~1
```

Switch:

```bash
git switch correct-branch
```

---

## "I forgot to add a file to the last commit"

```bash
git add forgotten-file.txt
git commit --amend --no-edit
```

---

## "My last commit message is wrong"

```bash
git commit --amend -m "Correct message"
```

---

## "I staged the wrong file"

```bash
git restore --staged secret.txt
```

---

## "I want to discard changes in one file"

```bash
git restore file.txt
```

Warning: unstaged changes will be lost.

---

## "I want to discard every tracked local change"

```bash
git reset --hard HEAD
```

Untracked files are not deleted by this command.

---

## "I deleted a branch accidentally"

Find the commit:

```bash
git reflog
```

Recreate the branch:

```bash
git branch recovered-branch <commit>
```

---

## "My push was rejected"

Often the remote contains commits you do not have.

Inspect first:

```bash
git fetch origin
git log --oneline --graph --decorate --all
```

Then integrate remote changes:

```bash
git pull
```

or explicitly:

```bash
git rebase origin/main
```

Then push:

```bash
git push
```

---

## "I am in detached HEAD state"

Detached HEAD means `HEAD` points directly to a commit rather than a branch.

Check:

```bash
git status
```

If you made useful commits, preserve them:

```bash
git switch -c recovery-branch
```

Now your commits are attached to a named branch.

---

## "I need to abort a merge"

```bash
git merge --abort
```

## "I need to abort a rebase"

```bash
git rebase --abort
```

## "I need to abort a cherry-pick"

```bash
git cherry-pick --abort
```

---

# 41. Advanced Git Commands

## Bisect

`git bisect` helps locate the commit that introduced a bug using binary search.

Start:

```bash
git bisect start
```

Mark current commit bad:

```bash
git bisect bad
```

Mark an older known-good commit:

```bash
git bisect good <commit>
```

Git checks out a midpoint.

Test the application, then mark:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

Continue until Git identifies the likely bad commit.

Finish:

```bash
git bisect reset
```

---

## Worktree

Worktrees let you have multiple branches checked out at the same time in different directories.

Example:

```bash
git worktree add ../project-hotfix hotfix
```

Now you can work in:

```text
project/
project-hotfix/
```

List worktrees:

```bash
git worktree list
```

Remove one:

```bash
git worktree remove ../project-hotfix
```

---

## Show object information

```bash
git cat-file -p <object>
```

Example:

```bash
git cat-file -p HEAD
```

Useful when learning Git internals.

---

## List tracked files

```bash
git ls-files
```

---

## Show repository root

```bash
git rev-parse --show-toplevel
```

## Show current commit hash

```bash
git rev-parse HEAD
```

## Show current branch

```bash
git branch --show-current
```

---

# 42. Recommended Commit Practices

Good commits are:

- focused
- understandable
- independently useful
- easy to review
- easy to revert

Bad commit:

```text
stuff
```

Better:

```text
Fix session expiration handling
```

Good examples:

```text
Add JWT authentication middleware
Fix duplicate payment submission
Refactor user validation service
Update Docker setup instructions
Remove deprecated API endpoint
```

---

## Commit one logical change at a time

Avoid:

```text
Commit:
- add login feature
- update README
- rename database tables
- change button styles
- upgrade 12 dependencies
```

Prefer several commits:

```text
Add login API endpoint
Add login form
Document authentication setup
Upgrade Express dependency
```

---

## Conventional Commits

Some teams use Conventional Commits.

Format:

```text
<type>(optional-scope): description
```

Examples:

```text
feat(auth): add password reset flow
fix(api): prevent duplicate requests
docs(readme): add local setup instructions
refactor(users): extract validation service
test(auth): add token expiration tests
chore(deps): update dependencies
```

Common types:

```text
feat
fix
docs
style
refactor
test
chore
build
ci
perf
```

---

# 43. Repository Hygiene

## Do not commit secrets

Avoid committing:

```text
.env
private keys
API keys
passwords
database credentials
cloud credentials
access tokens
```

Use environment variables or a secret manager.

If a secret was committed, deleting the file in a later commit may not be enough because the secret can remain in Git history.

Rotate exposed credentials immediately.

---

## Commit lock files when appropriate

Depending on the ecosystem, lock files are often intended to be version controlled.

Examples:

```text
package-lock.json
yarn.lock
pnpm-lock.yaml
poetry.lock
Cargo.lock
```

Follow your ecosystem and team conventions.

---

## Avoid giant unrelated commits

Small logical commits make:

- review easier
- debugging easier
- cherry-picking easier
- reverting safer
- history clearer

---

# 44. Practice Exercises

The best way to learn Git is to create a disposable repository and intentionally break things.

---

## Exercise 1 — First repository

```bash
mkdir git-practice
cd git-practice
git init
```

Create a file:

```bash
echo "# Git Practice" > README.md
```

Inspect:

```bash
git status
```

Stage:

```bash
git add README.md
```

Commit:

```bash
git commit -m "Add README"
```

View history:

```bash
git log --oneline
```

---

## Exercise 2 — Modify and inspect

Modify:

```bash
echo "Learning Git" >> README.md
```

Inspect:

```bash
git diff
git status
```

Stage:

```bash
git add README.md
```

Inspect staged changes:

```bash
git diff --staged
```

Commit:

```bash
git commit -m "Update README"
```

---

## Exercise 3 — Branching

Create branch:

```bash
git switch -c feature/about
```

Add file:

```bash
echo "About page" > about.txt
git add about.txt
git commit -m "Add about page"
```

Return to main:

```bash
git switch main
```

Merge:

```bash
git merge feature/about
```

Delete branch:

```bash
git branch -d feature/about
```

---

## Exercise 4 — Conflict

Create branch:

```bash
git switch -c conflict-demo
```

Edit the same line in a file and commit.

Switch to main:

```bash
git switch main
```

Edit that same line differently and commit.

Then merge:

```bash
git merge conflict-demo
```

Resolve the conflict manually.

Stage:

```bash
git add .
```

Commit:

```bash
git commit
```

---

## Exercise 5 — Undo working changes

Modify a file:

```bash
echo "Temporary mistake" >> README.md
```

Discard it:

```bash
git restore README.md
```

---

## Exercise 6 — Stash

Modify a file.

Then:

```bash
git stash push -m "Temporary work"
```

Verify:

```bash
git status
```

Restore:

```bash
git stash pop
```

---

## Exercise 7 — Soft reset

Create a commit:

```bash
git add .
git commit -m "Temporary commit"
```

Undo commit while preserving staged changes:

```bash
git reset --soft HEAD~1
```

Inspect:

```bash
git status
```

---

## Exercise 8 — Reflog recovery

Create two commits.

Then intentionally run:

```bash
git reset --hard HEAD~1
```

Inspect:

```bash
git reflog
```

Find the lost commit and recover it:

```bash
git branch recovered <commit>
```

---

## Exercise 9 — Rebase

Create a branch:

```bash
git switch -c feature/rebase-demo
```

Create two commits.

Switch to main:

```bash
git switch main
```

Create another commit.

Then:

```bash
git switch feature/rebase-demo
git rebase main
```

Visualize:

```bash
git log --oneline --graph --decorate --all
```

---

# 45. Git Command Cheat Sheet

## Setup

```bash
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --list
```

## Repository

```bash
git init
git clone <url>
git status
```

## Stage

```bash
git add file.txt
git add .
git add -A
git restore --staged file.txt
```

## Commit

```bash
git commit -m "Message"
git commit -am "Message"
git commit --amend
git commit --amend --no-edit
```

## Diff

```bash
git diff
git diff --staged
git diff main..feature
git diff <commit1> <commit2>
```

## History

```bash
git log
git log --oneline
git log --oneline --graph --decorate --all
git show <commit>
git reflog
```

## Branch

```bash
git branch
git branch -a
git switch branch-name
git switch -c new-branch
git branch -m new-name
git branch -d branch-name
git branch -D branch-name
```

## Merge

```bash
git merge branch-name
git merge --abort
```

## Remote

```bash
git remote -v
git remote add origin <url>
git remote set-url origin <url>
git remote remove origin
```

## Push

```bash
git push
git push origin main
git push -u origin feature
git push --force-with-lease
```

## Fetch / Pull

```bash
git fetch
git fetch origin
git pull
git pull origin main
```

## Restore

```bash
git restore file.txt
git restore --staged file.txt
git restore --source=<commit> file.txt
```

## Reset

```bash
git reset --soft HEAD~1
git reset HEAD~1
git reset --hard HEAD~1
```

## Revert

```bash
git revert <commit>
```

## Stash

```bash
git stash
git stash -u
git stash list
git stash apply
git stash pop
git stash drop stash@{0}
git stash clear
```

## Rebase

```bash
git rebase main
git rebase -i HEAD~5
git rebase --continue
git rebase --abort
```

## Cherry-pick

```bash
git cherry-pick <commit>
git cherry-pick --continue
git cherry-pick --abort
```

## Tags

```bash
git tag
git tag v1.0.0
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
git push origin --tags
```

## Clean

```bash
git clean -n
git clean -f
git clean -fd
```

## Files

```bash
git rm file.txt
git rm --cached file.txt
git mv old.txt new.txt
git ls-files
```

## Debugging

```bash
git blame file.txt
git bisect start
git reflog
```

---

# 46. Mental Model

If you remember only one section, remember this.

## Normal development

```text
edit
  ↓
git status
  ↓
git diff
  ↓
git add
  ↓
git diff --staged
  ↓
git commit
  ↓
git push
```

## Synchronize with remote

```text
git fetch
  ↓
inspect differences
  ↓
merge or rebase
  ↓
git push
```

## Feature development

```text
git switch main
git pull
git switch -c feature/my-feature

# work
git add .
git commit -m "Add my feature"

git push -u origin feature/my-feature
```

## Safest undo hierarchy

Before running a destructive command, ask:

```text
Is the change only in my working directory?
    -> git restore

Is it only staged?
    -> git restore --staged

Is it in a local unshared commit?
    -> git reset may be appropriate

Has it been pushed/shared?
    -> git revert is usually safer

Did I accidentally lose a commit?
    -> git reflog
```

---

# Final Git Rules to Remember

1. Run `git status` constantly.
2. Use `git diff` before staging.
3. Use `git diff --staged` before committing.
4. Make small, logical commits.
5. Write meaningful commit messages.
6. Create feature branches instead of working directly on `main`.
7. Fetch or pull before integrating work from others.
8. Do not casually force-push shared branches.
9. Prefer `git push --force-with-lease` over `git push --force` when history rewriting is necessary.
10. Use `git revert` for undoing shared commits.
11. Use `git reflog` when you think work has disappeared.
12. Never commit credentials or private keys.
13. Understand the staging area—it is one of Git's most important concepts.
14. Learn the difference between `merge` and `rebase`.
15. Experiment in disposable repositories until recovery commands feel natural.

---

# Suggested Learning Order

If you are starting from zero, learn Git in this order:

```text
Level 1
------
git init
git clone
git status
git add
git commit
git log

Level 2
------
git diff
git restore
.gitignore
git branch
git switch
git merge

Level 3
------
git remote
git fetch
git pull
git push
tracking branches

Level 4
------
git stash
git revert
git reset
git reflog

Level 5
------
git rebase
interactive rebase
cherry-pick
tags

Level 6
------
bisect
worktree
history searching
advanced recovery
team workflows
```

Once you can confidently explain **why** you are using each command—not merely memorize it—you have moved from basic Git usage toward genuine Git fluency.
