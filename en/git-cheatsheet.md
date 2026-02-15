# 🌿 Git Cheatsheet

## ⚙️ Initial Configuration

``` bash
git config --global user.name "Your Name"        # Set user name
git config --global user.email "you@example.com" # Set email
git config --global init.defaultBranch main      # Set default branch name
git config --list                                # View configuration
git config --global core.editor "vim"            # Set default editor
git config --global color.ui auto                # Enable colors
```

------------------------------------------------------------------------

## 🆕 Creating and Cloning Repositories

``` bash
git init                              # Create new repository in current folder
git init my-project                   # Create new folder and repository
git clone https://github.com/user/repo.git      # Clone repository from server
git clone https://github.com/user/repo.git mydir # Clone with custom folder name
```

------------------------------------------------------------------------

## 📝 Changes and Staging

``` bash
git status                            # Current file status
git status -s                         # Short output
git add file.txt                      # Add file to staging
git add .                             # Add all modified files
git add *.js                          # Add files with specific pattern
git add -p                            # Interactive add (hunk by hunk)
git rm file.txt                       # Remove file from git and filesystem
git rm --cached file.txt              # Remove file from git only (keep local)
git mv old.txt new.txt                # Rename file
```

------------------------------------------------------------------------

## 💾 Committing

``` bash
git commit -m "commit message"        # Commit changes with message
git commit -am "message"              # Add and commit simultaneously (tracked files only)
git commit --amend                    # Edit last commit
git commit --amend -m "new message"   # Change last commit message
git commit --amend --no-edit          # Add changes to previous commit without changing message
```

------------------------------------------------------------------------

## 📜 History and Log

``` bash
git log                               # Commit history
git log --oneline                     # Short display (one line per commit)
git log --graph                       # Graphical display of branches
git log --all --graph --oneline       # Complete display of all branches
git log -n 5                          # Display last 5 commits
git log --since="2 weeks ago"         # Commits from last 2 weeks
git log --author="Ali"                # Commits from specific person
git log --grep="fix"                  # Search in commit messages
git log file.txt                      # History of specific file
git log -p                            # Display diff for each commit
git show <commit-hash>                # Display details of a commit
git show HEAD                         # Display last commit
```

------------------------------------------------------------------------

## 🔍 Comparison and Differences

``` bash
git diff                              # Unstaged changes
git diff --staged                     # Staged changes
git diff HEAD                         # All changes (staged + unstaged)
git diff branch1 branch2              # Compare two branches
git diff commit1 commit2              # Compare two commits
git diff --stat                       # Statistical summary of changes
```

------------------------------------------------------------------------

## 🌿 Branch Management

``` bash
git branch                            # List local branches
git branch -a                         # List all branches (local and remote)
git branch feature-x                  # Create new branch
git branch -d feature-x               # Delete branch (safe delete)
git branch -D feature-x               # Force delete branch
git branch -m old-name new-name       # Rename branch
git checkout feature-x                # Switch to another branch
git checkout -b feature-x             # Create and switch to new branch
git switch feature-x                  # Switch branch (new method)
git switch -c feature-x               # Create and switch (new method)
```

------------------------------------------------------------------------

## 🔀 Merging

``` bash
git merge feature-x                   # Merge feature-x into current branch
git merge --no-ff feature-x           # Merge without fast-forward
git merge --squash feature-x          # Merge all commits into one commit
git merge --abort                     # Cancel merge on conflict
```

------------------------------------------------------------------------

## 🔄 Rebase

``` bash
git rebase main                       # Apply current commits on top of main
git rebase -i HEAD~3                  # Interactive rebase of last 3 commits
git rebase --continue                 # Continue rebase after resolving conflict
git rebase --abort                    # Cancel rebase
git rebase --skip                     # Skip current commit
```

------------------------------------------------------------------------

## 🔗 Working with Remote

``` bash
git remote                            # List remotes
git remote -v                         # Display URLs
git remote add origin <url>           # Add new remote
git remote remove origin              # Remove remote
git remote rename origin upstream     # Rename remote
git remote show origin                # Complete remote information
git fetch                             # Fetch changes without merge
git fetch --all                       # Fetch from all remotes
git pull                              # Fetch and merge changes
git pull --rebase                     # Fetch with rebase instead of merge
git push                              # Push changes to remote
git push origin main                  # Push specific branch
git push -u origin main               # Push and set upstream
git push --all                        # Push all branches
git push --tags                       # Push tags
git push --force                      # Force push (dangerous)
git push --force-with-lease           # Safer force push
```

------------------------------------------------------------------------

## 🏷 Tags

``` bash
git tag                               # List tags
git tag v1.0.0                        # Create lightweight tag
git tag -a v1.0.0 -m "Version 1.0"    # Create annotated tag
git tag -a v1.0.0 <commit-hash>       # Tag specific commit
git show v1.0.0                       # Display tag information
git tag -d v1.0.0                     # Delete local tag
git push origin v1.0.0                # Push a tag
git push origin --delete v1.0.0       # Delete tag from remote
```

------------------------------------------------------------------------

## ↩️ Undoing Changes

``` bash
git restore file.txt                  # Restore file to last commit
git restore --staged file.txt         # Unstage file
git checkout -- file.txt              # Restore file (old method)
git reset HEAD file.txt               # Unstage file
git reset --soft HEAD~1               # Remove last commit (keep changes in staging)
git reset --mixed HEAD~1              # Remove last commit (keep changes unstaged)
git reset --hard HEAD~1               # Completely remove last commit
git reset --hard <commit-hash>        # Return to specific commit
git revert <commit-hash>              # Create new commit to undo changes
```

------------------------------------------------------------------------

## 🗂 Stash (Temporary Save)

``` bash
git stash                             # Save current changes
git stash save "work in progress"     # Save with message
git stash -u                          # Save including untracked files
git stash list                        # List stashes
git stash show                        # Display last stash
git stash show -p                     # Display diff of last stash
git stash apply                       # Apply last stash (keep stash)
git stash pop                         # Apply and remove last stash
git stash apply stash@{2}             # Apply specific stash
git stash drop                        # Remove last stash
git stash drop stash@{2}              # Remove specific stash
git stash clear                       # Remove all stashes
git stash branch feature-x            # Create branch from stash
```

------------------------------------------------------------------------

## 🔍 Search and Inspection

``` bash
git grep "search term"                # Search in project files
git grep -n "search term"             # Search with line number
git blame file.txt                    # Show author of each line
git blame -L 10,20 file.txt           # Blame for specific lines
git bisect start                      # Start binary search for bug
git bisect bad                        # Mark bad commit
git bisect good <commit>              # Mark good commit
git bisect reset                      # End bisect
```

------------------------------------------------------------------------

## 🧹 Cleanup

``` bash
git clean -n                          # Display untracked files (preview)
git clean -f                          # Remove untracked files
git clean -fd                         # Remove untracked files and folders
git clean -fX                         # Remove ignored files
git clean -fx                         # Remove everything (untracked + ignored)
git gc                                # Optimize repository (garbage collection)
git prune                             # Remove unreachable objects
```

------------------------------------------------------------------------

## 🔧 Cherry-pick

``` bash
git cherry-pick <commit-hash>         # Apply specific commit to current branch
git cherry-pick <hash1> <hash2>       # Apply multiple commits
git cherry-pick --continue            # Continue after resolving conflict
git cherry-pick --abort               # Cancel operation
```

------------------------------------------------------------------------

## 🎯 Submodule

``` bash
git submodule add <url> path/to/submodule  # Add submodule
git submodule init                    # Initialize submodules
git submodule update                  # Fetch submodule code
git submodule update --init --recursive    # Init and update simultaneously
git clone --recursive <url>           # Clone with submodules
```

------------------------------------------------------------------------

## 📋 .gitignore

Example `.gitignore` file:

```
# System files
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
*.swp

# Dependencies
node_modules/
vendor/

# Build
dist/
build/
*.log

# Environment
.env
.env.local

# Temporary
*.tmp
temp/
```

------------------------------------------------------------------------

## 🔐 Git Hooks (Advanced)

``` bash
# Hooks are located in .git/hooks/
# Example pre-commit hook:

#!/bin/bash
# .git/hooks/pre-commit

npm test
if [ $? -ne 0 ]; then
    echo "Tests failed, commit aborted"
    exit 1
fi
```

------------------------------------------------------------------------

## 🌐 Git Workflows

### Feature Branch Workflow

``` bash
git checkout -b feature/new-feature   # Create new branch
# ... development and commits
git push -u origin feature/new-feature
# Create Pull Request in GitHub/GitLab
# After review and merge:
git checkout main
git pull
git branch -d feature/new-feature
```

### Gitflow Workflow

``` bash
# Main branches: main, develop
git checkout -b develop               # Create develop branch

# Start new feature
git checkout -b feature/x develop
# ... development
git checkout develop
git merge --no-ff feature/x

# Start release
git checkout -b release/1.0 develop
# ... preparation
git checkout main
git merge --no-ff release/1.0
git tag -a v1.0

# Hotfix
git checkout -b hotfix/1.0.1 main
# ... bug fix
git checkout main
git merge --no-ff hotfix/1.0.1
git checkout develop
git merge --no-ff hotfix/1.0.1
```

------------------------------------------------------------------------

## 🚀 Advanced Commands

### Reflog (Complete History)

``` bash
git reflog                            # History of all HEAD changes
git reflog show branch-name           # Reflog of specific branch
git reset --hard HEAD@{2}             # Return to previous state
```

### Worktree (Multiple Versions Simultaneously)

``` bash
git worktree add ../project-feature feature-branch  # Create worktree
git worktree list                     # List worktrees
git worktree remove ../project-feature # Remove worktree
```

### Patch (Create and Apply)

``` bash
git format-patch -1 HEAD              # Create patch from last commit
git format-patch -3                   # Create patch from last 3 commits
git apply patch-file.patch            # Apply patch
git am patch-file.patch               # Apply patch preserving commit info
```

### Interactive Add

``` bash
git add -i                            # Interactive mode
git add -p                            # Select specific hunks
# In interactive mode:
# y = yes, n = no, s = split, e = edit
```

------------------------------------------------------------------------

## 🐛 Debugging and Troubleshooting

``` bash
git status                            # Check status
git diff                              # View changes
git log --all --graph --oneline       # Overview of history
git fsck                              # Check repository integrity
git reflog                            # Find lost commits
git show :0:file.txt                  # Display staged version
git ls-files                          # List tracked files
git ls-files -u                       # Conflicted files
```

------------------------------------------------------------------------

## 📦 GitHub/GitLab Specific

``` bash
# Fork and collaboration
git remote add upstream <original-repo-url>  # Add original repository
git fetch upstream                    # Fetch changes from original repository
git merge upstream/main               # Merge with original repository

# Pull Request from CLI (with GitHub CLI)
gh pr create                          # Create PR
gh pr list                            # List PRs
gh pr checkout 123                    # Checkout PR
```

------------------------------------------------------------------------

## ⚡ Aliases (Shortcuts)

``` bash
# Set aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lg "log --graph --oneline --all"

# Usage:
git st                                # Equivalent to git status
git lg                                # Graphical log
```

------------------------------------------------------------------------

## 🏭 Production Best Practices

- ✅ Make small, logical commits
- ✅ Write clear and descriptive commit messages
- ✅ Pull before push to reduce conflicts
- ✅ Use `.gitignore` for sensitive files
- ✅ Test before merge
- ✅ Use feature branches
- ⚠️ Be careful with `--force` (better to use `--force-with-lease`)
- ⚠️ Keep merge commits (don't overuse squash)
- 🔐 Never commit passwords and keys
- 📝 Use Conventional Commits:
  - `feat:` for new features
  - `fix:` for bug fixes
  - `docs:` for documentation
  - `refactor:` for code refactoring

------------------------------------------------------------------------

## 🔑 Security Tips

``` bash
# Remove sensitive file from history
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD
# Or with newer tool:
git filter-repo --path passwords.txt --invert-paths

# Sign commits with GPG
git config --global user.signingkey <key-id>
git config --global commit.gpgsign true
git commit -S -m "Signed commit"

# Verify commit signature
git log --show-signature
```

------------------------------------------------------------------------

## 📊 Statistics

``` bash
git shortlog -sn                      # Number of commits per person
git shortlog -sn --all --no-merges    # Without merges
git log --author="Ali" --oneline --shortstat  # Stats for one person
git diff --stat                       # Change statistics
git log --since="1 month ago" --oneline | wc -l  # Number of commits last month
```

------------------------------------------------------------------------

## 💡 Pro Tips

### Finding the commit that introduced a bug:

``` bash
git bisect start
git bisect bad                        # Current commit has bug
git bisect good v1.0                  # Version 1.0 was fine
# git automatically tests commits
# At each step, test the bug and say:
git bisect good   # or
git bisect bad
# Until finding the problematic commit
git bisect reset
```

### Interactive Rebase for cleanup:

``` bash
git rebase -i HEAD~5
# In editor:
# pick = keep
# squash = merge with previous commit
# reword = change message
# edit = edit commit
# drop = remove commit
```

### Recover deleted commit:

``` bash
git reflog
# Find hash of lost commit
git checkout <commit-hash>
git checkout -b recovered-branch
```
