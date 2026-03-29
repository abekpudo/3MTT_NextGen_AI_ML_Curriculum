 Technology Stack & Tools Setup Guide

Program: 3MTT NextGen AI & Machine Learning  
Last Updated: March 2026

---

 🛠️ Complete Technology Stack

 Programming & Development

| Tool | Purpose | Version | Installation |
|------|---------|---------|-------------|
| Python | Primary language | 3.9+ | [python.org](https://www.python.org/) |
| Jupyter Notebook | Interactive coding | Latest | `pip install jupyter` |
| VS Code | Code editor | Latest | [code.visualstudio.com](https://code.visualstudio.com/) |
| PyCharm | IDE (optional) | Latest | [jetbrains.com](https://www.jetbrains.com/) |
| Git | Version control | Latest | [git-scm.com](https://git-scm.com/) |
| GitHub | Repository hosting | N/A | [github.com](https://github.com/) |

 Data Science & ML Libraries

```bash
 Core packages
pip install numpy pandas scikit-learn matplotlib seaborn

 Deep learning frameworks
pip install tensorflow torch torchvision

 NLP
pip install nltk spacy textblob transformers gensim

 Big data
pip install pyspark dask[complete]

 APIs & deployment
pip install fastapi flask uvicorn

 MLOps
pip install mlflow wandb

 Data versioning
pip install dvc

 Other utilities
pip install jupyter lab ipython pandas-profiling
```

 Month-by-Month Tech Requirements

 Month 1: SysOps
- Required: Git, GitHub, Linux/Bash, AWS Account
- Tools: VS Code, Terminal
- Skills: Shell scripting, git workflow

 Month 2: ML Techniques
- Add to Month 1: NumPy, Pandas, scikit-learn
- New Libraries: XGBoost, LightGBM
- Tools: Jupyter Notebook, Anaconda/Conda

 Month 3: NLP
- Add to Month 2: NLTK, spaCy, TextBlob
- Optional: Transformers, Gensim
- Datasets: IMDB reviews, 20 Newsgroups

 Month 4: Computer Vision
- Add to Month 3: TensorFlow, Keras, PyTorch
- Libraries: OpenCV, PIL, torchvision
- Datasets: CIFAR-10, ImageNet, MNIST

 Month 5: Big Data & Deployment
- Add to Month 4: Spark, Dask
- New Tools: Docker, FastAPI
- Monitoring: MLflow, Prometheus (optional)

---

 🔧 Installation Guides

 System Requirements
- OS: macOS, Linux, or Windows (WSL2)
- RAM: 8GB minimum (16GB recommended)
- Storage: 50GB free space (for datasets, models)
- Internet: High-speed (for downloads, cloud services)

 Python Environment Setup (Recommended)

 Using Anaconda (Simplest)
```bash
 Download and install from https://www.anaconda.com/

 Create environment for the program
conda create -n 3mtt python=3.9 jupyter numpy pandas scikit-learn

 Activate environment
conda activate 3mtt

 Add to ~/.bashrc or ~/.zshrc to auto-activate
echo "conda activate 3mtt" >> ~/.bashrc
```

 Using venv (Lightweight)
```bash
 Create virtual environment
python3.9 -m venv ~/envs/3mtt

 Activate
source ~/envs/3mtt/bin/activate   macOS/Linux
 or
~/envs/3mtt\Scripts\activate   Windows

 Install requirements
pip install -r requirements.txt
```

 Using Poetry (Modern)
```bash
 Install Poetry
curl -sSL https://install.python-poetry.org | python3 -

 Create project
poetry new 3mtt-project
cd 3mtt-project

 Add dependencies
poetry add numpy pandas scikit-learn jupyter

 Activate environment
poetry shell
```

---

 IDE Setup

 VS Code Extensions for ML Development
1. Python (Microsoft)
2. Pylance (Microsoft)
3. Jupyter (Microsoft)
4. Code Runner
5. GitLens
6. Docker
7. Thunder Client (API testing)
8. Black Formatter
9. Pylint

 VS Code Settings (settings.json)
```json
{
  "python.defaultInterpreterPath": "/path/to/3mtt/bin/python",
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": true,
  "editor.formatOnSave": true,
  "python.formatting.provider": "black",
  "[python]": {
    "editor.defaultFormatter": "ms-python.python",
    "editor.formatOnSave": true
  }
}
```

---

 Git & GitHub Setup

```bash
 Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

 Generate SSH key for GitHub
ssh-keygen -t ed25519 -C "your.email@example.com"

 Add to GitHub
 1. Copy public key: cat ~/.ssh/id_ed25519.pub
 2. Go to GitHub Settings > SSH Keys
 3. Paste and save

 Test connection
ssh -T git@github.com
```

---

 AWS Setup

1. Create AWS Account
   - Visit [aws.amazon.com](https://aws.amazon.com/)
   - Click "Create AWS Account"
   - Set up billing and payment method

2. Install AWS CLI
   ```bash
   pip install awscli
   aws --version
   ```

3. Configure AWS Credentials
   ```bash
   aws configure
    Enter: Access Key ID, Secret Access Key, Region, Output format
   ```

4. Create IAM User (for safety)
   - Go to IAM Dashboard
   - Create user with EC2, S3, and RDS permissions
   - Generate access keys

---

 Docker Installation

 macOS
```bash
 Using Homebrew
brew install docker

 Or download Docker Desktop from docker.com
 Includes Docker Compose
```

 Linux (Ubuntu)
```bash
sudo apt-get update
sudo apt-get install docker.io docker-compose

 Add your user to docker group
sudo usermod -aG docker $USER
```

 Windows
- Download Docker Desktop for Windows
- Enable WSL2
- Install from Microsoft Store or docker.com

 Test Installation
```bash
docker run hello-world
docker-compose --version
```

---

 📦 Requirements.txt Template

```txt
 Core Data Science
numpy==1.24.1
pandas==1.5.2
scikit-learn==1.2.1

 Visualization
matplotlib==3.6.3
seaborn==0.12.2
plotly==5.13.0

 Deep Learning
tensorflow==2.11.0
torch==1.13.1
torchvision==0.14.1

 NLP
nltk==3.8.1
spacy==3.5.0
textblob==0.17.1
transformers==4.27.0

 Big Data
pyspark==3.3.1
dask[complete]==2023.2.0

 APIs
fastapi==0.95.0
flask==2.2.2
uvicorn==0.20.0

 MLOps
mlflow==2.1.0
wandb==0.14.0
dvc==2.50.0

 Development
jupyter==1.0.0
jupyterlab==3.6.1
ipython==8.11.0
black==23.1.0
pylint==2.16.2
pytest==7.2.2

 Utilities
python-dotenv==0.21.0
pyyaml==6.0
requests==2.28.2
```

---

 🚀 Quick Start Checklist

 Before Month 1 Starts
- [ ] Python 3.9+ installed
- [ ] IDE (VS Code) installed with Python extension
- [ ] Git installed and configured
- [ ] GitHub account created
- [ ] Anaconda/conda or venv set up
- [ ] Jupyter Notebook working
- [ ] AWS account created (not mandatory but recommended)

 During Month 1
- [ ] Linux/Bash shell comfortable
- [ ] Git workflows practiced
- [ ] GitHub PRs and reviews understood
- [ ] AWS account configured
- [ ] Docker installed (for Month 5)

 Month 2+ Requirements
- [ ] scikit-learn for ML
- [ ] TensorFlow/PyTorch for deep learning
- [ ] Jupyter Lab productive setup
- [ ] MLflow for experiment tracking

---

 📚 Resource Links

 Official Documentation
- [Python Docs](https://docs.python.org/3/)
- [NumPy](https://numpy.org/doc/)
- [Pandas](https://pandas.pydata.org/docs/)
- [scikit-learn](https://scikit-learn.org/stable/documentation.html)
- [TensorFlow](https://www.tensorflow.org/api_docs)
- [PyTorch](https://pytorch.org/docs/stable/index.html)
- [FastAPI](https://fastapi.tiangolo.com/)
- [Docker](https://docs.docker.com/)

 Learning Resources
- [Real Python](https://realpython.com/)
- [Towards Data Science](https://towardsdatascience.com/)
- [Analytics Vidhya](https://www.analyticsvidhya.com/)
- [Papers with Code](https://paperswithcode.com/)
- [Kaggle Learn](https://www.kaggle.com/learn)

 Communities
- [Stack Overflow](https://stackoverflow.com/) - Q&A
- [Reddit r/MachineLearning](https://www.reddit.com/r/MachineLearning/)
- [GitHub Discussions](https://github.com/discussions)
- Discord communities (shared in onboarding)

---

 💡 Pro Tips

1. Use Virtual Environments - Always, prevents conflicts
2. Keep Requirements Updated - Document dependencies regularly
3. Use .gitignore - Never commit data files, models, or secrets
4. Jupyter Notebooks - Great for exploration, not production
5. Version Your Data - Use DVC for reproducibility
6. Test Early, Test Often - Write tests alongside code
7. Document as You Go - Don't leave it for the end
8. Use Type Hints - Makes code more maintainable
9. Follow PEP 8 - Use black formatter automatically
10. Backup Your Work - GitHub, Google Drive, or similar

---

 🐛 Troubleshooting

 Common Issues

"Python not found"
```bash
 Check installation
python3 --version
which python3

 Add to PATH if needed (on macOS)
echo 'export PATH=/usr/local/bin:$PATH' >> ~/.zshrc
```

"Module not found"
```bash
 Ensure environment is activated
conda activate 3mtt

 Install missing module
pip install module_name
```

"Git command not recognized"
```bash
 Install Git
brew install git   macOS
sudo apt-get install git   Linux

 Verify
git --version
```

"Docker daemon not running"
- Start Docker Desktop application
- Or: `docker daemon` (Linux)

"Out of memory"
- Reduce batch size in models
- Use Dask for chunked processing
- Split dataset into smaller batches

---

 📝 Useful Commands Reference

 Python/Jupyter
```bash
 Create Jupyter notebook
jupyter notebook

 Run specific notebook
jupyter nbconvert --to notebook --execute notebook.ipynb

 Install in notebook environment
!pip install package_name
```

 Git
```bash
 Clone repository
git clone https://github.com/user/repo.git

 Create and switch branch
git checkout -b feature-branch

 Commit changes
git add .
git commit -m "Clear commit message"

 Push to remote
git push origin feature-branch

 Create pull request
 (via GitHub web interface)
```

 Docker
```bash
 Build image
docker build -t my-app:latest .

 Run container
docker run -p 8000:8000 my-app:latest

 Push to Docker Hub
docker push username/my-app:latest

 Docker Compose
docker-compose up
docker-compose down
```

 AWS
```bash
 List S3 buckets
aws s3 ls

 Upload to S3
aws s3 cp file.txt s3://bucket-name/

 Launch EC2 instance
aws ec2 run-instances --image-id ami-xxx --instance-type t2.micro
```

---

 🔐 Security Best Practices

1. Never Commit Secrets
   - Use `.env` files (add to .gitignore)
   - Use AWS IAM roles for production
   - Use environment variables

2. Example .env file
   ```
   AWS_ACCESS_KEY_ID=your_key
   AWS_SECRET_ACCESS_KEY=your_secret
   DATABASE_URL=your_db_url
   ```

3. Load in code
   ```python
   from dotenv import load_dotenv
   import os
   
   load_dotenv()
   aws_key = os.getenv('AWS_ACCESS_KEY_ID')
   ```

4. Use GitHub Secrets for CI/CD
5. Enable 2FA on all accounts
6. Rotate Credentials regularly
7. Use SSH Keys instead of passwords

---

For additional help, refer to official documentation or ask in course Discord/forums.

