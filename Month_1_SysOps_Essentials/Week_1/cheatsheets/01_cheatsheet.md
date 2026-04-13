 Week 1 Cheatsheet: Git & Environment Setup

 🚀 Quick Reference Guide

---

 🐍 Python Environment Setup

 Installation Verification
```bash
 Check Python version (should be 3.9+)
python --version
python3 --version   On some systems

 Check pip version
pip --version

 Check where Python is installed
which python        Mac/Linux
where python        Windows
```

 Virtual Environment Commands
```bash
 Create virtual environment
python -m venv myenv
python3 -m venv myenv   If python3 is separate

 Activate environment
source myenv/bin/activate       Mac/Linux
myenv\Scripts\activate          Windows CMD
myenv\Scripts\Activate.ps1     Windows PowerShell

 Deactivate environment
deactivate

 Delete environment (just delete folder)
rm -rf myenv/                   Mac/Linux
rmdir /s myenv                  Windows
```

 Package Management
```bash
 Install packages
pip install package_name
pip install package_name==1.2.3   Specific version

 Install multiple packages
pip install numpy pandas matplotlib

 Install from requirements.txt
pip install -r requirements.txt

 Generate requirements.txt
pip freeze > requirements.txt

 List installed packages
pip list

 Show package info
pip show package_name

 Uninstall package
pip uninstall package_name

 Upgrade package
pip install --upgrade package_name
```

 requirements.txt Template
```txt
 Core data science
numpy>=1.21.0
pandas>=1.3.0
matplotlib>=3.4.0
seaborn>=0.11.0

 Jupyter
jupyter>=1.0.0
ipython>=7.0.0

 Utilities
requests>=2.26.0
python-dotenv>=0.19.0
```

---

 🔧 Git Essentials

 Configuration (One-Time Setup)
```bash
 Set your identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

 Set default branch name
git config --global init.defaultBranch main

 Set default editor (optional)
git config --global core.editor "code --wait"   VS Code
git config --global core.editor "vim"           Vim

 View all configurations
git config --list
git config --global --list

 Check specific config
git config user.name
```

 Repository Initialization
```bash
 Create new repository
git init

 Check repository status
git status

 Add files to staging area
git add filename.txt           Single file
git add .                    All files
git add .py                 All Python files
git add -A                   All changes (including deletes)

 Remove from staging
git restore --staged filename.txt

 Commit changes
git commit -m "Descriptive commit message"
git commit -m "Title" -m "Detailed description"

 View commit history
git log
git log --oneline               Compact view
git log --graph --oneline       Graph view
git log --stat                  With file stats
git log -p                      With diffs
git log --oneline -10           Last 10 commits
```

 Branching Commands
```bash
 List branches
git branch                       Local branches
git branch -a                    All branches (local + remote)
git branch -r                    Remote branches

 Create branch
git branch new-branch

 Switch to branch
git checkout branch-name
git switch branch-name          Newer syntax

 Create and switch (combined)
git checkout -b new-branch
git switch -c new-branch        Newer syntax

 Rename branch
git branch -m old-name new-name

 Delete branch
git branch -d branch-name       Safe delete (merged)
git branch -D branch-name       Force delete
```

 Merging
```bash
 Merge branch into current
git merge branch-name

 Common merge strategies
--ff-only        Fast-forward only
--no-ff          Always create merge commit
--squash         Squash all commits

 Abort merge (if conflicts)
git merge --abort

 Continue after resolving conflicts
git add .
git commit -m "Merge branch-name"
```

 Remote Repositories
```bash
 Add remote
git remote add origin <url>
git remote add upstream <url>

 View remotes
git remote -v

 Rename remote
git remote rename old new

 Remove remote
git remote remove origin

 Push to remote
git push origin main
git push -u origin main        Set upstream
git push origin branch-name     Push specific branch

 Pull from remote
git pull origin main
git pull origin branch-name

 Fetch (download without merging)
git fetch origin
git fetch --all

 Clone repository
git clone <url>
git clone <url> folder-name     Clone to specific folder
git clone --depth 1 <url>        Shallow clone (no history)
```

 Undoing Changes
```bash
 Unstage files (keep changes)
git restore --staged filename

 Discard uncommitted changes
git restore filename

 Amend last commit
git commit --amend -m "New message"
git commit --amend --no-edit    Keep message, add staged changes

 Reset to previous commit
git reset HEAD~1                Soft reset (keep changes)
git reset --hard HEAD~1         Hard reset (discard changes)

 View reflog (recovery)
git reflog
```

---

 🔗 GitHub Workflow

 SSH Key Setup
```bash
 Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

 Copy public key to clipboard
 Mac:
pbcopy < ~/.ssh/id_ed25519.pub

 Linux:
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard

 Windows (PowerShell):
Get-Content ~/.ssh/id_ed25519.pub | Set-Clipboard

 Add to GitHub: Settings → SSH and GPG keys → New SSH key

 Test connection
ssh -T git@github.com
```

 Complete GitHub Push Workflow
```bash
 1. Create repository on GitHub (don't initialize)

 2. In local project
git init
git add .
git commit -m "Initial commit"

 3. Connect to GitHub (SSH)
git remote add origin git@github.com:username/repo.git

 4. Push to GitHub
git branch -M main
git push -u origin main

 5. Future pushes (after -u flag)
git push   Simplified
```

 Fork and Pull Request Workflow
```bash
 1. Fork repository on GitHub (web interface)

 2. Clone your fork
git clone git@github.com:your-username/forked-repo.git

 3. Add upstream remote
git remote add upstream git@github.com:original/repo.git

 4. Create feature branch
git checkout -b feature-name

 5. Make changes and commit
git add .
git commit -m "Add feature"

 6. Push to your fork
git push origin feature-name

 7. Create Pull Request on GitHub (web interface)

 8. Sync with upstream (before new work)
git fetch upstream
git checkout main
git merge upstream/main
```

---

 📝 .gitignore Template for Python

```gitignore
 Byte-compiled / optimized / DLL files
__pycache__/
.py[cod]
$py.class
.so
.Python

 Distribution / packaging
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
.egg-info/
.installed.cfg
.egg

 PyInstaller
.manifest
.spec

 Installer logs
pip-log.txt
pip-delete-this-directory.txt

 Unit test / coverage reports
htmlcov/
.tox/
.nox/
.coverage
.coverage.
.cache
nosetests.xml
coverage.xml
.cover
.hypothesis/
.pytest_cache/

 Jupyter Notebook
.ipynb_checkpoints

 pyenv
.python-version

 Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

 IDE
.vscode/
.idea/
.swp
.swo
~

 OS
.DS_Store
Thumbs.db

 Project specific
data/
models/.pkl
models/.joblib
logs/
.log
secrets.json
credentials.txt
```

---

 📊 Git Best Practices

 Commit Message Format
```
type(scope): subject

body (optional)

footer (optional)
```

Types:
- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation only
- `style:` Formatting, missing semi colons, etc
- `refactor:` Code change that neither fixes a bug nor adds a feature
- `test:` Adding tests
- `chore:` Maintenance tasks

Examples:
```
feat(auth): add login functionality

Implement user authentication with JWT tokens.
Includes password hashing and session management.

Closes 123

fix(api): resolve null pointer in user endpoint

Handle edge case where user has no profile data.

docs(readme): update installation instructions

Add detailed steps for Windows users.
```

 Branch Naming Conventions
```
feature/description     New features
bugfix/description      Bug fixes
hotfix/description      Critical fixes
refactor/description  Code refactoring
docs/description        Documentation
test/description        Tests
chore/description       Maintenance

 Examples
feature/user-authentication
bugfix/login-validation
hotfix/security-patch
```

---

 🛠️ VS Code Shortcuts

 Essential Shortcuts
```
Cmd/Ctrl + Shift + P      Command palette
Cmd/Ctrl + P              Quick file open
Cmd/Ctrl + Shift + F      Search across files
Cmd/Ctrl + `              Toggle terminal
Cmd/Ctrl + /              Toggle line comment
Cmd/Ctrl + D              Select next occurrence
Cmd/Ctrl + Shift + L      Select all occurrences
Cmd/Ctrl + Shift + K      Delete line
Alt + Up/Down             Move line up/down
Option/Alt + Click        Multiple cursors
F5                        Start debugging
Cmd/Ctrl + Shift + E      Explorer sidebar
Cmd/Ctrl + Shift + G      Source control sidebar
Cmd/Ctrl + Shift + X      Extensions sidebar
```

---

 🐛 Common Issues & Solutions

| Error | Solution |
|-------|----------|
| `git: command not found` | Install Git, restart terminal |
| `fatal: not a git repository` | Run `git init` or navigate to repo |
| `Permission denied (publickey)` | Check SSH key, run `ssh -T git@github.com` |
| `fatal: refusing to merge unrelated histories` | Use `git pull origin main --allow-unrelated-histories` |
| `error: Your local changes...` | Stash changes: `git stash`, or commit first |
| `CONFLICT (content): Merge conflict` | Open file, resolve markers, add, commit |
| `failed to push some refs` | Pull first: `git pull origin main` |
| `ModuleNotFoundError` | Activate venv: `source venv/bin/activate` |
| `pip not found` | Use `python -m pip install package` |

---

 🎯 Quick Reference Commands

 Daily Git Workflow
```bash
 Start of day
git pull origin main

 During work
 ... make changes ...
git status                     Check what's changed
git add .                      Stage changes
git commit -m "message"        Commit
git push origin branch         Push

 End of day
git status                     Verify all committed
git log --oneline -5           Review recent commits
```

 Emergency Recovery
```bash
 Undo last commit (keep changes)
git reset --soft HEAD~1

 Undo last commit (discard changes)
git reset --hard HEAD~1

 Recover deleted commit
git reflog                     Find commit hash
git checkout -b recovery-branch <hash>

 Clean untracked files
git clean -fd                  Force, directories
```

---

Previous: [Getting Started](../../GETTING_STARTED.md) | Next: [Week 2 Cheatsheet →](../Week_2/cheatsheets/01_cheatsheet.md)
