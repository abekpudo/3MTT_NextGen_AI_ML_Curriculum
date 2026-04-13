 Git Fundamentals: Mastering Modern Version Control

 1. Introduction
Git is the industry-standard version control system for software development, data science, and DevOps. This lecture will take you from the basics to advanced workflows, with hands-on examples and best practices used by top engineering teams.

 2. Why Learn Git?
- Collaboration: Enables teams to work on the same codebase without conflict.
- History: Tracks every change, making it easy to review, revert, and audit code.
- Experimentation: Branching allows for safe experimentation and parallel development.
- Professionalism: Required for open source, internships, and nearly all tech jobs.

 3. Core Concepts

 3.1. Repositories
- Local Repository: Your working directory, staging area (index), and local commit history.
- Remote Repository: Hosted on platforms like GitHub, GitLab, or Bitbucket.
- Cloning: `git clone <repo-url>` creates a local copy of a remote repo.

 3.2. The Commit Workflow
1. Edit files in your working directory.
2. Stage changes: `git add <file>`
3. Commit: `git commit -m "Describe your change"`
4. Push to remote: `git push origin <branch>`

 3.3. Branching and Merging
- Branch: A parallel line of development. Create with `git branch <name>` or `git checkout -b <name>`
- Merge: Combine changes from one branch into another. `git merge <branch>`
- Rebase: Reapply commits on top of another base tip. `git rebase <branch>`
- Best Practice: Use feature branches for new work; keep `main` stable.

 3.4. Inspecting History
- Log: `git log` shows commit history.
- Diff: `git diff` shows changes between commits or branches.
- Blame: `git blame <file>` shows who last modified each line.

 3.5. Undoing Changes
- Reset: Move HEAD and optionally working directory. `git reset --hard <commit>`
- Checkout: Restore files or switch branches. `git checkout <branch>` or `git checkout <file>`
- Revert: Create a new commit that undoes a previous one. `git revert <commit>`
- Reflog: View all recent HEAD movements. `git reflog`

 3.6. Remote Collaboration
- Fetch: Download new data from remote. `git fetch`
- Pull: Fetch and merge. `git pull`
- Push: Upload local commits. `git push`
- Forks and Upstream: Contribute to projects you don’t own by forking and submitting pull requests.

 4. Hands-On Examples

 4.1. Creating a New Repository
```sh
git init myproject
cd myproject
echo " My Project" > README.md
git add README.md
git commit -m "Initial commit"
```

 4.2. Typical Feature Branch Workflow
```sh
git checkout -b feature/login
 Make changes
git add .
git commit -m "Add login feature"
git push origin feature/login
 Open a pull request on GitHub
```

 4.3. Resolving Merge Conflicts
1. Try to merge: `git merge feature/login`
2. If there’s a conflict, Git will mark the files.
3. Edit the files to resolve the conflict.
4. `git add <file>` and `git commit` to complete the merge.

 4.4. Undoing a Mistake
```sh
git log  Find the commit hash
git revert <commit-hash>  Safely undo a commit
```

 5. Best Practices
- Write clear, descriptive commit messages.
- Keep commits small and focused.
- Use branches for all new work.
- Pull and rebase often to stay up to date.
- Never commit secrets or large data files. Use `.gitignore`.
- Review code before merging.

 6. Advanced Topics
- Tagging: Mark releases with `git tag v1.0.0`
- Cherry-pick: Apply a commit from one branch to another: `git cherry-pick <commit>`
- Stashing: Save work-in-progress: `git stash`, `git stash pop`
- Hooks: Automate checks with pre-commit hooks

 7. Troubleshooting
- Detached HEAD: Happens when you checkout a commit instead of a branch. Create a new branch to save your work.
- Merge Conflicts: Use `git status` and a merge tool (e.g., VS Code, Meld) to resolve.
- Accidental Commits: Use `git reset` or `git revert` as appropriate.

 8. Resources
- [Pro Git Book](https://git-scm.com/book/en/v2)
- [GitHub Flow Guide](https://guides.github.com/introduction/flow/)
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)
- [Oh My Git! Interactive Game](https://ohmygit.org/)
- [Git Cheatsheet](https://education.github.com/git-cheat-sheet-education.pdf)

---
