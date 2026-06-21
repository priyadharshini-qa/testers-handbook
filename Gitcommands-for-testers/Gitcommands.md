# Git Commands for QA Testers

A practical reference for QA engineers working with Git day-to-day.

---

## Initial Setup

| Command | Example | Description |
|---|---|---|
| `git init` | `git init` | Initializes a new Git repository in the current directory |
| `git clone` | `git clone https://github.com/org/repo-name.git` | Clones a remote repository to local machine |
| `git config --global user.name` | `git config --global user.name "qausername"` | Sets the global username for commits |
| `git config --global user.email` | `git config --global user.email "your.email@company.com"` | Sets the global email for commits |

## Daily Workflow

| Command | Example | Description |
|---|---|---|
| `git status` | `git status` | Shows changed, staged, and untracked files in working directory |
| `git pull` | `git pull origin main` | Fetches and merges latest changes from remote branch into current branch |
| `git checkout -b` | `git checkout -b feature/login-test-suite` | Creates and switches to a new branch |
| `git add` | `git add .` | Stages all modified/new files for commit |
| `git add -p` | `git add -p` | Interactively stages selected hunks of changes for review before committing |
| `git commit -m` | `git commit -m "feat: add login test cases for negative scenarios"` | Commits staged changes with a message |
| `git push` | `git push origin feature/login-test-suite` | Pushes local branch and commits to remote repository |

## Branch Management

| Command | Example | Description |
|---|---|---|
| `git branch -a` | `git branch -a` | Lists all local and remote branches |
| `git branch -d` | `git branch -d feature/old-branch` | Deletes a local branch that's already merged |
| `git checkout` | `git checkout main` | Switches to the specified branch |
| `git merge` | `git merge feature/login-test-suite` | Merges specified branch into current branch |
| `git rebase` | `git rebase main` | Reapplies current branch's commits on top of main, keeping history linear |

## Inspecting History

| Command | Example | Description |
|---|---|---|
| `git log` | `git log --oneline --graph --decorate` | Shows commit history in a compact, visual graph format |
| `git diff` | `git diff HEAD~1` | Shows changes between the last commit and the one before it |
| `git blame` | `git blame tests/login_test.java` | Shows who last modified each line of a file and in which commit |
| `git show` | `git show <commit-hash>` | Displays details and changes of a specific commit |

## Undo / Fix Mistakes

| Command | Example | Description |
|---|---|---|
| `git stash` | `git stash` | Temporarily saves uncommitted changes without committing them |
| `git stash pop` | `git stash pop` | Restores the most recently stashed changes back to working directory |
| `git reset --soft` | `git reset --soft HEAD~1` | Undoes the last commit but keeps changes staged |
| `git revert` | `git revert <commit-hash>` | Creates a new commit that undoes changes from a specific commit (safe for shared history) |
| `git checkout --` | `git checkout -- <file>` | Discards uncommitted changes in a specific file |

## Tags for Releases

| Command | Example | Description |
|---|---|---|
| `git tag -a` | `git tag -a v1.2.0 -m "Release 1.2.0 - regression suite"` | Creates an annotated tag marking a release point |
| `git push origin <tag>` | `git push origin v1.2.0` | Pushes a specific tag to the remote repository |