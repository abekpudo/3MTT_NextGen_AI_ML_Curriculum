Week 1 Cheatsheet: Git & GitHub Collaboration

Key Concepts
- Version control basics (init, add, commit, log)
- Branching strategies (feature, bugfix, hotfix)
- Merge vs. rebase
- Remote collaboration (origin, upstream)
- Pull requests and code review
- Issue tracking and project boards

Essential Commands
- `git init` — Initialize a new repository
- `git clone <url>` — Clone a remote repository
- `git status` — Show current changes
- `git add <file>` — Stage changes
- `git commit -m "message"` — Commit staged changes
- `git branch` — List branches
- `git checkout -b <branch>` — Create and switch to a new branch
- `git merge <branch>` — Merge a branch into current
- `git pull` — Fetch and merge from remote
- `git push` — Push changes to remote
- `git log --oneline --graph` — Visualize commit history
- `git diff` — Show changes between commits or working tree

GitHub Collaboration
- Fork a repo: Use the GitHub web interface
- Add a remote: `git remote add upstream <url>`
- Open a pull request: Use GitHub web or CLI
- Reference issues: `Fixes 123` in PR description
- Assign reviewers and use project boards

Best Practices
- Commit early, commit often, and write clear messages
- Use feature branches for each new task
- Keep pull requests small and focused
- Review code and respond to feedback
- Document your workflow in the README

Troubleshooting
- Undo last commit (keep changes): `git reset --soft HEAD~1`
- Discard local changes: `git checkout -- <file>`
- Resolve merge conflicts: Edit files, then `git add` and `git commit`

---
