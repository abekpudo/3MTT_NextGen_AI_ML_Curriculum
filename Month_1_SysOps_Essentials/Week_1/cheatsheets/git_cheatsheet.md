 Git Cheatsheet

 Configuration
```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --list
```

 Getting Started
```bash
git init                     Initialize repository
git clone <url>              Clone repository
git clone <url> <dirname>    Clone to specific folder
```

 Checking Status
```bash
git status                   Short status
git log                      View history
git log --oneline           Compact history
git log --graph --all       Visual branch history
git diff                     Unstaged changes
git diff --staged            Staged changes
git show <commit>            View specific commit
```

 Staging Changes
```bash
git add <file>               Stage specific file
git add .                    Stage all changes
git add *.py                 Stage all Python files
git reset <file>             Unstage file
git restore <file>           Discard changes
```

 Committing
```bash
git commit -m "message"      Create commit with message
git commit -am "message"     Add and commit all
git commit --amend           Modify last commit
git commit --amend --no-edit  Modify last commit, keep message
```

 Branches
```bash
git branch                   List branches
git branch -a                List all branches
git branch <name>            Create branch
git checkout <branch>        Switch branch
git checkout -b <branch>     Create and switch
git branch -d <branch>       Delete branch (safe)
git branch -D <branch>       Delete branch (force)
git branch -m <old> <new>    Rename branch
```

 Merging & Rebasing
```bash
git merge <branch>           Merge branch
git merge <branch> --no-ff   Merge with merge commit
git rebase <branch>          Rebase on another branch
git merge --abort            Cancel merge
git rebase --abort           Cancel rebase
```

 Undoing Changes
```bash
git restore <file>           Discard unstaged changes
git reset HEAD <file>        Unstage file
git reset --soft HEAD~1      Undo last commit (keep changes)
git reset --hard HEAD~1      Undo last commit (lose changes)
git revert <commit>          Create opposite commit
git checkout <commit> -- <file>   Restore old version
```

 Remote Operations
```bash
git remote -v                List remotes
git remote add <name> <url>  Add remote
git fetch <remote>           Fetch remote changes
git pull <remote> <branch>   Fetch and merge
git push <remote> <branch>   Push branch
git push -u <remote> <branch>   Push and set upstream
git push <remote> --delete <branch>   Delete remote branch
```

 Debugging
```bash
git blame <file>             Who changed what
git bisect                   Find bad commit
git reflog                   View recent references
git tag <name>               Create tag
```

 Common Workflows

 Starting a Feature
```bash
git checkout main
git pull origin main
git checkout -b feature/my-feature
```

 Finishing a Feature
```bash
git add .
git commit -m "Complete feature"
git push -u origin feature/my-feature
 Create PR on GitHub
```

 Syncing with Main
```bash
git fetch origin
git rebase origin/main
 or
git merge origin/main
```

 Cleanup
```bash
git branch -d feature/completed
git push origin --delete feature/completed
```

---

 Common Patterns

 Commit Message Format
```
[Type] Brief description

Optional longer description

Fixes 123
```

Types: feat, fix, refactor, style, test, docs, chore

 Good Commit Message Examples
```
feat: Add user authentication
fix: Handle null pointer in data processor
refactor: Simplify database queries
docs: Update README with setup instructions
```

 Not-So-Good Examples
```
fix stuff
asdf
work in progress
changes
```

---

 Tips & Tricks

 View what changed in last commit
```bash
git show
```

 Compare branches
```bash
git diff main feature
```

 See who committed what
```bash
git log -p                   Show diffs
git log --author="name"      Filter by author
git log --since="2 weeks ago"   Filter by date
```

 Stash work temporarily
```bash
git stash                    Save changes
git stash list               List stashes
git stash pop                Apply latest stash
```

 Create an alias
```bash
git config --global alias.lg "log --graph --oneline --all"
git lg                       Much better!
```

---

 Emergency Commands

 "I lost my commits!"
```bash
git reflog
git reset --hard <ref>       Restore from reflog
```

 "I committed to wrong branch"
```bash
git reset --soft HEAD~1
git stash
git checkout correct-branch
git stash pop
git commit -m "message"
```

 "I need to undo a pushed commit"
```bash
git revert <commit>
git push
```

 "I want to rewrite history"
```bash
git rebase -i HEAD~3         Interactive rebase last 3 commits
```

⚠️ WARNING: Only rewrite history on branches you haven't shared!
