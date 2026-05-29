# Git Commands for QA Testers

A practical reference for QA engineers working with Git day-to-day.

---

## 1. Setup & Configuration

```bash
# Set your identity
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Check your current config
git config --list
```

---

## 2. Cloning & Getting the Code

```bash
# Clone a repository
git clone https://github.com/org/repo.git

# Clone a specific branch
git clone -b develop https://github.com/org/repo.git

# Clone into a named folder
git clone https://github.com/org/repo.git my-folder
```

---

## 3. Checking Out Branches & Commits

```bash
# List all branches (local + remote)
git branch -a

# Switch to an existing branch
git checkout develop

# Check out a specific branch for testing
git checkout feature/login-redesign

# Check out a specific commit (for regression testing)
git checkout abc1234

# Return to the latest commit on a branch
git checkout develop
```

---

## 4. Viewing History & Changes

```bash
# View commit history (concise)
git log --oneline

# View history with author and date
git log --oneline --decorate --graph --all

# Show changes in a specific commit
git show abc1234

# Show what changed between two branches
git diff main..feature/my-feature

# Show changed files only (no diff content)
git diff --name-only main..feature/my-feature

# Show changes not yet staged
git diff

# Show staged changes
git diff --staged
```

---

## 5. Identifying What Changed

```bash
# Who changed what line in a file (blame)
git blame src/login.js

# Search commit messages for a keyword
git log --all --grep="fix login"

# Search for when a specific string was added/removed
git log -S "error message text" --oneline

# Find which commit introduced a file
git log --follow -- path/to/file.js
```

---

## 6. Comparing Versions for Regression Testing

```bash
# Compare current branch to main
git diff main

# Compare two specific tags (e.g., releases)
git diff v1.2.0 v1.3.0

# Compare two specific commits
git diff abc1234 def5678

# List files changed between two releases
git diff --name-status v1.2.0 v1.3.0
```

---

## 7. Working with Tags (Releases)

```bash
# List all tags
git tag

# Check out a specific release tag
git checkout v1.3.0

# See the commit a tag points to
git show v1.3.0 --stat

# List tags with their messages
git tag -n
```

---

## 8. Stashing Work in Progress

```bash
# Stash your uncommitted changes before switching branches
git stash

# List stashes
git stash list

# Apply the most recent stash
git stash pop

# Apply a specific stash
git stash apply stash@{2}

# Drop a stash you no longer need
git stash drop stash@{0}
```

---

## 9. Finding Bugs with Bisect

Use `git bisect` to find the exact commit that introduced a bug.

```bash
# Start bisect
git bisect start

# Mark the current commit as bad (bug is present)
git bisect bad

# Mark a known good commit (bug was not present)
git bisect good v1.2.0

# Git will check out a commit — test it, then mark it:
git bisect good   # if bug is NOT present
git bisect bad    # if bug IS present

# Repeat until Git identifies the culprit commit

# Reset when done
git bisect reset
```

---

## 10. Checking File & Commit Status

```bash
# See the current state of the working directory
git status

# Check which branch you're on
git branch

# Confirm the last commit (author, date, message)
git log -1

# Confirm the exact commit hash of HEAD
git rev-parse HEAD
```

---

## 11. Pulling & Syncing

```bash
# Update your local branch with remote changes
git pull

# Fetch without merging (to inspect before applying)
git fetch origin

# See what's new on remote without applying
git log HEAD..origin/develop --oneline
```

---

## 12. Creating Bug Report Branches

```bash
# Create a branch to reproduce/document a bug
git checkout -b bug/BUG-1234-login-crash

# Push to remote for collaboration
git push origin bug/BUG-1234-login-crash
```

---

## 13. Reverting & Resetting (Use with Care)

```bash
# Undo the last commit but keep the changes staged
git reset --soft HEAD~1

# Undo changes to a specific file (discard edits)
git checkout -- path/to/file.js

# Revert a commit safely (creates a new undo commit)
git revert abc1234
```

---

## 14. Useful Aliases for QA Workflows

Add these to your `~/.gitconfig` under `[alias]`:

```ini
[alias]
  st = status
  co = checkout
  lg = log --oneline --decorate --graph --all
  last = log -1 HEAD
  changed = diff --name-only
  who = blame
```

---

## Quick Reference

| Task | Command |
|---|---|
| Switch to a branch | `git checkout branch-name` |
| See recent commits | `git log --oneline` |
| Compare branches | `git diff main..feature/x` |
| Find who changed a line | `git blame file.js` |
| Search commit messages | `git log --grep="keyword"` |
| Check out a release tag | `git checkout v1.0.0` |
| Find a bug's commit | `git bisect start` |
| Save work temporarily | `git stash` |
| See what files changed | `git diff --name-only` |
| Show a commit's changes | `git show abc1234` |

---

*Tip: Always confirm which branch you're on with `git branch` before starting any test session.*
