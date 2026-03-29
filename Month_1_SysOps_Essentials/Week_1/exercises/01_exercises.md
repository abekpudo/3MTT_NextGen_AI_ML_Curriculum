 Week 1 Exercises

 Exercise 1: Python Environment Setup Verification

Objective: Ensure your development environment is correctly set up.

 Tasks

1. Verify Python Installation
   ```bash
   python3 --version   Should be 3.9 or higher
   which python3       Should show path to Python
   python3 -c "import sys; print(sys.executable)"
   ```

2. Create Virtual Environment
   ```bash
   mkdir ~/my-ml-project
   cd ~/my-ml-project
   python3 -m venv venv
   source venv/bin/activate
   ```

3. Install Essential Packages
   ```bash
   pip install --upgrade pip
   pip install jupyter numpy pandas matplotlib
   pip freeze > requirements.txt
   ```

4. Verify Jupyter Works
   ```bash
   jupyter notebook
    Create a new notebook, run: print("Hello from Jupyter!")
   ```

Deliverable: Screenshot showing Jupyter running with a successful code cell

---

 Exercise 2: Git Basics Practice

Objective: Get comfortable with Git workflow.

 Tasks

1. Initialize a Git Repository
   ```bash
   cd ~/my-ml-project
   git init
   git config user.name "Your Name"
   git config user.email "your@email.com"
   ```

2. Create Files
   ```bash
   echo " My ML Learning" > README.md
   echo "print('Hello')" > hello.py
   ```

3. Practice Git Commands
   ```bash
    Check status
   git status
   
    Stage changes
   git add README.md
   git add hello.py
   
    Check status again
   git status
   
    Create commit
   git commit -m "Initial commit with hello.py and README"
   
    View history
   git log
   ```

4. Create a Branch
   ```bash
   git branch feature/add-more-files
   git checkout feature/add-more-files
   
    Create new file
   echo " Experiments" > experiments.md
   git add experiments.md
   git commit -m "Add experiments file"
   
    Switch back to main
   git checkout main
   
    Merge branch
   git merge feature/add-more-files
   ```

Deliverable: Screenshot of `git log` showing all commits

---

 Exercise 3: Understanding .gitignore

Objective: Learn what files should be excluded from Git.

 Tasks

1. Create a .gitignore file
   ```bash
   cat > .gitignore << 'EOF'
    Python
   __pycache__/
   *.py[cod]
   venv/
   .env
   
    Jupyter
   .ipynb_checkpoints/
   
    OS
   .DS_Store
   
    Data
   *.csv
   data/
   EOF
   ```

2. Test .gitignore
   ```bash
    Create files that should be ignored
   mkdir __pycache__
   touch __pycache__/file.pyc
   touch data.csv
   
    Check git status - these shouldn't show up
   git status
   
    Add and commit .gitignore
   git add .gitignore
   git commit -m "Add .gitignore"
   ```

3. Verify Ignored Files
   ```bash
    These should NOT appear in 'git status'
   git status
   ```

Deliverable: Screenshot of `git status` showing .gitignore is working

---

 Exercise 4: Command Line Navigation

Objective: Get comfortable with terminal navigation (important for SysOps!)

 Tasks

```bash
 Create directory structure
mkdir -p ~/ml-course/month1/{week1,week2,week3,week4}
mkdir -p ~/ml-course/month1/week1/{exercises,assignments,notes}

 Navigate around
cd ~/ml-course/month1
pwd   Print working directory
ls    List files
tree  Tree view (install if needed: brew install tree)

 File operations
cd week1
touch exercise1.py exercise2.py
ls -la

 Back to parent
cd ..
cd ..
pwd   Should be ~/ml-course
```

Deliverable: Screenshot showing the directory structure you created

---

 Exercise 5: Documentation Practice

Objective: Write professional documentation.

 Tasks

1. Create a Professional README
   
   Create a `README.md` file:

   ```markdown
    My ML Learning Journey

   Welcome to my 3MTT NextGen AI & ML Curriculum repository!

    📚 About

   This repository contains my learning materials and projects from the 3MTT program.

    📂 Structure

   ```
   .
   ├── Month_1_SysOps/
   │   ├── Week_1/
   │   ├── Week_2/
   │   └── ...
   ├── Month_2_ML/
   └── ...
   ```

    🎯 Goals

   - Master machine learning fundamentals
   - Build production-ready systems
   - Create portfolio projects

    📝 Progress

   - [x] Week 1: Environment setup & Git
   - [ ] Week 2: Linux & Bash
   - [ ] Week 3: AWS Essentials
   - [ ] Week 4: Advanced Git Workflows

    🔗 Links

   - [3MTT Program](https://3mtt.org)
   - My GitHub: [Your Profile]
   - Email: your@email.com

    📄 License

   This repository is for learning purposes.
   ```

2. Commit Your README
   ```bash
   git add README.md
   git commit -m "Add comprehensive README documentation"
   git log --oneline
   ```

Deliverable: Screenshot of your README.md file in your editor

---

 Exercise 6: Git Workflow Simulation

Objective: Simulate a realistic development workflow.

 Tasks

1. Scenario: You're building a Python module with multiple features.

   ```bash
    Create main branch work
   git checkout main
   
    Create feature branch
   git checkout -b feature/user-module
   
    Simulate development
   cat > user.py << 'EOF'
   class User:
       def __init__(self, name, email):
           self.name = name
           self.email = email
       
       def display_info(self):
           return f"{self.name} ({self.email})"
   EOF
   
   git add user.py
   git commit -m "feat: Add User class"
   
    More work on feature
   cat >> user.py << 'EOF'

       def is_valid_email(self):
           return "@" in self.email
   EOF
   
   git add user.py
   git commit -m "feat: Add email validation method"
   
    Meanwhile, someone updates main
   git checkout main
   
    Create another feature
   git checkout -b feature/database
   cat > database.py << 'EOF'
   class Database:
       def __init__(self):
           self.users = []
   EOF
   
   git add database.py
   git commit -m "feat: Add Database class"
   git checkout main
   
    Merge first feature
   git merge feature/user-module -m "Merge user module"
   
    Merge second feature
   git merge feature/database -m "Merge database module"
   
    View history
   git log --oneline --graph --all
   ```

Deliverable: Screenshot of the git log showing all commits and merges

---

 Reflection Questions

After completing these exercises, answer these questions in a document:

1. What is the purpose of a virtual environment in Python?
2. What are the three stages of Git (working directory, staging area, repository)?
3. Why should you write good commit messages?
4. What files should typically be in .gitignore?
5. What's the difference between git clone and git fork?

Deliverable: A markdown file with your answers

---

 Additional Challenges (Optional)

If you want to go deeper:

1. Merge Conflicts: Create a scenario where you must resolve merge conflicts
2. Git History: Use `git log` with different options and understand the output
3. Undo Operations: Practice undoing commits using `git reset` and `git revert`
4. Aliasing: Create Git aliases for common commands

```bash
 Example aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.lg "log --graph --oneline --all"
```

---

 Success Criteria

You should be able to:
- [ ] Create and activate virtual environments
- [ ] Create Python projects with proper structure
- [ ] Initialize Git repositories
- [ ] Understand and use Git workflow (add, commit, push)
- [ ] Create and merge branches
- [ ] Write meaningful commit messages
- [ ] Navigate file system efficiently
- [ ] Write clear documentation

Next: Move on to the assignments!
