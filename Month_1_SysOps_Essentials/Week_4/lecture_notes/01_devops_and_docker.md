 DevOps and Docker: Containerization Fundamentals

 1. Introduction
Docker revolutionized software development by providing lightweight, portable containers that package applications with all their dependencies. This lecture covers Docker fundamentals, container management, image building, and deployment strategies for modern DevOps workflows.

 2. Why Docker Matters
- Consistency: Same environment across development, testing, and production
- Isolation: Applications run independently without conflicts
- Portability: Run anywhere Docker is installed
- Efficiency: Lightweight compared to virtual machines
- Scalability: Easy to replicate and scale containers
- DevOps Integration: Seamless CI/CD pipeline integration

 3. Docker Installation and Setup

 3.1. Installing Docker
```bash
 macOS - Using Homebrew
brew install --cask docker

 Linux (Ubuntu/Debian)
 Update package index
sudo apt-get update

 Install prerequisites
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release

 Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

 Set up stable repository
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

 Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

 Start Docker service
sudo systemctl start docker
sudo systemctl enable docker

 Add user to docker group (log out and back in for changes to take effect)
sudo usermod -aG docker $USER

 Verify installation
docker --version
docker-compose --version
docker info
```

 3.2. Docker Configuration
```bash
 Configure Docker daemon
cat > /etc/docker/daemon.json << EOF
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2",
  "experimental": false
}
EOF

 Restart Docker to apply configuration
sudo systemctl restart docker

 Login to Docker Hub
docker login

 Configure Docker context
docker context ls
docker context create my-context --docker "host=unix:///var/run/docker.sock"
docker context use my-context
```

 4. Docker Fundamentals

 4.1. Core Concepts
```bash
 Docker architecture components:
 - Docker Daemon: Background service managing containers
 - Docker Client: CLI tool for interacting with daemon
 - Docker Images: Read-only templates for containers
 - Docker Containers: Runnable instances of images
 - Docker Registry: Storage for images (Docker Hub, ECR, etc.)

 Check Docker system information
docker info

 View Docker disk usage
docker system df

 Clean up unused resources
docker system prune -f
docker system prune -a -f   Remove all unused images, not just dangling ones
```

 4.2. Basic Container Operations
```bash
 Run a simple container
docker run hello-world

 Run an interactive Ubuntu container
docker run -it ubuntu:latest bash

 Run a container in detached mode (background)
docker run -d --name my-nginx nginx:latest

 List running containers
docker ps

 List all containers (including stopped)
docker ps -a

 List container IDs only
docker ps -q

 Stop a container
docker stop my-nginx

 Start a stopped container
docker start my-nginx

 Restart a container
docker restart my-nginx

 Kill a container (forceful stop)
docker kill my-nginx

 Remove a stopped container
docker rm my-nginx

 Remove a running container (force)
docker rm -f my-nginx

 Remove all stopped containers
docker container prune -f
```

 5. Working with Docker Images

 5.1. Image Management
```bash
 Search for images on Docker Hub
docker search python

 Pull an image from Docker Hub
docker pull python:3.9-slim

 Pull a specific version
docker pull nginx:1.21-alpine

 List local images
docker images

 List dangling (untagged) images
docker images -f "dangling=true"

 Remove an image
docker rmi python:3.9-slim

 Remove unused images
docker image prune -f

 Remove all unused images (not just dangling)
docker image prune -a -f

 Tag an image
docker tag myapp:latest myapp:v1.0

 Save image to tar file
docker save -o myapp.tar myapp:latest

 Load image from tar file
docker load -i myapp.tar

 Inspect image details
docker inspect python:3.9-slim

 View image history (layers)
docker history python:3.9-slim
```

 5.2. Building Docker Images
```bash
 Basic Dockerfile structure
 Build an image from Dockerfile
docker build -t myapp:latest .

 Build with specific Dockerfile
docker build -f Dockerfile.prod -t myapp:prod .

 Build with no cache (fresh build)
docker build --no-cache -t myapp:latest .

 Build with build arguments
docker build --build-arg PYTHON_VERSION=3.9 -t myapp:latest .

 Build and tag with multiple tags
docker build -t myapp:latest -t myapp:v1.0 .

 View build output in detail
docker build --progress=plain -t myapp:latest .
```

 6. Dockerfile Best Practices

 6.1. Writing Efficient Dockerfiles
```dockerfile
 Example: Production-ready Python application Dockerfile

 Stage 1: Build dependencies
FROM python:3.9-slim as builder

 Set working directory
WORKDIR /app

 Install build dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/

 Copy requirements first (for better caching)
COPY requirements.txt .

 Install Python dependencies
RUN pip install --no-cache-dir --user -r requirements.txt

 Stage 2: Production image
FROM python:3.9-slim

 Create non-root user for security
RUN groupadd -r appuser && useradd -r -g appuser appuser

 Set working directory
WORKDIR /app

 Copy installed packages from builder stage
COPY --from=builder /root/.local /home/appuser/.local

 Copy application code
COPY --chown=appuser:appuser . .

 Set environment variables
ENV PATH=/home/appuser/.local/bin:$PATH \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    FLASK_ENV=production

 Switch to non-root user
USER appuser

 Expose port
EXPOSE 5000

 Health check
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:5000/health || exit 1

 Run the application
CMD ["python", "app.py"]
```

 6.2. Dockerfile Optimization Techniques
```dockerfile
 1. Use specific base image tags (not 'latest')
FROM python:3.9.7-slim-buster   Good
 FROM python:latest               Bad - not reproducible

 2. Minimize layers by combining RUN commands
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/   Good - single layer

 3. Copy requirements before code for better caching
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .   This layer changes often, but pip install is cached

 4. Use .dockerignore to reduce build context
 .dockerignore file contents:
/
!app.py
!requirements.txt
!src/
```

```bash
 Create .dockerignore file
cat > .dockerignore << EOF
 Git
.git
.gitignore

 Python
__pycache__
.pyc
.pyo
.pyd
.Python
.so
.egg
.egg-info
dist
build

 Virtual environments
venv/
env/
ENV/

 IDE
.vscode
.idea
.swp
.swo

 Testing
.pytest_cache
.coverage
htmlcov

 Documentation
.md
docs/

 Local development
.env
.env.local
.log
EOF
```

 7. Container Networking

 7.1. Docker Networks
```bash
 List networks
docker network ls

 Create a bridge network
docker network create my-network

 Create network with specific subnet
docker network create --subnet=172.20.0.0/16 --gateway=172.20.0.1 my-network

 Inspect network
docker network inspect my-network

 Run container with network
docker run -d --name container1 --network my-network nginx

 Connect container to network
docker network connect my-network container2

 Disconnect container from network
docker network disconnect my-network container2

 Remove network
docker network rm my-network

 Clean up unused networks
docker network prune -f
```

 7.2. Port Mapping and Exposure
```bash
 Map container port to host port
docker run -d -p 8080:80 nginx   Host:Container

 Map to random host port
docker run -d -p 80 nginx

 Map specific IP and port
docker run -d -p 127.0.0.1:8080:80 nginx

 Map multiple ports
docker run -d -p 8080:80 -p 8443:443 nginx

 Map all exposed ports to random host ports
docker run -d -P nginx

 View port mappings
docker port container_name

 Expose ports in Dockerfile but not map them automatically
 (use -p flag when running to actually map)
```

 8. Data Persistence with Volumes

 8.1. Volume Management
```bash
 Create a named volume
docker volume create my-data

 List volumes
docker volume ls

 Inspect volume
docker volume inspect my-data

 Mount volume to container
docker run -d -v my-data:/data nginx

 Mount with specific options
docker run -d -v my-data:/data:rw nginx   Read-write (default)
docker run -d -v my-data:/data:ro nginx   Read-only

 Remove volume
docker volume rm my-data

 Remove unused volumes
docker volume prune -f

 Backup volume data
docker run --rm -v my-data:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz -C /data .

 Restore volume data
docker run --rm -v my-data:/data -v $(pwd):/backup alpine tar xzf /backup/backup.tar.gz -C /data
```

 8.2. Bind Mounts
```bash
 Mount host directory to container
docker run -d -v /host/path:/container/path nginx

 Mount current directory
docker run -d -v $(pwd):/app python:3.9 python /app/script.py

 Mount with read-only access
docker run -d -v $(pwd):/app:ro python:3.9

 Use --mount syntax (more explicit)
docker run -d --mount type=bind,source=/host/path,target=/container/path nginx

 Use --mount for volumes
docker run -d --mount type=volume,source=my-data,target=/data nginx
```

 9. Docker Compose

 9.1. Docker Compose Fundamentals
```yaml
 docker-compose.yml - Multi-container application
version: '3.8'

services:
  web:
    build:
      context: ./web
      dockerfile: Dockerfile
    ports:
      - "5000:5000"
    environment:
      - FLASK_ENV=production
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    volumes:
      - ./web:/app
      - /app/node_modules   Anonymous volume to preserve node_modules
    depends_on:
      - db
      - redis
    networks:
      - frontend
      - backend
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    restart: unless-stopped

  db:
    image: postgres:13-alpine
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: ${DB_PASSWORD:-defaultpass}
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d mydb"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:6-alpine
    volumes:
      - redis_data:/data
    networks:
      - backend
    command: redis-server --appendonly yes

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./ssl:/etc/nginx/ssl:ro
    depends_on:
      - web
    networks:
      - frontend

volumes:
  postgres_data:
    driver: local
  redis_data:
    driver: local

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true   No external access
```

 9.2. Docker Compose Commands
```bash
 Start all services
docker-compose up

 Start in detached mode
docker-compose up -d

 Build and start
docker-compose up --build

 Start specific service
docker-compose up -d web

 View logs
docker-compose logs

 View logs for specific service
docker-compose logs -f web

 Follow logs (tail)
docker-compose logs -f

 Stop services
docker-compose stop

 Stop and remove containers
docker-compose down

 Stop and remove containers, volumes, and images
docker-compose down -v --rmi all

 Restart services
docker-compose restart

 Scale a service
docker-compose up -d --scale web=3

 Execute command in running container
docker-compose exec web bash

 Run one-off command
docker-compose run --rm web python manage.py migrate

 Validate configuration
docker-compose config

 Pull latest images
docker-compose pull

 Build images
docker-compose build

 Build specific service
docker-compose build web

 Check service health
docker-compose ps

 View resource usage
docker-compose top
```

 10. Container Registries

 10.1. Docker Hub
```bash
 Login to Docker Hub
docker login

 Tag image for Docker Hub
docker tag myapp:latest username/myapp:v1.0

 Push image to Docker Hub
docker push username/myapp:v1.0

 Pull image from Docker Hub
docker pull username/myapp:v1.0

 Logout from Docker Hub
docker logout
```

 10.2. Amazon ECR (Elastic Container Registry)
```bash
 Login to ECR
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-west-2.amazonaws.com

 Create ECR repository
aws ecr create-repository --repository-name myapp --region us-west-2

 Tag image for ECR
docker tag myapp:latest 123456789012.dkr.ecr.us-west-2.amazonaws.com/myapp:v1.0

 Push to ECR
docker push 123456789012.dkr.ecr.us-west-2.amazonaws.com/myapp:v1.0

 Pull from ECR
docker pull 123456789012.dkr.ecr.us-west-2.amazonaws.com/myapp:v1.0

 List ECR repositories
aws ecr describe-repositories --region us-west-2

 List images in repository
aws ecr describe-images --repository-name myapp --region us-west-2
```

 11. Container Orchestration Basics

 11.1. Docker Swarm (Introductory)
```bash
 Initialize Docker Swarm
docker swarm init --advertise-addr 192.168.1.100

 Get join token for workers
docker swarm join-token worker

 Join swarm as worker (run on worker node)
docker swarm join --token <token> 192.168.1.100:2377

 List nodes
docker node ls

 Create service
docker service create --name web --replicas 3 -p 8080:80 nginx

 List services
docker service ls

 Scale service
docker service scale web=5

 Update service
docker service update --image nginx:alpine web

 Remove service
docker service rm web

 Leave swarm
docker swarm leave

 Leave swarm forcefully (manager)
docker swarm leave --force
```

 12. Practical Examples

 12.1. Complete Python Web Application
```dockerfile
 Dockerfile for Flask application
FROM python:3.9-slim

 Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    FLASK_APP=app.py \
    FLASK_ENV=production

 Set work directory
WORKDIR /app

 Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/

 Install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

 Copy project
COPY . .

 Create non-root user
RUN useradd -m appuser && chown -R appuser:appuser /app
USER appuser

 Expose port
EXPOSE 5000

 Health check
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:5000/health || exit 1

 Run application
CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:5000", "app:app"]
```

```python
 app.py - Simple Flask application
from flask import Flask, jsonify
import os

app = Flask(__name__)

@app.route('/')
def hello():
    return jsonify({
        'message': 'Hello from Docker!',
        'environment': os.environ.get('FLASK_ENV', 'development')
    })

@app.route('/health')
def health():
    return jsonify({'status': 'healthy'}), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

```txt
 requirements.txt
Flask==2.3.3
gunicorn==21.2.0
```

```bash
!/bin/bash
 build-and-run.sh - Build and run the application

 Build image
docker build -t myflaskapp:latest .

 Run container
docker run -d \
    --name myflaskapp \
    -p 5000:5000 \
    -e FLASK_ENV=production \
    --restart unless-stopped \
    myflaskapp:latest

 View logs
docker logs -f myflaskapp
```

 12.2. Data Science Environment
```dockerfile
 Dockerfile for data science environment
FROM jupyter/datascience-notebook:latest

USER root

 Install additional packages
RUN apt-get update && apt-get install -y \
    git \
    curl \
    && rm -rf /var/lib/apt/lists/

USER jovyan

 Install Python packages
RUN pip install --no-cache-dir \
    tensorflow \
    pytorch \
    scikit-learn \
    matplotlib \
    seaborn \
    plotly

 Copy notebooks
COPY --chown=jovyan:jovyan notebooks/ /home/jovyan/work/

 Expose Jupyter port
EXPOSE 8888

 Start Jupyter
CMD ["start-notebook.sh", "--NotebookApp.token=''"]
```

```bash
 Run data science container
docker run -d \
    --name jupyter \
    -p 8888:8888 \
    -v $(pwd)/data:/home/jovyan/data \
    -v $(pwd)/notebooks:/home/jovyan/work \
    datascience-env:latest
```

 13. Security Best Practices

 13.1. Container Security
```bash
 Scan image for vulnerabilities
docker scan myapp:latest

 Use Trivy for scanning
trivy image myapp:latest

 Run container with limited capabilities
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE nginx

 Run with read-only root filesystem
docker run --read-only -v /tmp:/tmp nginx

 Run with security options
docker run --security-opt=no-new-privileges:true nginx

 Run with user namespace
docker run --userns=host nginx
```

 13.2. Secrets Management
```bash
 Use Docker secrets (Swarm mode)
echo "my_secret_password" | docker secret create db_password -

 Use environment files (Compose)
 Create .env file
cat > .env << EOF
DB_PASSWORD=supersecretpassword
API_KEY=sk-1234567890
EOF

 Reference in docker-compose.yml
 environment:
   - DB_PASSWORD=${DB_PASSWORD}

 Never commit .env to git
echo ".env" >> .gitignore
```

 14. Debugging and Troubleshooting

 14.1. Container Debugging
```bash
 View container logs
docker logs container_name
docker logs -f container_name   Follow logs
docker logs --tail 100 container_name   Last 100 lines

 Execute command in running container
docker exec -it container_name bash
docker exec -it container_name sh   If bash not available

 View container processes
docker top container_name

 View container resource usage
docker stats

 View container resource usage for specific container
docker stats container_name

 Inspect container details
docker inspect container_name

 Get specific field from inspect
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name

 Copy files to/from container
docker cp container_name:/path/in/container /host/path
docker cp /host/path container_name:/path/in/container

 View container events
docker events --filter container=container_name
```

 14.2. Image Troubleshooting
```bash
 Create container from image without running
docker create --name temp_container myimage:latest

 Export container filesystem
docker export temp_container -o filesystem.tar

 Import filesystem as image
docker import filesystem.tar newimage:latest

 Run with debug shell
docker run --rm -it --entrypoint /bin/sh myimage:latest

 Override entrypoint
docker run --rm -it --entrypoint /bin/bash myimage:latest
```

 15. Production Deployment

 15.1. Production Checklist
```bash
!/bin/bash
 production-deploy.sh - Production deployment script

set -euo pipefail

IMAGE_NAME="myapp"
VERSION=$(git describe --tags --always)
REGISTRY="123456789012.dkr.ecr.us-west-2.amazonaws.com"

echo "Building production image..."
docker build \
    --target production \
    --build-arg BUILD_DATE=$(date -u +%Y-%m-%dT%H:%M:%SZ) \
    --build-arg VCS_REF=$(git rev-parse --short HEAD) \
    --build-arg VERSION=$VERSION \
    -t $IMAGE_NAME:$VERSION \
    -t $IMAGE_NAME:latest \
    -f Dockerfile.prod .

echo "Running security scan..."
docker scan $IMAGE_NAME:$VERSION || true

echo "Testing container..."
docker run --rm -d --name test-$IMAGE_NAME -p 8080:5000 $IMAGE_NAME:$VERSION
sleep 5
curl -f http://localhost:8080/health || (echo "Health check failed!" && exit 1)
docker stop test-$IMAGE_NAME

echo "Pushing to registry..."
docker tag $IMAGE_NAME:$VERSION $REGISTRY/$IMAGE_NAME:$VERSION
docker tag $IMAGE_NAME:latest $REGISTRY/$IMAGE_NAME:latest
docker push $REGISTRY/$IMAGE_NAME:$VERSION
docker push $REGISTRY/$IMAGE_NAME:latest

echo "Deployment complete!"
echo "Image: $REGISTRY/$IMAGE_NAME:$VERSION"
```

 16. Resources and Further Learning

 16.1. Documentation
- [Docker Documentation](https://docs.docker.com/)
- [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Docker Security](https://docs.docker.com/engine/security/)

 16.2. Tools and Utilities
- Docker Desktop: GUI for local development
- Docker Hub: Public image registry
- Amazon ECR: AWS container registry
- Trivy: Container vulnerability scanner
- Portainer: Docker management UI
- ctop: Container monitoring tool

 16.3. Best Practices
- Keep images small and focused
- Use specific version tags (avoid 'latest')
- Never store secrets in images
- Use multi-stage builds for optimization
- Implement health checks
- Run containers as non-root user
- Use .dockerignore to reduce build context
- Scan images for vulnerabilities
- Use volumes for persistent data
- Implement proper logging
- Monitor container resource usage

---

Next Steps: Practice building Docker images, create multi-container applications with Docker Compose, and explore container orchestration with Kubernetes or Docker Swarm.
