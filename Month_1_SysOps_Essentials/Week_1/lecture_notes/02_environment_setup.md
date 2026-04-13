 Environment Setup: Building a Modern, Reproducible Dev Stack

 1. Introduction
Setting up your development environment is the first step to success in any technical program or professional project. This lecture will guide you through the process of creating a robust, reproducible, and industry-standard environment for AI/ML and software engineering. You will learn not just what to install, but why each tool matters, how to automate your setup, and how to troubleshoot common issues.

 2. Why Environment Setup Matters
- Reproducibility: Ensures your code runs the same way on any machine, reducing "it works on my machine" problems.
- Collaboration: Makes it easy to share code and onboard new team members.
- Productivity: A well-configured environment saves time and reduces friction.
- Professionalism: Mirrors the practices of top tech companies and research labs.

 3. Core Components of a Modern Dev Environment

 3.1. Python and Package Management
- Python (3.10+ recommended): The primary language for AI/ML.
- Virtual Environments: Use `python -m venv`, `conda`, or `poetry` to isolate dependencies per project.
- Package Managers: `pip`, `conda`, `pipenv`, and `poetry` for installing libraries.
- Best Practice: Always use a virtual environment. Never install packages globally unless absolutely necessary.

 3.2. Version Control
- Git: Industry-standard for tracking code changes and collaborating.
- GitHub CLI: For interacting with GitHub from the terminal.
- .gitignore: Prevents sensitive or unnecessary files from being tracked.

 3.3. Code Editor
- VS Code: Highly extensible, with support for Python, Docker, Git, and remote development.
- Extensions: Python, Docker, GitLens, Prettier, and Jupyter.
- Settings Sync: Use VS Code’s built-in sync or Settings Sync extension to keep your setup portable.

 3.4. Terminal and Shell
- bash/zsh/oh-my-zsh: Choose a shell that fits your workflow. `oh-my-zsh` adds productivity features.
- Aliases and Functions: Speed up common tasks.
- Prompt Customization: Use tools like `powerlevel10k` for informative prompts.

 3.5. Containers and Cloud SDKs
- Docker: For containerizing applications and ensuring consistent environments.
- Docker Compose: For multi-container setups.
- Cloud SDKs: AWS CLI, Azure CLI, GCP SDK for cloud integration.

 4. Step-by-Step Setup Guide

 4.1. Install Python and Create a Virtual Environment
```sh
 macOS/Linux
python3 -m venv .venv
source .venv/bin/activate
 Windows
python -m venv .venv
.venv\Scripts\activate
```
Install packages with `pip install -r requirements.txt`.

 4.2. Install Git and Configure User Info
```sh
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```
Clone a repository: `git clone <repo-url>`

 4.3. Set Up VS Code
- Download from [code.visualstudio.com](https://code.visualstudio.com/)
- Install recommended extensions (Python, Docker, GitLens, Jupyter)
- Sync settings for portability

 4.4. Configure Your Shell
- Install `zsh` and `oh-my-zsh` for productivity
- Add aliases and functions to `.zshrc` or `.bashrc`
- Customize your prompt for context (e.g., show git branch, Python venv)

 4.5. Install Docker and Cloud SDKs
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
- [Google Cloud SDK](https://cloud.google.com/sdk/docs/install)

 4.6. Automate Your Setup
- Use a `Makefile` or shell script to automate environment setup and testing
- Example `Makefile` targets: `setup`, `test`, `lint`, `clean`
- Document every manual step in a `README.md` or `SETUP.md`

 4.7. Validate Your Environment
- Run `python --version`, `git --version`, `docker --version`, etc.
- Run a sample script or notebook to ensure all dependencies work

 5. Troubleshooting & Best Practices
- Common Issues: PATH problems, conflicting Python versions, missing dependencies
- Solutions: Use `which python`, `pip list`, and check environment variables
- Never commit secrets: Use `.env` files and add them to `.gitignore`
- Reproducibility: Use `requirements.txt` or `environment.yml` for dependencies
- Onboarding: Keep setup instructions up to date for new team members

 6. Advanced Topics
- Dev Containers: Use VS Code’s `devcontainer.json` for fully reproducible environments
- Remote Development: Use SSH or Codespaces for cloud-based coding
- Dotfiles: Version control your shell/editor configs for portability

 7. Resources
- [Official Python Setup Guide](https://realpython.com/installing-python/)
- [VS Code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)
- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [Docker Getting Started](https://docs.docker.com/get-started/)
- [Awesome Dev Environment](https://github.com/jondot/awesome-devenv)
- [VS Code Dev Containers](https://code.visualstudio.com/docs/remote/containers)

---
