 Exercise 2: Git Workflow Practice

 🎯 Objective
Practice complete Git workflow including branching, merging, and conflict resolution.

---

 📋 Part 1: Initialize Repository

 Task 1.1: Create Local Repository
```bash
 Create project directory
mkdir git-practice-project
cd git-practice-project

 Initialize Git repository
git init

 Check status
git status

 Configure for this repo (optional)
git config user.name "Your Name"
git config user.email "your.email@example.com"
```

 Task 1.2: First Commit
```bash
 Create initial README
cat > README.md << 'EOF'
 Git Practice Project

This repository is for practicing Git workflows.

 Contents
- Python scripts for learning Git
- Branching exercises
- Merge conflict resolution
EOF

 Check status
git status

 Stage and commit
git add README.md
git commit -m "Initial commit: Add README.md"

 View history
git log --oneline
```

---

 📋 Part 2: Branching Practice

 Task 2.1: Create Feature Branch
```bash
 Create and switch to new branch
git checkout -b feature/add-python-script

 Verify branch
git branch
```

 Task 2.2: Add Python Script
Create `hello_git.py`:
```python
!/usr/bin/env python3
"""
Simple script to practice Git workflows.
"""

def main():
    print("Hello, Git!")
    print("Learning version control is fun.")
    
     TODO: Add more functionality
    pass

if __name__ == "__main__":
    main()
```

```bash
 Commit on feature branch
git add hello_git.py
git commit -m "feat: Add initial Python script"

 Add another commit
cat >> hello_git.py << 'EOF'

def greet_user(name):
    """Greet a specific user."""
    return f"Hello, {name}! Welcome to Git practice."
EOF

git add hello_git.py
git commit -m "feat: Add user greeting function"
```

 Task 2.3: View Branch History
```bash
 View all commits
git log --oneline --graph --all

 Compare branches
git diff main feature/add-python-script
```

---

 📋 Part 3: Merging

 Task 3.1: Switch to Main and Merge
```bash
 Switch to main branch
git checkout main

 Verify we're on main
git branch

 Merge feature branch
git merge feature/add-python-script

 View updated history
git log --oneline --graph
```

 Task 3.2: Create and Merge Another Branch
```bash
 Create documentation branch
git checkout -b docs/update-readme

 Update README.md
cat >> README.md << 'EOF'

 Git Commands Learned
- git init: Initialize repository
- git add: Stage changes
- git commit: Save changes
- git branch: Manage branches
- git checkout: Switch branches
- git merge: Combine branches
EOF

git add README.md
git commit -m "docs: Update README with Git commands"

 Switch back and merge
git checkout main
git merge docs/update-readme

 Delete merged branches
git branch -d feature/add-python-script
git branch -d docs/update-readme
```

---

 📋 Part 4: Merge Conflict Resolution

 Task 4.1: Create Conflicting Branches
```bash
 Create branch 1
git checkout -b feature/add-math

 Edit hello_git.py
cat >> hello_git.py << 'EOF'

def add_numbers(a, b):
    """Add two numbers."""
    return a + b
EOF

git add hello_git.py
git commit -m "feat: Add math functionality (addition)"

 Switch to main and create branch 2
git checkout main
git checkout -b feature/add-strings

 Edit same file differently
cat >> hello_git.py << 'EOF'

def reverse_string(s):
    """Reverse a string."""
    return s[::-1]
EOF

git add hello_git.py
git commit -m "feat: Add string functionality (reverse)"
```

 Task 4.2: Create Merge Conflict
```bash
 Switch to main
git checkout main

 Merge first branch (will succeed)
git merge feature/add-math

 Try to merge second branch (will conflict)
git merge feature/add-strings

 You should see a CONFLICT message
```

 Task 4.3: Resolve the Conflict
```bash
 Check status
git status

 View conflicted file
cat hello_git.py
 Look for <<<<<<< HEAD markers
```

Edit `hello_git.py` to keep both changes:
```python
def main():
    print("Hello, Git!")
    print("Learning version control is fun.")
    
     TODO: Add more functionality
    pass

def greet_user(name):
    """Greet a specific user."""
    return f"Hello, {name}! Welcome to Git practice."

 Math functions
def add_numbers(a, b):
    """Add two numbers."""
    return a + b

 String functions
def reverse_string(s):
    """Reverse a string."""
    return s[::-1]

if __name__ == "__main__":
    main()
```

```bash
 Mark as resolved
git add hello_git.py
git commit -m "resolve: Merge math and string features"

 Verify resolution
git log --oneline --graph
```

---

 📋 Part 5: .gitignore Practice

 Task 5.1: Create .gitignore
```bash
 Create .gitignore
cat > .gitignore << 'EOF'
 Python
__pycache__/
.py[cod]
$py.class
.so
.Python

 Virtual Environment
venv/
env/
ENV/

 IDE
.vscode/
.idea/
.swp

 OS
.DS_Store
Thumbs.db

 Exercise specific
verification_report.txt
exercise_plot.png
EOF

git add .gitignore
git commit -m "chore: Add .gitignore for Python project"
```

 Task 5.2: Test .gitignore
```bash
 Create files that should be ignored
mkdir __pycache__
touch __pycache__/test.cpython-39.pyc
touch temp.pyc
touch .DS_Store

 Check status - ignored files should not appear
git status

 Clean up test files
rm -rf __pycache__ temp.pyc .DS_Store
```

---

 📋 Part 6: Complete Workflow Documentation

 Task 6.1: Create Workflow Documentation
Create `GIT_WORKFLOW.md`:
```markdown
 Git Workflow Documentation

 Project: Git Practice

 Branches Created
1. `main` - Main development branch
2. `feature/add-python-script` - Added hello_git.py
3. `docs/update-readme` - Updated documentation
4. `feature/add-math` - Added math functions
5. `feature/add-strings` - Added string functions

 Merge History
- Merged `feature/add-python-script` into main
- Merged `docs/update-readme` into main
- Merged `feature/add-math` into main
- Merged `feature/add-strings` into main (with conflict resolution)

 Conflict Resolution
Resolved conflict in hello_git.py by keeping both:
- Math functions from feature/add-math
- String functions from feature/add-strings

 Total Commits
[Number of commits]
```

```bash
git add GIT_WORKFLOW.md
git commit -m "docs: Add Git workflow documentation"
```

---

 ✅ Verification Commands

```bash
 View complete history
git log --oneline --graph --all

 Count commits
git rev-list --count HEAD

 List all branches (including deleted)
git reflog

 Check file contents
cat hello_git.py
cat README.md

 Verify .gitignore works
git check-ignore -v __pycache__
```

---

 🎓 Success Criteria

- [ ] Repository initialized with Git
- [ ] Minimum 10 meaningful commits
- [ ] 3+ branches created and merged
- [ ] At least 1 merge conflict created and resolved
- [ ] .gitignore properly configured
- [ ] All changes documented

---

 📊 Submission

Commit all files to your repository:
1. README.md
2. hello_git.py
3. .gitignore
4. GIT_WORKFLOW.md
5. Any other files created

Push to GitHub:
```bash
git remote add origin <your-github-url>
git push -u origin main
```

---

Previous: [Exercise 1: Environment Setup ←](./01_environment_setup.md) | Next: [Exercise 3: README Challenge →](./03_readme_challenge.md)
