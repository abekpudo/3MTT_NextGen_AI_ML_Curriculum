 Week 1: Foundations & Program Orientation

Duration: 5 days | Theme: Getting Started & Git Fundamentals | Difficulty: Beginner  
Welcome to 3MTT NextGen AI/ML Program!

---

 🎯 Learning Objectives

By the end of this week, you will:
- ✅ Understand the 3MTT program structure and success strategies
- ✅ Set up a professional Python development environment
- ✅ Master Git fundamentals (init, add, commit, branch, merge)
- ✅ Create and manage GitHub repositories professionally
- ✅ Establish a personal portfolio on GitHub
- ✅ Practice version control workflows used in industry

---

 📖 Detailed Daily Schedule

 Day 1: Program Introduction & Environment Setup
Lecture: [01_program_introduction.md](./lecture_notes/01_program_introduction.md)

Topics:
- 3MTT program overview and learning path
- Month-by-month curriculum roadmap
- Success strategies and time management
- Python 3.9+ installation (Windows/Mac/Linux)
- Virtual environments (venv vs conda)
- VS Code installation and configuration
- Essential VS Code extensions for Python

Hands-On:
```bash
 Install Python (verify version)
python --version   Should be 3.9+

 Create virtual environment
python -m venv 3mtt_env

 Activate environment
source 3mtt_env/bin/activate   Mac/Linux
3mtt_env\Scripts\activate        Windows

 Install essential packages
pip install jupyter pandas numpy matplotlib
```

Practice:
- [ ] Install Python and verify version
- [ ] Set up virtual environment
- [ ] Install VS Code with Python extensions
- [ ] Create first Jupyter notebook
- [ ] Run "Hello, 3MTT!" program

---

 Day 2: Python Environment & Package Management
Lecture: [02_environment_setup.md](./lecture_notes/02_environment_setup.md)

Topics:
- pip package manager (install, uninstall, freeze)
- requirements.txt creation and usage
- Conda environments (alternative to venv)
- Jupyter notebooks and JupyterLab
- IPython magic commands
- Python path and environment variables

Hands-On:
```bash
 Generate requirements.txt
pip freeze > requirements.txt

 Install from requirements
pip install -r requirements.txt

 Create Conda environment (alternative)
conda create -n 3mtt python=3.9
conda activate 3mtt

 Jupyter notebook operations
jupyter notebook   Start server
 Create new notebook → Write code → Run cells
```

Practice:
- [ ] Create and manage multiple virtual environments
- [ ] Generate requirements.txt for a project
- [ ] Practice conda commands (if using)
- [ ] Create Jupyter notebook with markdown cells
- [ ] Install and test pandas, numpy, matplotlib

---

 Day 3: Git Fundamentals - Part 1
Lecture: [03_git_basics.md](./lecture_notes/03_git_basics.md)

Topics:
- Version control concepts (why use Git?)
- Git installation and configuration
- `git init` - Initializing repositories
- `git add` - Staging changes
- `git commit` - Saving snapshots
- `git status` - Checking repository state
- `git log` - Viewing history

Hands-On:
```bash
 Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main

 Create first repository
mkdir my-first-project
cd my-first-project
git init

 Basic workflow
echo " My Project" > README.md
git add README.md
git commit -m "Initial commit: Add README"
git log --oneline
```

Practice:
- [ ] Configure Git with your identity
- [ ] Create 3 different repositories
- [ ] Practice add → commit workflow (10+ times)
- [ ] View commit history with different log formats
- [ ] Understand staging area with `git status`

---

 Day 4: Git Fundamentals - Part 2
Lecture: [04_git_workflows.md](./lecture_notes/04_git_workflows.md)

Topics:
- Branching concepts (`git branch`, `git checkout`)
- Creating and switching branches
- Merging branches (`git merge`)
- Handling merge conflicts
- `.gitignore` files
- Git best practices

Hands-On:
```bash
 Branching workflow
git branch feature-login     Create branch
git checkout feature-login   Switch to branch
 OR: git checkout -b feature-login

 Make changes, commit
echo "def login(): pass" > auth.py
git add auth.py
git commit -m "Add login function"

 Merge back to main
git checkout main
git merge feature-login

 Create .gitignore
cat > .gitignore << EOF
__pycache__/
.pyc
.env
.DS_Store
EOF
git add .gitignore
git commit -m "Add .gitignore"
```

Practice:
- [ ] Create feature branches for different features
- [ ] Practice merging (fast-forward and non-fast-forward)
- [ ] Create intentional merge conflict and resolve it
- [ ] Build comprehensive .gitignore for Python projects
- [ ] Document branching strategy

---

 Day 5: GitHub & Remote Repositories
Lecture: [05_github_setup.md](./lecture_notes/05_github_setup.md)

Topics:
- GitHub account creation and setup
- SSH key authentication
- `git remote` - Managing remotes
- `git push` - Uploading to GitHub
- `git pull` - Downloading from GitHub
- `git clone` - Copying repositories
- Creating professional README.md files
- GitHub Pages (portfolio hosting)

Hands-On:
```bash
 Set up SSH keys
ssh-keygen -t ed25519 -C "your.email@example.com"
cat ~/.ssh/id_ed25519.pub   Add to GitHub

 Connect local repo to GitHub
git remote add origin git@github.com:username/repo.git
git branch -M main
git push -u origin main

 Clone existing repo
git clone git@github.com:username/another-repo.git

 Pull latest changes
git pull origin main
```

Practice:
- [ ] Create GitHub account with professional username
- [ ] Set up SSH authentication
- [ ] Push local repository to GitHub
- [ ] Create professional README with badges
- [ ] Fork an open-source project
- [ ] Enable GitHub Pages for portfolio

---

 📚 Essential Resources

 Required Reading
1. Enhanced Lecture Notes:
   - [01_program_introduction.md](./lecture_notes/01_program_introduction.md) - Program overview
   - [02_environment_setup.md](./lecture_notes/02_environment_setup.md) - Python environment setup
   - [03_git_basics.md](./lecture_notes/03_git_basics.md) - Git fundamentals
   - [04_git_workflows.md](./lecture_notes/04_git_workflows.md) - Branching and merging
   - [05_github_setup.md](./lecture_notes/05_github_setup.md) - GitHub integration

2. External Resources:
   - [Pro Git Book](https://git-scm.com/book/en/v2) - Chapters 1-3
   - [GitHub Hello World](https://guides.github.com/activities/hello-world/)
   - [VS Code Python Tutorial](https://code.visualstudio.com/docs/python/python-tutorial)
   - [Markdown Guide](https://www.markdownguide.org/)

 Tools & Software
```bash
 Required Software
- Python 3.9 or higher (3.11 recommended)
- Git 2.30 or higher
- VS Code (latest)
- GitHub account

 VS Code Extensions (Install These)
- Python (Microsoft)
- Pylance
- Jupyter
- GitLens
- Markdown All in One
- Python Docstring Generator

 Optional but Recommended
- GitHub Desktop (GUI for Git)
- Anaconda (if using conda)
- Windows Terminal / iTerm2 / Guake
```

---

 💼 Weekly Deliverables

 Required Submissions

1. Development Environment Setup (20%)
   - Screenshot of Python version check
   - Screenshot of VS Code with Python extension
   - Virtual environment creation verification
   - List of installed packages (`pip list` output)

2. Git Configuration (20%)
   - Git configuration (`git config --list` output)
   - Screenshot of SSH key added to GitHub
   - Commit history showing multiple commits (`git log --oneline`)

3. Repository with Professional README (30%)
   - Public GitHub repository
   - Comprehensive README.md including:
     - Project description
     - Installation instructions
     - Usage examples
     - Screenshots (if applicable)
     - License information
   - Proper .gitignore file
   - Minimum 5 meaningful commits

4. Branching & Merging Exercise (20%)
   - Repository with feature branches
   - Evidence of merge (screenshots or log)
   - Documentation of conflict resolution (if any)

5. Portfolio Repository (10%)
   - Create `username.github.io` repository
   - Basic portfolio page (index.html or README)
   - Link to professional social profiles

---

 🎯 Exercises

 Exercise 1: Environment Verification
Create a Python script that:
- Prints Python version
- Lists installed packages and versions
- Verifies Jupyter notebook works
- Creates a simple matplotlib plot
- Saves output to verification_report.txt

 Exercise 2: Git Workflow Practice
Create a repository that demonstrates:
- Initial commit with README
- Feature branch for adding Python script
- Multiple commits with descriptive messages
- Merge back to main
- Proper .gitignore for Python

 Exercise 3: Collaborative GitHub
- Fork the course repository
- Create a branch for adding your profile
- Add a markdown file with your introduction
- Submit a pull request (if enabled)
- Practice reviewing code

 Exercise 4: README Challenge
Create the most professional README.md possible:
- Use badges (build status, license, Python version)
- Include table of contents
- Add code examples with syntax highlighting
- Include screenshots or diagrams
- Follow Markdown best practices

---

 📋 Assignment: Personal Portfolio Setup

 Part A: Environment Setup (30 points)
- [ ] Python 3.9+ installed (5 pts)
- [ ] Virtual environment created (5 pts)
- [ ] VS Code with extensions (5 pts)
- [ ] Jupyter notebook working (5 pts)
- [ ] Git installed and configured (10 pts)

 Part B: GitHub Portfolio (40 points)
- [ ] Account created with professional name (5 pts)
- [ ] SSH keys configured (10 pts)
- [ ] Portfolio repository created (10 pts)
- [ ] Professional README.md (10 pts)
- [ ] GitHub Pages enabled (5 pts)

 Part C: Version Control (30 points)
- [ ] Repository with 10+ commits (10 pts)
- [ ] Feature branch created and merged (10 pts)
- [ ] Proper .gitignore (5 pts)
- [ ] Clean commit history (5 pts)

---

 🔗 Quick Navigation

| Resource | Description |
|----------|-------------|
| [� Lecture Notes](./lecture_notes/) | Detailed Git and setup concepts |
| [🎯 Exercises](./exercises/) | Practice problems for Git and setup |
| [📋 Assignments](./assignments/) | Graded deliverables |
| [📄 Cheatsheets](./cheatsheets/) | Git commands quick reference |

---

 ⚡ Daily Study Schedule

| Time | Activity |
|------|----------|
| 30 min | Watch/read lecture materials |
| 45 min | Hands-on practice (terminal/IDE) |
| 15 min | Complete exercises |
| 15 min | Document progress and commit |

Total Daily Time: ~1.5-2 hours

---

 💡 Success Tips for Week 1

1. Don't Rush Setup: A properly configured environment saves hours later
2. Practice Git Daily: Make commits even for small changes
3. Use Command Line: GUI tools are convenient, but learn CLI first
4. Read Error Messages: Git errors usually tell you exactly what to do
5. Commit Messages Matter: Write descriptive messages for future you
6. Backup Your Work: Push to GitHub frequently
7. Ask Questions: If stuck, check Stack Overflow or ask peers
8. Explore: Try breaking things and fixing them (in a test repo!)

---

 🆘 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| `git: command not found` | Reinstall Git and restart terminal |
| `Permission denied (publickey)` | Check SSH key setup on GitHub |
| `Merge conflict` | Open files, find `<<<<<`, resolve, commit |
| `Module not found` | Ensure virtual environment is activated |
| `jupyter: command not found` | Install with `pip install jupyter` |
| `git push rejected` | Pull first, resolve conflicts, then push |

---

 🎓 Week 1 Completion Checklist

- [ ] Python installed and verified
- [ ] Virtual environment mastered
- [ ] VS Code configured
- [ ] Git installed and configured
- [ ] 5+ local commits practiced
- [ ] GitHub account created
- [ ] SSH keys working
- [ ] First repository pushed
- [ ] Professional README written
- [ ] Portfolio repository created
- [ ] All exercises completed
- [ ] Assignment submitted

---

Next: [Week 2: Linux & Bash →](../Week_2/README.md)
