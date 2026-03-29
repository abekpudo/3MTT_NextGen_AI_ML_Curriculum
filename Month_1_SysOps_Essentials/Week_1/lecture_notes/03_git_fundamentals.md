 Lecture 3: Git Fundamentals

 Why Git Matters

Git is the most important tool in professional software development. Understanding it deeply is non-negotiable. Every single project you'll work on uses Git.

---

 🎯 Learning Objectives

- ✅ Understand version control concepts
- ✅ Master Git basic commands
- ✅ Understand Git workflow and staging
- ✅ Work with branches effectively
- ✅ Follow Git best practices

---

 📖 What is Git?

Git is a distributed version control system that allows you to:
- Track changes to your code over time
- Collaborate with others
- Revert to previous versions
- Create branches for experimental features
- Maintain code history

 Why Distributed?
Unlike centralized systems, Git stores the complete history locally on your machine AND on a server (like GitHub). Everyone has a full backup.

---

 🔄 Git Workflow

 The Three Stages of Git

```
Working Directory  →  Staging Area  →  Repository (.git)
   (your files)      (git add)       (git commit)
```

Working Directory: Where you edit files
Staging Area: Where you prepare changes for commit
Repository: Where Git stores your history

---

 📝 Essential Git Commands

 Initial Setup

```bash
 Configure Git (one-time)
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor "vim"
```

 Creating a Repository

```bash
 Method 1: Initialize local repository
cd my-project
git init

 Method 2: Clone existing repository
git clone https://github.com/username/repo.git
```

 Checking Status

```bash
 See current status
git status

 See detailed changes
git diff                     Changes not staged
git diff --staged            Changes staged for commit
git diff HEAD~1              Compare with last commit
```

 Making Changes

```bash
 Stage specific files
git add file1.py file2.py

 Stage all changes
git add .

 Unstage a file
git reset file1.py

 Discard changes (be careful!)
git restore file1.py
```

 Committing

```bash
 Create a commit
git commit -m "Clear description of changes"

 Add and commit in one step
git commit -am "Message"

 Amend last commit (only if not pushed)
git commit --amend --no-edit
```

 Viewing History

```bash
 See commit history
git log

 Pretty log format
git log --oneline --graph --all

 See specific file history
git log -- filename.py

 See who changed each line
git blame filename.py
```

---

 🌳 Branching

Branches allow you to work on features independently.

 Branch Commands

```bash
 List branches
git branch               Local only
git branch -a            All branches (local + remote)

 Create a branch
git branch feature-name
git branch -b feature-name       Create and switch

 Switch branches
git checkout feature-name
git switch feature-name          Newer syntax

 Delete branch
git branch -d feature-name       Local
git push origin --delete feature-name   Remote

 Rename branch
git branch -m old-name new-name
```

 Merge Branches

```bash
 Switch to target branch
git checkout main

 Merge feature branch
git merge feature-name

 If conflicts occur, resolve them manually, then:
git add .
git commit -m "Resolve merge conflicts"
```

---

 🔗 Working with Remote Repositories

 Remote Commands

```bash
 List remote repositories
git remote -v

 Add remote
git remote add origin https://github.com/username/repo.git

 Fetch changes (download, don't merge)
git fetch origin

 Pull changes (fetch + merge)
git pull origin main

 Push changes
git push origin main

 Set upstream (so you can just 'git push')
git push -u origin main
git branch -u origin/main
```

---

 💡 Git Best Practices

 Commit Messages
Good commit messages are crucial.

```
✅ GOOD
- git commit -m "Add user authentication to login page"
- git commit -m "Fix null pointer exception in data processor"
- git commit -m "Refactor database connection pooling"

❌ BAD
- git commit -m "fix stuff"
- git commit -m "changes"
- git commit -m "asdfgh"
```

Format:
```
[Type] Brief description (50 chars max)

Optional longer description explaining:
- What changed and why
- Any side effects
- Related issues

Example:
feat: Add email verification for new users

- Implement email verification flow
- Add verification token generation
- Update user model with email_verified field
- Closes 123
```

 Types of Commits
- feat: New feature
- fix: Bug fix
- refactor: Code reorganization
- style: Formatting (no logic change)
- test: Add tests
- docs: Documentation
- chore: Maintenance tasks

 Branching Strategy

```
main (production-ready code)
  ↑
develop (integration branch)
  ↑
feature/* (individual features)
```

Example workflow:
```bash
 Create feature branch from develop
git checkout develop
git pull
git checkout -b feature/user-auth

 Work on feature
 Commit regularly

 Prepare for merge
git pull origin develop   Get latest changes

 Merge back to develop
git checkout develop
git pull
git merge feature/user-auth
git push origin develop

 Delete feature branch
git branch -d feature/user-auth
```

---

 ⚠️ Common Mistakes & Fixes

 Committed to Wrong Branch

```bash
 Undo last commit (keep changes)
git reset --soft HEAD~1
git stash
git checkout correct-branch
git stash pop
git commit -m "proper message"
```

 Need to Undo Commits

```bash
 Undo last 3 commits (keep changes)
git reset --soft HEAD~3

 Undo last 3 commits (discard changes)
git reset --hard HEAD~3
```

 Accidentally Deleted Branch

```bash
 See recent commits
git reflog

 Recreate branch
git checkout -b branch-name <commit-hash>
```

---

 🆘 Debugging Git

```bash
 Check Git configuration
git config --list

 Verify remote setup
git remote -v

 Check branches and commits
git log --oneline --all --graph

 Find a lost commit
git reflog

 Get help on any command
git help commit
```

---

 📊 Git Workflow Visualization

```
┌─────────────────────────────────────────────────┐
│         Working Directory                        │
│      (your files on disk)                        │
│                                                  │
│  edited_file.py (modified)                      │
│  new_file.py (untracked)                        │
└─────────────────────────────────────────────────┘
              ↓ git add .
┌─────────────────────────────────────────────────┐
│         Staging Area                             │
│      (files to be committed)                     │
│                                                  │
│  edited_file.py                                 │
│  new_file.py                                    │
└─────────────────────────────────────────────────┘
         ↓ git commit -m "message"
┌─────────────────────────────────────────────────┐
│       Local Repository                           │
│      (.git folder history)                       │
│                                                  │
│  commit abc123 - added feature X                │
│  commit def456 - fixed bug Y                    │
│  commit ghi789 - initial commit                 │
└─────────────────────────────────────────────────┘
         ↓ git push
┌─────────────────────────────────────────────────┐
│      Remote Repository                           │
│      (GitHub/GitLab/etc)                        │
│                                                  │
│  Same commit history as local                   │
└─────────────────────────────────────────────────┘
```

---

 ✅ Checklist: You Should Know

- [ ] What the three stages of Git are
- [ ] How to create and clone repositories
- [ ] Basic commands: status, add, commit, log
- [ ] How to create and merge branches
- [ ] How to push and pull from remote
- [ ] Best practices for commit messages
- [ ] How to handle common Git problems

---

 📚 Next Steps

1. Practice these commands in exercises
2. Get comfortable with Git workflow
3. Learn GitHub next
4. Be prepared to use Git daily for rest of program

---

 🔗 Additional Resources

- [Pro Git Book (Free)](https://git-scm.com/book/en/v2)
- [Git Cheatsheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Visualizing Git](https://git-scm.com/book/en/v2/Git-Branching-Branch-Management)
- [Understand Git Conceptually](https://www.sbf5.com/~cchambers/documentation/git.shtml)
