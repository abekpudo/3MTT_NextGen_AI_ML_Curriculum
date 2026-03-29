 Lecture 2: Development Environment Setup

 Overview

A professional development environment is crucial. In this lecture, you'll set up the exact tools you'll use throughout the entire 5-month program.

---

 🎯 Learning Objectives

- ✅ Install Python 3.9+ on your machine
- ✅ Set up virtual environments (venv/conda)
- ✅ Install and configure VS Code
- ✅ Install Git and configure it globally
- ✅ Verify your complete setup

---

 📋 What You'll Install

1. Python 3.9+ - Core programming language
2. Git - Version control
3. VS Code - Code editor (or alternative IDE)
4. Virtual Environment - Project isolation
5. Essential Python packages - pandas, numpy, jupyter, etc.

---

 Step-by-Step Installation

 1. Python Installation

 macOS (using Homebrew)
```bash
 Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

 Install Python 3.9+
brew install python@3.9

 Verify installation
python3 --version
```

 Windows
```bash
 Download from https://www.python.org/downloads/
 Run installer, make sure to check "Add Python to PATH"
 Verify installation
python --version
```

 Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install python3.9 python3-pip python3-venv

 Verify
python3 --version
```

 2. Install Git

 macOS
```bash
brew install git
git --version
```

 Windows
Download from: https://git-scm.com/download/win

 Linux
```bash
sudo apt install git
git --version
```

 3. Configure Git Globally

```bash
 Set your identity
git config --global user.name "Your Full Name"
git config --global user.email "your.email@example.com"

 Set default editor
git config --global core.editor "vim"

 Verify configuration
git config --global --list
```

 4. Virtual Environment Setup

 Using venv (Built-in)
```bash
 Create project directory
mkdir my-ml-project
cd my-ml-project

 Create virtual environment
python3 -m venv venv

 Activate virtual environment
 macOS/Linux
source venv/bin/activate

 Windows
venv\Scripts\activate

 You should see (venv) at start of terminal prompt
```

 Using conda (Alternative)
```bash
 Install Anaconda from https://www.anaconda.com/

 Create conda environment
conda create -n ml-env python=3.9

 Activate
conda activate ml-env

 Deactivate when done
conda deactivate
```

 5. Install VS Code

1. Download from: https://code.visualstudio.com/
2. Install following platform-specific instructions
3. Launch VS Code

 Recommended Extensions
```bash
 Install these extensions in VS Code:
 - Python (by Microsoft)
 - Pylance (by Microsoft)
 - Jupyter (by Microsoft)
 - Git Graph
 - Better Comments
 - Code Runner
```

 6. Install Essential Python Packages

```bash
 Activate your virtual environment first!
source venv/bin/activate   macOS/Linux
 OR
venv\Scripts\activate   Windows

 Upgrade pip
pip install --upgrade pip

 Install essential packages
pip install jupyter numpy pandas matplotlib seaborn scikit-learn

 Verify installation
python -c "import numpy; print(numpy.__version__)"
```

---

 🔧 Configuration Tips

 Create .gitignore File

```bash
 In your project root
cat > .gitignore << 'EOF'
 Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
env/
ENV/

 Jupyter
.ipynb_checkpoints/
*.ipynb

 IDE
.vscode/
.idea/
*.swp
*.swo

 OS
.DS_Store
Thumbs.db

 Data
*.csv
*.xlsx
data/

 Logs
*.log
EOF
```

 Create requirements.txt

```bash
 Generate from current environment
pip freeze > requirements.txt

 Example requirements.txt:
 numpy==1.21.0
 pandas==1.3.0
 jupyter==1.0.0
 scikit-learn==0.24.2
```

 VS Code Settings

Create `.vscode/settings.json`:

```json
{
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": true,
  "python.formatting.provider": "black",
  "editor.formatOnSave": true,
  "editor.rulers": [80, 120],
  "[python]": {
    "editor.defaultFormatter": "ms-python.python",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.organizeImports": true
    }
  }
}
```

---

 ✅ Verification Checklist

After setup, verify everything works:

```bash
 Check Python
python3 --version   Should be 3.9+

 Check Git
git --version

 Check virtual environment (should show path to your env)
which python

 Check packages
python -c "import numpy, pandas, jupyter; print('All good!')"

 Start Jupyter
jupyter notebook   Should open in browser
```

---

 🆘 Troubleshooting

 Python not found
- macOS: Try `python3` instead of `python`
- Windows: Add Python to PATH (run installer again, select "Add Python to PATH")

 Virtual environment won't activate
- macOS/Linux: Make sure you're in correct directory and file has execute permissions
- Windows: Run PowerShell as Administrator

 Git configuration issues
```bash
 Reset configuration
git config --global --unset user.name
git config --global --unset user.email

 Reconfigure
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

---

 📚 Next Steps

1. Complete all installation steps above
2. Run the verification checklist
3. Create your first virtual environment
4. Write a simple Python script and run it
5. Ready for Git lecture!

---

 📖 Additional Resources

- [Python Official Docs](https://docs.python.org/3/)
- [Virtual Environments Guide](https://docs.python.org/3/tutorial/venv.html)
- [VS Code Python Setup](https://code.visualstudio.com/docs/python/python-tutorial)
- [Git Installation](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
