 Week 4: DevOps & Automation

Duration: 5 days | Theme: Professional Development Practices | Difficulty: Advanced  
Prerequisite: Complete Weeks 1-3 (Git, Linux, AWS)

---

 🎯 Learning Objectives

By the end of this week, you will:
- ✅ Understand DevOps culture, principles, and practices (CALMS framework)
- ✅ Master Docker containerization (Dockerfile, images, containers, registries)
- ✅ Implement CI/CD pipelines with GitHub Actions
- ✅ Create automated testing and deployment workflows
- ✅ Set up monitoring, logging, and alerting with modern tools
- ✅ Deploy production-ready applications with full automation

---

 📖 Detailed Daily Schedule

 Day 1: DevOps Principles & Docker Fundamentals
Lecture: [01_devops_principles.md](./lecture_notes/01_devops_principles.md) | [02_docker_basics.md](./lecture_notes/02_docker_basics.md)

Topics:
- DevOps CALMS framework (Culture, Automation, Lean, Measurement, Sharing)
- DevOps vs traditional IT operations
- Containerization concepts and benefits
- Docker architecture: daemon, client, images, containers, registries
- Installing Docker and basic commands

Hands-On:
```bash
 Install Docker (Ubuntu/Debian)
sudo apt update
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
 Log out and back in for group changes

 Verify installation
docker --version
docker info

 First container
docker run hello-world

 List containers
docker ps            Running containers
docker ps -a         All containers

 Run interactive container
docker run -it ubuntu bash

 Run detached container
docker run -d --name my-nginx -p 8080:80 nginx

 View logs
docker logs my-nginx

 Stop and remove
docker stop my-nginx
docker rm my-nginx
```

Practice:
- [ ] Install Docker on your system
- [ ] Run 5 different official images
- [ ] Create and manage multiple containers
- [ ] Practice port mapping (-p flag)
- [ ] Explore volume mounting (-v flag)

---

 Day 2: Docker Images & Dockerfile
Lecture: [03_docker_images.md](./lecture_notes/03_docker_images.md)

Topics:
- Dockerfile instructions (FROM, RUN, CMD, COPY, ADD, ENV, EXPOSE)
- Building custom images
- Layer caching and optimization
- Multi-stage builds
- Docker Hub and private registries
- Image tagging and versioning

Hands-On:
```dockerfile
 Dockerfile for Python Flask app
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

```bash
 Build image
docker build -t my-flask-app:1.0 .

 Multi-stage build example
docker build -t my-flask-app:optimized -f Dockerfile.multistage .

 List images
docker images

 Tag image
docker tag my-flask-app:1.0 username/my-flask-app:latest

 Push to Docker Hub
docker login
docker push username/my-flask-app:latest

 Pull from registry
docker pull username/my-flask-app:latest

 Run built image
docker run -d -p 5000:5000 my-flask-app:1.0
```

Practice:
- [ ] Write Dockerfile for Week 2's Python app
- [ ] Implement multi-stage build
- [ ] Push image to Docker Hub
- [ ] Optimize image size
- [ ] Create .dockerignore file

---

 Day 3: CI/CD with GitHub Actions
Lecture: [04_cicd_github_actions.md](./lecture_notes/04_cicd_github_actions.md)

Topics:
- Continuous Integration vs Continuous Deployment
- GitHub Actions workflow syntax
- Triggers (push, pull_request, schedule)
- Jobs, steps, and actions
- Secrets and environment variables
- Automated testing in CI

Hands-On:
```yaml
 .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        pip install pytest flake8
    
    - name: Lint with flake8
      run: flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
    
    - name: Test with pytest
      run: pytest --cov=src --cov-report=xml
    
    - name: Upload coverage
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml

  build:
    needs: test
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Build Docker image
      run: docker build -t myapp:${{ github.sha }} .
    
    - name: Login to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}
    
    - name: Push to registry
      run: |
        docker tag myapp:${{ github.sha }} ${{ secrets.DOCKER_USERNAME }}/myapp:${{ github.sha }}
        docker push ${{ secrets.DOCKER_USERNAME }}/myapp:${{ github.sha }}
```

Practice:
- [ ] Create first GitHub Actions workflow
- [ ] Set up automated testing
- [ ] Configure secrets in GitHub
- [ ] Implement Docker build and push
- [ ] Add status badge to README

---

 Day 4: Monitoring, Logging & Infrastructure
Lecture: [05_monitoring_logging.md](./lecture_notes/05_monitoring_logging.md) | [03_infrastructure_as_code.md](./lecture_notes/03_infrastructure_as_code.md)

Topics:
- Monitoring principles (metrics, logs, traces)
- Prometheus and Grafana basics
- CloudWatch for AWS monitoring
- Centralized logging with ELK stack
- Infrastructure as Code with Terraform
- Configuration management with Ansible

Hands-On:
```yaml
 docker-compose.yml for monitoring
version: '3'
services:
  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
  
  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
  
  app:
    build: .
    ports:
      - "5000:5000"
```

```bash
 Terraform basic setup
 main.tf
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  tags = {
    Name = "Terraform-Managed"
  }
}
```

```bash
 Ansible playbook
 playbook.yml
---
- name: Deploy web application
  hosts: webservers
  become: yes
  
  tasks:
    - name: Install Docker
      apt:
        name: docker.io
        state: present
    
    - name: Start Docker
      service:
        name: docker
        state: started
        enabled: yes
    
    - name: Deploy container
      docker_container:
        name: webapp
        image: username/myapp:latest
        ports:
          - "80:5000"
        state: started
```

Practice:
- [ ] Set up Prometheus + Grafana locally
- [ ] Create Terraform configuration for EC2
- [ ] Write Ansible playbook for deployment
- [ ] Configure CloudWatch alarms
- [ ] Set up log aggregation

---

 Day 5: DevOps Capstone Project
Lecture: [06_devops_capstone.md](./lecture_notes/06_devops_capstone.md)

Complete CI/CD Pipeline Project:

```
Project Structure:
my-devops-project/
├── .github/
│   └── workflows/
│       ├── ci.yml               Build & test
│       ├── deploy-staging.yml   Deploy to staging
│       └── deploy-prod.yml      Deploy to production
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── ansible/
│   ├── playbook.yml
│   └── inventory.ini
├── monitoring/
│   ├── prometheus.yml
│   └── docker-compose.monitoring.yml
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── src/
│   └── app.py
└── tests/
    └── test_app.py
```

Implementation Steps:

1. Application Setup (30 min)
   - Create Python Flask application
   - Add health check endpoint
   - Write unit tests
   - Create Dockerfile

2. CI Pipeline (45 min)
   - Configure GitHub Actions workflow
   - Automated testing
   - Docker image build
   - Push to registry

3. Infrastructure (45 min)
   - Write Terraform configuration
   - Create AWS resources
   - Configure security groups

4. Deployment (30 min)
   - Ansible playbook for deployment
   - Rolling deployment strategy
   - Health checks

5. Monitoring (30 min)
   - Prometheus metrics
   - Grafana dashboards
   - Alerts configuration

---

 📚 Essential Resources

 Required Reading
1. Enhanced Lecture Notes:
   - [01_devops_principles.md](./lecture_notes/01_devops_principles.md) - DevOps culture
   - [02_docker_basics.md](./lecture_notes/02_docker_basics.md) - Docker fundamentals
   - [03_docker_images.md](./lecture_notes/03_docker_images.md) - Image building
   - [04_cicd_github_actions.md](./lecture_notes/04_cicd_github_actions.md) - CI/CD pipelines
   - [05_monitoring_logging.md](./lecture_notes/05_monitoring_logging.md) - Observability
   - [06_devops_capstone.md](./lecture_notes/06_devops_capstone.md) - Final project

2. External Resources:
   - [Docker Documentation](https://docs.docker.com/)
   - [GitHub Actions Docs](https://docs.github.com/en/actions)
   - [Terraform Tutorials](https://learn.hashicorp.com/terraform)
   - [Prometheus Docs](https://prometheus.io/docs/)

 Tools & Setup
```bash
 Required
- Docker Desktop / Docker Engine
- GitHub account with repository access
- AWS CLI configured
- Terraform CLI
- Ansible

 Optional but Recommended
- kubectl (for Kubernetes basics)
- Helm
- LocalStack (local AWS testing)
```

---

 💼 Weekly Deliverables

 Required Submissions

1. Docker Containerization (20%)
   - Dockerfile with best practices
   - Optimized multi-stage build
   - Image pushed to Docker Hub
   - Docker Compose configuration

2. CI/CD Pipeline (25%)
   - GitHub Actions workflow files
   - Automated testing configured
   - Build and push automation
   - Status badges in README

3. Infrastructure Code (20%)
   - Terraform configuration
   - Ansible playbooks
   - Infrastructure documentation
   - Deployment scripts

4. Monitoring Setup (20%)
   - Prometheus configuration
   - Grafana dashboards
   - Alerting rules
   - Log aggregation

5. Documentation & Runbooks (15%)
   - Architecture diagram
   - Deployment procedures
   - Troubleshooting guide
   - On-call runbook

---

 🎯 Exercises

 Exercise 1: Docker Mastery
- Containerize the Week 3 Flask application
- Implement multi-stage build reducing size by 50%
- Create docker-compose with database
- Implement health checks

 Exercise 2: CI/CD Pipeline
- Create workflow with 3 environments (dev, staging, prod)
- Implement automated security scanning
- Add performance testing stage
- Configure deployment approvals

 Exercise 3: Infrastructure as Code
- Create Terraform module for reusable infrastructure
- Implement Ansible role for application deployment
- Set up remote state management
- Create variable configurations for multiple environments

 Exercise 4: Monitoring & Alerting
- Set up Prometheus with custom metrics
- Create Grafana dashboard
- Configure PagerDuty/Slack alerts
- Implement distributed tracing

---

 📋 Assignment: Complete DevOps Pipeline

 Part A: Containerization (20 points)
- [ ] Dockerfile following best practices (5 pts)
- [ ] Multi-stage build optimization (5 pts)
- [ ] Docker Compose with all services (5 pts)
- [ ] Image published to registry (5 pts)

 Part B: CI/CD Implementation (30 points)
- [ ] GitHub Actions workflow with testing (10 pts)
- [ ] Automated Docker builds (5 pts)
- [ ] Multi-environment deployment (10 pts)
- [ ] Secrets management (5 pts)

 Part C: Infrastructure (20 points)
- [ ] Terraform configuration for AWS (10 pts)
- [ ] Ansible deployment automation (5 pts)
- [ ] Environment separation (5 pts)

 Part D: Observability (20 points)
- [ ] Metrics collection (5 pts)
- [ ] Logging aggregation (5 pts)
- [ ] Alerting configuration (5 pts)
- [ ] Dashboard creation (5 pts)

 Part E: Documentation (10 points)
- [ ] Architecture documentation (3 pts)
- [ ] Runbook for common issues (4 pts)
- [ ] README with badges (3 pts)

---

 🔗 Quick Navigation

| Resource | Description |
|----------|-------------|
| [� Lecture Notes](./lecture_notes/) | DevOps concepts and tools |
| [🎯 Exercises](./exercises/) | Hands-on practice |
| [📋 Assignments](./assignments/) | Graded projects |
| [📄 Cheatsheets](./cheatsheets/) | Quick command reference |

---

 ⚡ Daily Study Schedule

| Time | Activity |
|------|----------|
| 30 min | Read lecture notes |
| 45 min | Hands-on tool practice |
| 15 min | Configure workflows |
| 15 min | Test and document |

Total Daily Time: ~1.75 hours

---

 � Success Tips for Week 4

1. Version Everything: Docker tags, Git tags, Terraform state
2. Security First: Never commit secrets, use GitHub Secrets
3. Fail Fast: Implement health checks and validation
4. Automate Testing: Every commit should run tests
5. Monitor Early: Add observability from day one
6. Document Decisions: Why you chose specific tools
7. Practice Recovery: Know how to rollback deployments
8. Keep It Simple: Start basic, add complexity gradually

---

 🆘 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| `Docker permission denied` | Add user to docker group: `sudo usermod -aG docker $USER` |
| `Build context too large` | Use .dockerignore file |
| `CI workflow failing` | Check secrets configuration, YAML syntax |
| `Terraform state lock` | Run `terraform force-unlock <ID>` |
| `Ansible connection failed` | Check SSH keys, inventory file |
| `Prometheus not scraping` | Verify target configuration, network access |

---

 🎓 Week 4 Completion Checklist

- [ ] Docker installed and working
- [ ] Dockerfile created and optimized
- [ ] Image pushed to Docker Hub
- [ ] GitHub Actions workflow running
- [ ] Automated tests passing
- [ ] Terraform configuration working
- [ ] Ansible playbook tested
- [ ] Monitoring stack deployed
- [ ] Alerts configured
- [ ] Documentation complete
- [ ] All exercises done
- [ ] Assignment submitted

---

Previous: [Week 3: AWS Cloud ←](../Week_3/README.md) | Next: [Month 2: ML Techniques →](../../Month_2_ML_Techniques/README.md)
