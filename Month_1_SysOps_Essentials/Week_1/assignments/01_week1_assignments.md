 Week 1 Assignments

Submission: Via GitHub by end of week (create GitHub link in submission form)

---

 Assignment 1: Professional GitHub Repository Setup

Due: By end of Week 1 | Weight: 20%

 Objective
Create a professional GitHub repository that will serve as your portfolio and learning journal for the entire 3MTT program.

 Requirements

1. Create GitHub Repository
   - Name: `3MTT-NextGen-AI-ML` (or `3mtt-ml-learning`)
   - Description: "My journey through the 3MTT NextGen AI & Machine Learning Curriculum"
   - Make it Public (for portfolio)
   - Initialize with README
   - Add Python `.gitignore`
   - Choose MIT License

2. Write Professional README.md
   
   Your README must include:
   - [ ] Project title and brief description
   - [ ] Your name and contact info
   - [ ] Table of contents
   - [ ] About the 3MTT Program (1-2 paragraphs)
   - [ ] Your learning goals (4-5 specific goals)
   - [ ] Repository structure (folder breakdown)
   - [ ] Weekly progress tracking (checkboxes for each week)
   - [ ] Tech stack you'll be using
   - [ ] How to use this repository
   - [ ] Contributing guidelines
   - [ ] License section
   - [ ] Links to your other profiles (LinkedIn, Twitter, etc.)

   Example Structure:
   ```
    3MTT NextGen AI & ML Curriculum
   
    About Me
   - Name: [Your Name]
   - Location: [City, Country]
   - Background: [Brief background]
   - LinkedIn: [Your LinkedIn]
   
    Program Overview
   [1-2 paragraphs about 3MTT program]
   
    Learning Goals
   1. Master ML fundamentals and algorithms
   2. Gain expertise in NLP and Computer Vision
   3. Learn to build production ML systems
   4. Create portfolio projects
   5. Be job-ready as ML Engineer
   
    Repository Structure
   ```
   3mtt-learning/
   ├── Month_1_SysOps/
   │   ├── Week_1/
   │   │   ├── exercises/
   │   │   ├── assignments/
   │   │   └── notes/
   │   └── ...
   ├── Month_2_ML/
   ├── ...
   └── README.md
   ```
   
    Progress Tracking
   - [ ] Month 1: SysOps Essentials
   - [ ] Month 2: ML Techniques
   - [ ] Month 3: NLP
   - [ ] Month 4: Computer Vision
   - [ ] Month 5: Big Data & Deployment
   - [ ] Capstone Project
   ```

3. Clone Locally and Make Initial Commits
   - Clone repository to your machine
   - Create folder structure for all 5 months
   - Create a `LEARNING_LOG.md` file to track your journey
   - Make at least 3 meaningful commits

4. Set Up Git Configuration
   - Configure global Git user
   - Generate SSH keys (if you haven't)
   - Test SSH connection to GitHub

 Evaluation Criteria

- Professional Quality (40%): README is well-written, organized, and clear
- Completeness (40%): All required sections included
- Git Usage (20%): Proper commits with meaningful messages

 Example Submission

Share your GitHub URL in the submission form.

---

 Assignment 2: Environment Setup Verification Script

Due: By mid-week | Weight: 15%

 Objective
Create a Python script that verifies your development environment is properly set up.

 Requirements

Create a file called `verify_environment.py` that checks:

```python
import sys
import os
import subprocess

def check_python_version():
    """Check Python version is 3.9+"""
    version = sys.version_info
    print(f"✓ Python: {version.major}.{version.minor}.{version.micro}")
    return version >= (3, 9)

def check_packages():
    """Check essential packages are installed"""
    packages = [
        'numpy',
        'pandas',
        'jupyter',
        'matplotlib',
        'scikit-learn'
    ]
    
    missing = []
    for package in packages:
        try:
            __import__(package)
            print(f"✓ {package}")
        except ImportError:
            print(f"✗ {package} - NOT INSTALLED")
            missing.append(package)
    
    return len(missing) == 0

def check_git():
    """Check Git is installed and configured"""
     Your code here
    pass

def check_jupyter():
    """Check Jupyter can start"""
     Your code here
    pass

def main():
    print("=" * 50)
    print("Environment Verification Script")
    print("=" * 50)
    
    checks = {
        "Python Version": check_python_version(),
        "Required Packages": check_packages(),
        "Git Configuration": check_git(),
        "Jupyter Installation": check_jupyter(),
    }
    
    print("\n" + "=" * 50)
    all_passed = all(checks.values())
    
    if all_passed:
        print("✓ All checks passed! Your environment is ready.")
    else:
        print("✗ Some checks failed. See above for details.")
        return 1
    
    return 0

if __name__ == "__main__":
    sys.exit(main())
```

 Your Tasks

1. Complete the script - Fill in missing functions
2. Add more checks:
   - Git is installed
   - Git user is configured
   - SSH key exists
   - Virtual environment is active
   - Current Python version matches requirements

3. Commit and Push
   ```bash
   git add verify_environment.py
   git commit -m "Add environment verification script"
   git push origin main
   ```

 Expected Output

```
==================================================
Environment Verification Script
==================================================
✓ Python: 3.9.12
✓ numpy
✓ pandas
✓ jupyter
✓ matplotlib
✓ scikit-learn
✓ Git is installed
✓ Git user configured: John Smith
✓ SSH key exists
✓ Virtual environment is active

==================================================
✓ All checks passed! Your environment is ready.
```

 Evaluation Criteria

- Functionality (50%): Script correctly checks all environments
- Code Quality (30%): Clean, well-commented code
- Completeness (20%): All checks implemented

---

 Assignment 3: Git Workflow Practice Project

Due: By end of week | Weight: 25%

 Objective
Demonstrate proficiency with Git workflow by simulating a realistic development scenario.

 Requirements

Scenario: You're building a simple calculator module with multiple team members working on different features.

1. Create Feature Branches
   
   Create at least 4 branches:
   ```bash
    Basic calculator branch
   git checkout -b feature/basic-operations
   
    Advanced operations branch
   git checkout -b feature/advanced-operations
   
    Documentation branch
   git checkout -b feature/documentation
   
    Tests branch
   git checkout -b feature/tests
   ```

2. Develop Calculator Module
   
   On `feature/basic-operations`:
   ```python
    calculator.py
   class Calculator:
       def __init__(self):
           self.result = 0
       
       def add(self, a, b):
           return a + b
       
       def subtract(self, a, b):
           return a - b
       
       def multiply(self, a, b):
           return a * b
       
       def divide(self, a, b):
           if b == 0:
               raise ValueError("Cannot divide by zero")
           return a / b
   ```

   Make commits:
   ```bash
   git commit -m "feat: Add Calculator class"
   git commit -m "feat: Implement basic arithmetic operations"
   ```

   On `feature/advanced-operations`:
   ```python
    Add methods to calculator:
   def power(self, a, b):
       return a ** b
   
   def modulo(self, a, b):
       return a % b
   ```

   Make commits:
   ```bash
   git commit -m "feat: Add power operation"
   git commit -m "feat: Add modulo operation"
   ```

   On `feature/documentation`:
   ```markdown
    Calculator Module
   
   Simple calculator for basic math operations.
   
    Usage
   
   ```python
   from calculator import Calculator
   
   calc = Calculator()
   print(calc.add(5, 3))   8
   ```
   ```

   On `feature/tests`:
   ```python
    test_calculator.py
   from calculator import Calculator
   
   def test_add():
       calc = Calculator()
       assert calc.add(2, 3) == 5
   
   def test_divide_by_zero():
       calc = Calculator()
       try:
           calc.divide(5, 0)
           assert False
       except ValueError:
           pass
   ```

3. Merge Branches to Main
   
   ```bash
    Switch to main
   git checkout main
   
    Merge all features
   git merge feature/basic-operations -m "Merge: Basic calculator operations"
   git merge feature/advanced-operations -m "Merge: Advanced operations"
   git merge feature/documentation -m "Merge: Add documentation"
   git merge feature/tests -m "Merge: Add test suite"
   
    View final log
   git log --oneline --graph --all
   ```

4. Deliverables
   - [ ] All 4 branches created and committed to
   - [ ] Professional commit messages
   - [ ] All branches successfully merged to main
   - [ ] Calculator module is functional
   - [ ] Documentation and tests included
   - [ ] Clean Git history

 What to Submit

1. GitHub Repository URL with all commits visible
2. Screenshot of `git log --graph --oneline --all`
3. Brief summary of what you learned from this exercise

 Evaluation Criteria

- Git Workflow (40%): Proper use of branches and commits
- Code Quality (30%): Clean, working Python code
- Documentation (20%): Clear documentation and comments
- Commit Messages (10%): Professional, descriptive messages

---

 Assignment 4: Reflection and Documentation

Due: By end of week | Weight: 10%

 Objective
Reflect on your learning and document your progress.

 Requirements

Create a file called `WEEK_1_REFLECTION.md`:

```markdown
 Week 1 Reflection

 Key Learnings

 1. Development Environment
- What was the purpose of virtual environments?
- Why did we use each tool (Python, Git, VS Code)?

 2. Git & Version Control
- Explain the three stages of Git in your own words
- Why is version control important for team development?

 3. GitHub & Collaboration
- How would you explain GitHub to someone who's never used it?
- Why do good commit messages matter?

 Challenges Faced

1. Challenge: [Describe a challenge]
   Solution: [How you solved it]
   Learning: [What you learned]

2. Challenge: [Another challenge]
   Solution: [How you solved it]
   Learning: [What you learned]

 Skills Acquired

Rate yourself 1-5 on each (5 = expert, 1 = beginner):

- [ ] Python virtual environments: ___
- [ ] Git commands (add, commit, push): ___
- [ ] Git branching and merging: ___
- [ ] GitHub workflow: ___
- [ ] Terminal/command line: ___

 Goals for Next Week

1. [Goal for Week 2]
2. [Goal for Week 2]
3. [Goal for Week 2]

 Resources Used

- Pro Git book (chapters 1-3)
- GitHub guides
- YouTube tutorial on Git
- [Any other resources]

 Questions for Instructor

- Question 1?
- Question 2?
```

 Evaluation Criteria

- Thoughtfulness (50%): Deep reflection on learning
- Honesty (30%): Genuine assessment of skills and challenges
- Completeness (20%): All sections answered thoroughly

---

 Submission Instructions

 How to Submit

1. Create a new branch for your assignments:
   ```bash
   git checkout -b week1-assignments
   git add .
   git commit -m "Submit Week 1 assignments"
   git push origin week1-assignments
   ```

2. Create a Pull Request on GitHub
   - Title: "Week 1: Assignments Submission"
   - Description: List what you're submitting
   - Link to any external files

3. Fill out submission form with:
   - GitHub repository URL
   - GitHub PR link
   - Any notes

 Deadline
Friday 11:59 PM UTC of Week 1

 Grading Breakdown

| Assignment | Weight | Points |
|-----------|--------|--------|
| GitHub Repo & README | 20% | 20 |
| Environment Verification | 15% | 15 |
| Git Workflow Project | 25% | 25 |
| Reflection | 10% | 10 |
| Total | 70% | 70 |

Final Grade: 70 points max for Week 1

---

 Late Submission Policy

- On time: 100% credit
- 1 day late: 90% credit
- 2 days late: 80% credit
- 3+ days late: 50% credit

---

 Getting Help

- Discord: Post in week1-help
- Office Hours: Tue & Thu 4-6 PM UTC
- 1-on-1: Schedule with instructor

---

Good luck! 🚀
