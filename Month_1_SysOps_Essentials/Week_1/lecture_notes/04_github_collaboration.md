 Lecture 4: GitHub & Collaboration

 From Git to GitHub

Git is the version control system. GitHub is a platform for hosting Git repositories and facilitating collaboration.

---

 🎯 Learning Objectives

- ✅ Create GitHub account and configure it
- ✅ Understand GitHub workflow and collaboration
- ✅ Create professional repositories
- ✅ Write excellent README files
- ✅ Use GitHub Issues and Pull Requests
- ✅ Collaborate effectively with teams

---

 🚀 Getting Started with GitHub

 Create Account

1. Go to https://github.com
2. Sign up with email
3. Verify email
4. Choose username (this is part of your professional identity!)

Username Tips:
- ✅ Simple and professional: `john-smith`, `jsmith123`
- ✅ Easy to type and remember
- ✅ Consistent with other platforms if possible
- ❌ Avoid numbers, special characters, or inside jokes: `xXgamer420Xx`

 Generate SSH Key (Recommended)

SSH keys allow secure communication with GitHub without entering password every time.

```bash
 Generate SSH key
ssh-keygen -t ed25519 -C "your@email.com"

 When prompted, press Enter for default location
 Don't set a passphrase for now (leave blank)

 Copy public key
cat ~/.ssh/id_ed25519.pub

 Go to GitHub Settings → SSH and GPG keys
 Click "New SSH key"
 Paste your key
```

 Configure Git with GitHub

```bash
 Set your GitHub email
git config --global user.name "Your Name"
git config --global user.email "your.github@email.com"

 Test connection
ssh -T git@github.com
 Should say: "Hi username! You've successfully authenticated..."
```

---

 📁 Creating Your First Repository

 On GitHub Web Interface

1. Click "+" in top right → "New repository"
2. Name it: `ml-learning-2024` (or similar)
3. Add description: "My machine learning learning journey for 3MTT program"
4. Choose: Public (for portfolio) or Private (for personal)
5. Check "Add a README file"
6. Add `.gitignore` for Python
7. Choose license (MIT or Apache 2.0 recommended)
8. Click "Create repository"

 Professional README Structure

Your README is the first impression. Make it count!

```markdown
 Project Name

Short description of what this is.

 📚 Table of Contents

- [Overview](overview)
- [Prerequisites](prerequisites)
- [Installation](installation)
- [Usage](usage)
- [Features](features)
- [Project Structure](project-structure)
- [Contributing](contributing)
- [License](license)

 🎯 Overview

Detailed description explaining:
- What problem does it solve?
- Who is it for?
- Key features

 📋 Prerequisites

- Python 3.9+
- Dependencies listed...

 💾 Installation

Step-by-step guide:

```bash
git clone https://github.com/username/project.git
cd project
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

 🚀 Usage

How to use your project:

```python
from module import function
result = function(arg)
```

 ✨ Features

- Feature 1
- Feature 2
- Feature 3

 📂 Project Structure

```
project/
├── README.md
├── requirements.txt
├── src/
│   ├── __init__.py
│   └── main.py
├── tests/
│   └── test_main.py
└── .gitignore
```

 🤝 Contributing

Explain how others can contribute...

 📄 License

MIT License - see LICENSE file for details

---

 🔗 Links

- [Documentation](link)
- [Issues](link/issues)
- [Author GitHub](link)
```

---

 💻 Clone and Work Locally

```bash
 Clone your repository
git clone git@github.com:your-username/repo-name.git
cd repo-name

 Create a virtual environment
python -m venv venv
source venv/bin/activate   macOS/Linux

 Make some changes
echo "Hello" > hello.txt

 Commit and push
git add .
git commit -m "Add hello.txt"
git push origin main
```

---

 🔀 GitHub Workflow: Branches and Pull Requests

 Pull Request Workflow

This is how professional teams collaborate:

```
1. Create Feature Branch
   git checkout -b feature/cool-feature

2. Make Changes & Commit
   git add .
   git commit -m "Implement cool feature"

3. Push to GitHub
   git push origin feature/cool-feature

4. Open Pull Request
   - Go to GitHub
   - GitHub suggests "Create Pull Request"
   - Write description explaining changes
   - Link any related issues

5. Code Review
   - Team members review code
   - Request changes if needed
   - Discuss in comments

6. Merge Pull Request
   - Once approved, click "Merge Pull Request"
   - Delete branch after merging

7. Update Local
   git checkout main
   git pull origin main
```

 Pull Request Description Template

```markdown
 Description
Brief description of what this PR does.

 Related Issue
Fixes 123

 Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change

 How to Test
Steps to test this change...

 Checklist
- [ ] Code follows style guidelines
- [ ] Comments added for complex logic
- [ ] Tests updated
- [ ] Documentation updated
```

---

 🏷️ GitHub Issues

Issues are for tracking bugs, features, and discussions.

 Creating an Issue

```markdown
 Bug: Login fails with special characters

 Description
When username contains special characters like @, login fails.

 Steps to Reproduce
1. Go to login page
2. Enter username "user@test.com"
3. Enter password
4. Click submit
5. Error appears

 Expected Behavior
User should successfully login

 Actual Behavior
"Invalid username" error appears

 Environment
- Browser: Chrome 98
- OS: macOS 12.1

 Screenshots
[if applicable]
```

 Linking Issues

```bash
 In commit message
git commit -m "Fix login with special chars. Fixes 42"

 In pull request description
Fixes 42
Closes 43
Resolves 44
```

---

 🔐 GitHub Security

 Protect Your Main Branch

1. Go to Repository Settings → Branches
2. Under "Branch protection rules", click "Add rule"
3. Apply to: `main` or `master`
4. Check:
   - "Require pull request reviews before merging"
   - "Require status checks to pass"
   - "Include administrators"

 Adding Collaborators

1. Repository Settings → Collaborators
2. Click "Add people"
3. Search for username
4. Select permission level (maintain/write/read)

---

 📊 GitHub Usage Best Practices

 For Personal Projects

```
main branch (always production-ready)
↓
develop branch (integration)
↓
feature/* branches (individual work)
```

 Daily Workflow

```bash
 At start of day
git checkout develop
git pull origin develop

 Create feature branch
git checkout -b feature/my-feature

 Work and commit
 (make multiple commits, each with clear message)

 When ready to merge
git push origin feature/my-feature

 Go to GitHub, open PR

 After review and approval
 Merge PR

 Back to main
git checkout develop
git pull
```

---

 🆘 Common GitHub Issues

 Problem: Accidentally Pushed Sensitive Data

```bash
 Remove from history
git filter-branch --tree-filter 'rm -f secret.key' HEAD

 Force push (dangerous!)
git push --force-with-lease

 Better: Use GitHub's remove sensitive data tool
```

 Problem: Out of Sync Fork

```bash
 Add upstream
git remote add upstream https://github.com/original/repo.git

 Fetch latest
git fetch upstream

 Rebase your main
git rebase upstream/main

 Force push to your fork
git push --force-with-lease origin main
```

---

 📈 Building Your GitHub Profile

 Make Your Profile Stand Out

1. Professional Picture - Use a clear headshot
2. Good Bio - "ML Engineer | Python Developer | Open Source"
3. Pin Repositories - Show off your best projects
4. Public Contributions - Have them visible
5. README Profile - Create `username/username` repo

 README Profile Template

```markdown
 Hi there 👋

I'm a Machine Learning Engineer focused on...

 🛠️ Tech Stack
- Python, PyTorch, TensorFlow
- AWS, Docker
- Git, Linux

 📊 Recent Projects
- [Project 1](link) - Description
- [Project 2](link) - Description

 📈 GitHub Stats
![Your GitHub stats](https://github-readme-stats.vercel.app/api?username=yourname)

 🔗 Connect
- [LinkedIn](link)
- [Portfolio](link)
- [Email](mailto:email@example.com)
```

---

 ✅ Checklist: GitHub Mastery

- [ ] GitHub account created
- [ ] SSH keys generated and configured
- [ ] First repository created
- [ ] Professional README written
- [ ] Comfortable with clone/push/pull workflow
- [ ] Understand pull requests
- [ ] Can create and manage issues
- [ ] Know how to protect branches

---

 📚 Next Steps

1. Create your GitHub account
2. Create first repository
3. Write professional README
4. Push code using SSH
5. Practice PR workflow
6. Start building your portfolio

---

 🔗 Additional Resources

- [GitHub Hello World](https://guides.github.com/activities/hello-world/)
- [GitHub Guides](https://guides.github.com/)
- [GitHub Learning Lab](https://lab.github.com/)
- [GitHub Markdown Guide](https://guides.github.com/features/mastering-markdown/)
- [README Best Practices](https://readme.so/)
