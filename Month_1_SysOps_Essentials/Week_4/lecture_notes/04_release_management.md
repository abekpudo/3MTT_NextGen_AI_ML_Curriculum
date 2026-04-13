 Release Management: Safe and Reliable Software Delivery

 1. Introduction
Release management is the process of planning, scheduling, and controlling software builds through different stages and environments. This lecture covers versioning strategies, deployment patterns, rollback procedures, and release automation to ensure stable and reliable software delivery.

 2. Why Release Management Matters
- Risk Reduction: Minimize production issues through controlled deployments
- Reliability: Ensure consistent and predictable releases
- Recovery: Quickly recover from failed deployments
- Coordination: Align teams around release schedules
- Compliance: Meet regulatory and audit requirements
- User Experience: Maintain service availability during updates

 3. Versioning Strategies

 3.1. Semantic Versioning (SemVer)
```
Version Format: MAJOR.MINOR.PATCH

Examples:
- 1.0.0 - Initial stable release
- 1.1.0 - New features, backward compatible
- 1.1.1 - Bug fixes, backward compatible
- 2.0.0 - Breaking changes

Rules:
- MAJOR: Increment for incompatible API changes
- MINOR: Increment for backward-compatible functionality
- PATCH: Increment for backward-compatible bug fixes

Pre-release versions:
- 1.0.0-alpha.1
- 1.0.0-beta.2
- 1.0.0-rc.1 (release candidate)
```

```bash
!/bin/bash
 semver.sh - Semantic versioning utilities

 Get current version from git tag
get_current_version() {
    git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0"
}

 Bump version based on type (major, minor, patch)
bump_version() {
    local version=$1
    local bump_type=$2
    
     Remove 'v' prefix if present
    version=${versionv}
    
     Parse version components
    IFS='.' read -r major minor patch <<< "$version"
    
    case $bump_type in
        major)
            major=$((major + 1))
            minor=0
            patch=0
            ;;
        minor)
            minor=$((minor + 1))
            patch=0
            ;;
        patch)
            patch=$((patch + 1))
            ;;
        )
            echo "Invalid bump type: $bump_type"
            exit 1
            ;;
    esac
    
    echo "v${major}.${minor}.${patch}"
}

 Create new version tag
create_release() {
    local bump_type=$1
    local current_version=$(get_current_version)
    local new_version=$(bump_version "$current_version" "$bump_type")
    
    echo "Current version: $current_version"
    echo "New version: $new_version"
    
     Generate changelog
    git log "${current_version}..HEAD" --pretty=format:"- %s (%h)" > /tmp/changelog.txt
    
     Create annotated tag
    git tag -a "$new_version" -m "Release $new_version

Changes:
$(cat /tmp/changelog.txt)"
    
    echo "Created tag: $new_version"
    echo "Push with: git push origin $new_version"
}

 Usage examples
 create_release patch   1.0.0 -> 1.0.1
 create_release minor   1.0.1 -> 1.1.0
 create_release major   1.1.0 -> 2.0.0
```

 3.2. Calendar Versioning (CalVer)
```
Version Format: YYYY.MM.MICRO

Examples:
- 2024.01.0 - January 2024 release
- 2024.01.1 - January 2024 patch
- 2024.02.0 - February 2024 release

Use Cases:
- Ubuntu: 22.04, 24.04
- Windows: Windows 10, Windows 11
- Time-based release cycles

Benefits:
- Clear release timeline
- Easy to identify support windows
- Predictable upgrade schedules
```

```bash
!/bin/bash
 calver.sh - Calendar versioning

generate_calver() {
    local year=$(date +%Y)
    local month=$(date +%m)
    local micro=${1:-0}
    
    echo "${year}.${month}.${micro}"
}

 Get next micro version
get_next_micro() {
    local current_version=$1
    local base_version=$(echo $current_version | cut -d. -f1,2)
    local current_micro=$(echo $current_version | cut -d. -f3)
    local next_micro=$((current_micro + 1))
    
    echo "${base_version}.${next_micro}"
}

 Usage
echo "Current CalVer: $(generate_calver)"
echo "Next micro: $(get_next_micro "2024.01.5")"
```

 3.3. Git Tagging and Release Branches
```bash
!/bin/bash
 release-workflow.sh - Complete release workflow

set -euo pipefail

 Configuration
RELEASE_BRANCH_PREFIX="release/"
MAIN_BRANCH="main"
DEVELOP_BRANCH="develop"

 Create release branch
create_release_branch() {
    local version=$1
    local release_branch="${RELEASE_BRANCH_PREFIX}${version}"
    
     Ensure we're on develop
    git checkout "$DEVELOP_BRANCH"
    git pull origin "$DEVELOP_BRANCH"
    
     Create release branch
    git checkout -b "$release_branch"
    
    echo "Created release branch: $release_branch"
    echo "Push with: git push -u origin $release_branch"
}

 Finalize release
finalize_release() {
    local version=$1
    local release_branch="${RELEASE_BRANCH_PREFIX}${version}"
    
     Merge to main
    git checkout "$MAIN_BRANCH"
    git pull origin "$MAIN_BRANCH"
    git merge --no-ff "$release_branch" -m "Merge release $version"
    
     Tag release
    git tag -a "v${version}" -m "Release v${version}"
    
     Merge back to develop
    git checkout "$DEVELOP_BRANCH"
    git pull origin "$DEVELOP_BRANCH"
    git merge --no-ff "$release_branch" -m "Merge release $version back to develop"
    
     Delete release branch
    git branch -d "$release_branch"
    
    echo "Release $version finalized"
    echo "Push all changes: git push origin $MAIN_BRANCH $DEVELOP_BRANCH --tags"
}

 Hotfix workflow
create_hotfix() {
    local version=$1
    local hotfix_branch="hotfix/${version}"
    
     Create from main
    git checkout "$MAIN_BRANCH"
    git pull origin "$MAIN_BRANCH"
    git checkout -b "$hotfix_branch"
    
    echo "Created hotfix branch: $hotfix_branch"
    echo "Make your fixes, then run: finalize_hotfix $version"
}

finalize_hotfix() {
    local version=$1
    local hotfix_branch="hotfix/${version}"
    
     Merge to main
    git checkout "$MAIN_BRANCH"
    git merge --no-ff "$hotfix_branch" -m "Hotfix v${version}"
    git tag -a "v${version}" -m "Hotfix v${version}"
    
     Merge to develop
    git checkout "$DEVELOP_BRANCH"
    git merge --no-ff "$hotfix_branch" -m "Merge hotfix v${version}"
    
     Delete hotfix branch
    git branch -d "$hotfix_branch"
    
    echo "Hotfix $version finalized"
}

 Example usage
 create_release_branch "1.2.0"
 finalize_release "1.2.0"
 create_hotfix "1.2.1"
 finalize_hotfix "1.2.1"
```

 4. Deployment Strategies

 4.1. Rolling Deployment
```bash
!/bin/bash
 rolling-deployment.sh - Rolling deployment strategy

 Configuration
NAMESPACE="production"
DEPLOYMENT_NAME="myapp"
REPLICAS=5
ROLLOUT_TIMEOUT="10m"

rolling_deploy() {
    local new_image=$1
    
    echo "Starting rolling deployment..."
    
     Update deployment with new image
    kubectl set image deployment/$DEPLOYMENT_NAME \
        app=$new_image \
        --namespace=$NAMESPACE
    
     Monitor rollout
    kubectl rollout status deployment/$DEPLOYMENT_NAME \
        --namespace=$NAMESPACE \
        --timeout=$ROLLOUT_TIMEOUT
    
    if [ $? -eq 0 ]; then
        echo "Rolling deployment successful!"
        
         Verify deployment
        kubectl get pods --namespace=$NAMESPACE -l app=$DEPLOYMENT_NAME
    else
        echo "Rolling deployment failed! Rolling back..."
        kubectl rollout undo deployment/$DEPLOYMENT_NAME --namespace=$NAMESPACE
        exit 1
    fi
}

 Rollback if needed
rollback() {
    echo "Rolling back deployment..."
    kubectl rollout undo deployment/$DEPLOYMENT_NAME --namespace=$NAMESPACE
    kubectl rollout status deployment/$DEPLOYMENT_NAME --namespace=$NAMESPACE
}

 Usage
 rolling_deploy "myapp:v1.2.0"
 rollback
```

 4.2. Blue-Green Deployment
```yaml
 blue-green-deployment.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  namespace: production
spec:
  selector:
    app: myapp
    version: blue   Change to "green" to switch
  ports:
    - port: 80
      targetPort: 8080
  type: LoadBalancer

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-blue
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
        - name: app
          image: myapp:v1.0.0
          ports:
            - containerPort: 8080

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-green
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
        - name: app
          image: myapp:v1.1.0   New version
          ports:
            - containerPort: 8080
```

```bash
!/bin/bash
 blue-green-deploy.sh - Blue-green deployment automation

set -euo pipefail

NAMESPACE="production"
SERVICE_NAME="myapp-service"
CURRENT_VERSION=$(kubectl get service $SERVICE_NAME -n $NAMESPACE -o jsonpath='{.spec.selector.version}')
NEW_VERSION=""

determine_new_version() {
    if [ "$CURRENT_VERSION" == "blue" ]; then
        NEW_VERSION="green"
    else
        NEW_VERSION="blue"
    fi
}

health_check() {
    local version=$1
    local deployment_name="myapp-${version}"
    
    echo "Checking health of $deployment_name..."
    
     Wait for deployment to be ready
    kubectl wait --for=condition=available --timeout=300s \
        deployment/$deployment_name -n $NAMESPACE
    
     Run health checks
    kubectl get pods -n $NAMESPACE -l "app=myapp,version=$version"
    
     Port forward and test
    kubectl port-forward deployment/$deployment_name 8080:8080 -n $NAMESPACE &
    local pid=$!
    sleep 5
    
     Test health endpoint
    if curl -f http://localhost:8080/health; then
        echo "Health check passed!"
        kill $pid 2>/dev/null || true
        return 0
    else
        echo "Health check failed!"
        kill $pid 2>/dev/null || true
        return 1
    fi
}

switch_traffic() {
    echo "Switching traffic from $CURRENT_VERSION to $NEW_VERSION..."
    
    kubectl patch service $SERVICE_NAME -n $NAMESPACE --type='json' \
        -p='[{"op": "replace", "path": "/spec/selector/version", "value": "'$NEW_VERSION'"}]'
    
    echo "Traffic switched successfully!"
}

deploy_blue_green() {
    local new_image=$1
    
    determine_new_version
    
    echo "Current version: $CURRENT_VERSION"
    echo "Deploying to: $NEW_VERSION"
    
     Update inactive environment
    local deployment_name="myapp-${NEW_VERSION}"
    kubectl set image deployment/$deployment_name app=$new_image -n $NAMESPACE
    
     Wait for rollout
    kubectl rollout status deployment/$deployment_name -n $NAMESPACE --timeout=300s
    
     Health check
    if health_check $NEW_VERSION; then
        switch_traffic
        echo "Deployment successful!"
    else
        echo "Health check failed! Aborting deployment."
        exit 1
    fi
}

rollback() {
    echo "Rolling back to $CURRENT_VERSION..."
    kubectl patch service $SERVICE_NAME -n $NAMESPACE --type='json' \
        -p='[{"op": "replace", "path": "/spec/selector/version", "value": "'$CURRENT_VERSION'"}]'
    echo "Rollback complete!"
}

 Usage
 deploy_blue_green "myapp:v1.2.0"
 rollback
```

 4.3. Canary Deployment
```yaml
 canary-deployment.yaml
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: myapp
  namespace: production
spec:
  provider: nginx
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  service:
    port: 80
    targetPort: 8080
  analysis:
    interval: 1m
    threshold: 5
    maxWeight: 50
    stepWeight: 10
    metrics:
      - name: request-success-rate
        thresholdRange:
          min: 99
        interval: 1m
      - name: request-duration
        thresholdRange:
          max: 500
        interval: 1m
    webhooks:
      - name: load-test
        url: http://flagger-loadtester.test/
        timeout: 5s
        metadata:
          cmd: "hey -z 1m -q 10 -c 2 http://myapp-canary/"
```

```bash
!/bin/bash
 canary-deploy.sh - Manual canary deployment

NAMESPACE="production"
DEPLOYMENT_NAME="myapp"
CANARY_PERCENTAGE=10

 Create canary deployment
create_canary() {
    local new_image=$1
    local canary_percentage=$2
    
    echo "Creating canary deployment with $canary_percentage% traffic..."
    
     Calculate replicas
    local total_replicas=$(kubectl get deployment $DEPLOYMENT_NAME -n $NAMESPACE -o jsonpath='{.spec.replicas}')
    local canary_replicas=$((total_replicas  canary_percentage / 100))
    
    if [ $canary_replicas -lt 1 ]; then
        canary_replicas=1
    fi
    
     Create canary deployment
    cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ${DEPLOYMENT_NAME}-canary
  namespace: $NAMESPACE
spec:
  replicas: $canary_replicas
  selector:
    matchLabels:
      app: $DEPLOYMENT_NAME
      version: canary
  template:
    metadata:
      labels:
        app: $DEPLOYMENT_NAME
        version: canary
    spec:
      containers:
        - name: app
          image: $new_image
          ports:
            - containerPort: 8080
EOF
    
     Update service to include canary
    kubectl patch service $DEPLOYMENT_NAME -n $NAMESPACE --type='merge' \
        -p='{"spec":{"selector":null}}'
    
    echo "Canary deployment created with $canary_replicas replicas"
}

 Promote canary to full deployment
promote_canary() {
    echo "Promoting canary to full deployment..."
    
     Update main deployment image
    local canary_image=$(kubectl get deployment ${DEPLOYMENT_NAME}-canary -n $NAMESPACE -o jsonpath='{.spec.template.spec.containers[0].image}')
    kubectl set image deployment/$DEPLOYMENT_NAME app=$canary_image -n $NAMESPACE
    
     Wait for rollout
    kubectl rollout status deployment/$DEPLOYMENT_NAME -n $NAMESPACE
    
     Remove canary
    kubectl delete deployment ${DEPLOYMENT_NAME}-canary -n $NAMESPACE
    
    echo "Canary promoted successfully!"
}

 Abort canary deployment
abort_canary() {
    echo "Aborting canary deployment..."
    kubectl delete deployment ${DEPLOYMENT_NAME}-canary -n $NAMESPACE
    echo "Canary deployment removed!"
}

 Monitor canary metrics
monitor_canary() {
    echo "Monitoring canary deployment..."
    
     Check error rate
    local error_rate=$(kubectl exec -it deployment/prometheus -n monitoring -- \
        curl -s 'http://localhost:9090/api/v1/query?query=rate(http_requests_total{status=~"5..",version="canary"}[5m])')
    
     Check latency
    local latency=$(kubectl exec -it deployment/prometheus -n monitoring -- \
        curl -s 'http://localhost:9090/api/v1/query?query=histogram_quantile(0.95,rate(http_request_duration_seconds_bucket{version="canary"}[5m]))')
    
    echo "Canary Error Rate: $error_rate"
    echo "Canary P95 Latency: $latency"
}

 Usage
 create_canary "myapp:v1.2.0" 10
 monitor_canary
 promote_canary   If metrics look good
 abort_canary     If metrics look bad
```

 5. Feature Flags and Gradual Rollouts

 5.1. Feature Flag Implementation
```python
 feature_flags.py - Feature flag management
import os
import json
from typing import Dict, Any
import redis

class FeatureFlagManager:
    def __init__(self, redis_client=None):
        self.redis = redis_client or redis.Redis(host='localhost', port=6379, db=0)
        self.local_flags = self._load_local_flags()
    
    def _load_local_flags(self) -> Dict[str, Any]:
        """Load flags from environment or config file"""
        flags = {}
        
         Load from environment variables
        for key, value in os.environ.items():
            if key.startswith('FEATURE_'):
                flag_name = key[8:].lower()
                flags[flag_name] = value.lower() == 'true'
        
        return flags
    
    def is_enabled(self, flag_name: str, user_id: str = None) -> bool:
        """Check if feature flag is enabled"""
        
         Check local environment first
        if flag_name in self.local_flags:
            return self.local_flags[flag_name]
        
         Check Redis for dynamic flags
        flag_data = self.redis.get(f"flag:{flag_name}")
        if flag_data:
            flag_config = json.loads(flag_data)
            
             Check percentage rollout
            if 'percentage' in flag_config:
                if user_id:
                     Consistent hashing for user-based rollout
                    import hashlib
                    user_hash = int(hashlib.md5(user_id.encode()).hexdigest(), 16)
                    return (user_hash % 100) < flag_config['percentage']
                return False
            
            return flag_config.get('enabled', False)
        
        return False
    
    def set_flag(self, flag_name: str, enabled: bool, percentage: int = None):
        """Set feature flag configuration"""
        config = {'enabled': enabled}
        if percentage is not None:
            config['percentage'] = percentage
        
        self.redis.set(f"flag:{flag_name}", json.dumps(config))
    
    def get_all_flags(self) -> Dict[str, Any]:
        """Get all feature flags"""
        flags = self.local_flags.copy()
        
         Get dynamic flags from Redis
        for key in self.redis.scan_iter(match="flag:"):
            flag_name = key.decode().split(':')[1]
            flag_data = json.loads(self.redis.get(key))
            flags[flag_name] = flag_data
        
        return flags

 Usage in Flask application
from flask import Flask, g

app = Flask(__name__)
feature_flags = FeatureFlagManager()

@app.before_request
def load_feature_flags():
    g.feature_flags = feature_flags

@app.route('/api/new-feature')
def new_feature():
    if not g.feature_flags.is_enabled('new_api', g.user_id):
        return {'error': 'Feature not available'}, 403
    
    return {'data': 'New feature data'}

 Admin endpoints for feature flag management
@app.route('/admin/flags/<flag_name>', methods=['POST'])
def set_flag(flag_name):
    data = request.json
    feature_flags.set_flag(
        flag_name,
        data.get('enabled', False),
        data.get('percentage')
    )
    return {'status': 'updated'}

@app.route('/admin/flags')
def list_flags():
    return feature_flags.get_all_flags()
```

```bash
!/bin/bash
 feature-flag-cli.sh - Feature flag management CLI

REDIS_HOST=${REDIS_HOST:-localhost}
REDIS_PORT=${REDIS_PORT:-6379}

enable_flag() {
    local flag_name=$1
    local percentage=${2:-100}
    
    redis-cli -h $REDIS_HOST -p $REDIS_PORT SET "flag:$flag_name" \
        "{\"enabled\": true, \"percentage\": $percentage}"
    
    echo "Enabled flag '$flag_name' with $percentage% rollout"
}

disable_flag() {
    local flag_name=$1
    
    redis-cli -h $REDIS_HOST -p $REDIS_PORT SET "flag:$flag_name" \
        "{\"enabled\": false}"
    
    echo "Disabled flag '$flag_name'"
}

list_flags() {
    echo "Feature Flags:"
    redis-cli -h $REDIS_HOST -p $REDIS_PORT KEYS "flag:" | while read key; do
        flag_name=$(echo $key | sed 's/flag://')
        value=$(redis-cli -h $REDIS_HOST -p $REDIS_PORT GET "$key")
        echo "  $flag_name: $value"
    done
}

 Usage
 enable_flag "dark_mode" 50
 disable_flag "dark_mode"
 list_flags
```

 6. Rollback Strategies

 6.1. Automated Rollback
```bash
!/bin/bash
 automated-rollback.sh - Automated rollback system

NAMESPACE="production"
DEPLOYMENT_NAME="myapp"
HEALTH_CHECK_URL="http://myapp/health"
ROLLBACK_THRESHOLD=5

 Health check function
health_check() {
    local attempts=0
    local max_attempts=10
    
    while [ $attempts -lt $max_attempts ]; do
        if curl -f -s "$HEALTH_CHECK_URL" > /dev/null; then
            echo "Health check passed"
            return 0
        fi
        
        attempts=$((attempts + 1))
        echo "Health check attempt $attempts failed, retrying..."
        sleep 10
    done
    
    echo "Health check failed after $max_attempts attempts"
    return 1
}

 Monitor deployment metrics
monitor_metrics() {
    local duration=${1:-300}   5 minutes default
    local interval=${2:-30}    30 seconds
    local elapsed=0
    local error_count=0
    
    echo "Monitoring deployment for $duration seconds..."
    
    while [ $elapsed -lt $duration ]; do
         Check error rate
        local error_rate=$(curl -s "http://prometheus:9090/api/v1/query?query=rate(http_requests_total{status=~\"5..\"}[1m])" | \
            jq -r '.data.result[0].value[1] // 0')
        
        echo "Error rate: $error_rate"
        
         Check if error rate exceeds threshold
        if (( $(echo "$error_rate > 0.05" | bc -l) )); then
            error_count=$((error_count + 1))
            echo "High error rate detected (count: $error_count)"
            
            if [ $error_count -ge $ROLLBACK_THRESHOLD ]; then
                echo "Error threshold exceeded! Triggering rollback..."
                return 1
            fi
        else
            error_count=0
        fi
        
        sleep $interval
        elapsed=$((elapsed + interval))
    done
    
    echo "Monitoring completed successfully"
    return 0
}

 Rollback deployment
rollback_deployment() {
    echo "Initiating rollback..."
    
     Get previous revision
    local previous_revision=$(kubectl rollout history deployment/$DEPLOYMENT_NAME -n $NAMESPACE | \
        tail -n 2 | head -n 1 | awk '{print $1}')
    
    echo "Rolling back to revision $previous_revision"
    
     Perform rollback
    kubectl rollout undo deployment/$DEPLOYMENT_NAME -n $NAMESPACE --to-revision=$previous_revision
    
     Wait for rollback to complete
    kubectl rollout status deployment/$DEPLOYMENT_NAME -n $NAMESPACE --timeout=300s
    
     Verify rollback
    if health_check; then
        echo "Rollback successful!"
        
         Notify team
        send_notification "Rollback completed for $DEPLOYMENT_NAME"
    else
        echo "Rollback failed! Manual intervention required."
        exit 1
    fi
}

 Deploy with automatic rollback
deploy_with_rollback() {
    local new_image=$1
    
    echo "Deploying $new_image with automatic rollback protection..."
    
     Record current state for potential rollback
    kubectl get deployment $DEPLOYMENT_NAME -n $NAMESPACE -o yaml > /tmp/pre-deployment-state.yaml
    
     Perform deployment
    kubectl set image deployment/$DEPLOYMENT_NAME app=$new_image -n $NAMESPACE
    
     Wait for initial rollout
    if ! kubectl rollout status deployment/$DEPLOYMENT_NAME -n $NAMESPACE --timeout=300s; then
        echo "Initial rollout failed! Rolling back..."
        rollback_deployment
        exit 1
    fi
    
     Health check
    if ! health_check; then
        echo "Health check failed! Rolling back..."
        rollback_deployment
        exit 1
    fi
    
     Monitor metrics
    if ! monitor_metrics; then
        echo "Metrics monitoring failed! Rolling back..."
        rollback_deployment
        exit 1
    fi
    
    echo "Deployment successful!"
}

 Send notification
send_notification() {
    local message=$1
    
     Slack notification
    curl -X POST -H 'Content-type: application/json' \
        --data "{\"text\":\"$message\"}" \
        "$SLACK_WEBHOOK_URL" || true
}

 Usage
 deploy_with_rollback "myapp:v1.2.0"
```

 6.2. Database Migration Rollback
```bash
!/bin/bash
 db-rollback.sh - Database migration rollback

 Configuration
DB_HOST=${DB_HOST:-localhost}
DB_NAME=${DB_NAME:-myapp}
DB_USER=${DB_USER:-admin}
MIGRATION_DIR="./migrations"
BACKUP_DIR="./backups"

 Create backup before migration
backup_database() {
    local backup_file="$BACKUP_DIR/backup_$(date +%Y%m%d_%H%M%S).sql"
    
    echo "Creating database backup: $backup_file"
    mysqldump -h $DB_HOST -u $DB_USER -p $DB_NAME > "$backup_file"
    
    echo "Backup created: $backup_file"
    echo "$backup_file" > /tmp/last_backup.txt
}

 Get last backup
get_last_backup() {
    if [ -f /tmp/last_backup.txt ]; then
        cat /tmp/last_backup.txt
    else
        ls -t $BACKUP_DIR/.sql | head -n 1
    fi
}

 Rollback database
rollback_database() {
    local backup_file=$(get_last_backup)
    
    if [ -z "$backup_file" ] || [ ! -f "$backup_file" ]; then
        echo "No backup file found!"
        exit 1
    fi
    
    echo "Rolling back database using: $backup_file"
    
     Confirm rollback
    read -p "Are you sure you want to rollback? This will destroy recent data! (yes/no): " confirm
    if [ "$confirm" != "yes" ]; then
        echo "Rollback cancelled"
        exit 0
    fi
    
     Restore from backup
    mysql -h $DB_HOST -u $DB_USER -p $DB_NAME < "$backup_file"
    
    echo "Database rollback completed!"
}

 Reverse migrations (if using migration framework)
reverse_migrations() {
    local steps=${1:-1}
    
    echo "Reversing last $steps migration(s)..."
    
     Alembic (Python)
     alembic downgrade -$steps
    
     Flyway (Java)
     flyway undo
    
     Liquibase
     liquibase rollbackCount $steps
    
    echo "Migrations reversed!"
}

 Usage
 backup_database
 rollback_database
 reverse_migrations 3
```

 7. Release Automation

 7.1. Complete Release Pipeline
```yaml
 .github/workflows/release.yml
name: Release Pipeline

on:
  push:
    branches: [main]
    tags: ['v']

jobs:
  prepare-release:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.version.outputs.version }}
      release_type: ${{ steps.version.outputs.type }}
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Determine version
        id: version
        run: |
          if [[ $GITHUB_REF == refs/tags/v ]]; then
            VERSION=${GITHUB_REFrefs/tags/}
            echo "version=$VERSION" >> $GITHUB_OUTPUT
            echo "type=production" >> $GITHUB_OUTPUT
          else
            VERSION="v$(date +%Y%m%d)-$(git rev-parse --short HEAD)"
            echo "version=$VERSION" >> $GITHUB_OUTPUT
            echo "type=staging" >> $GITHUB_OUTPUT
          fi

  build-and-test:
    needs: prepare-release
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build application
        run: |
          npm ci
          npm run build
      
      - name: Run tests
        run: npm test
      
      - name: Upload artifacts
        uses: actions/upload-artifact@v4
        with:
          name: build-${{ needs.prepare-release.outputs.version }}
          path: ./dist

  deploy-staging:
    needs: [prepare-release, build-and-test]
    runs-on: ubuntu-latest
    if: needs.prepare-release.outputs.release_type == 'staging'
    environment: staging
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v4
        with:
          name: build-${{ needs.prepare-release.outputs.version }}
          path: ./dist
      
      - name: Deploy to staging
        run: |
          echo "Deploying ${{ needs.prepare-release.outputs.version }} to staging"
           Deploy commands here

  deploy-production:
    needs: [prepare-release, build-and-test]
    runs-on: ubuntu-latest
    if: needs.prepare-release.outputs.release_type == 'production'
    environment:
      name: production
      url: https://myapp.com
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v4
        with:
          name: build-${{ needs.prepare-release.outputs.version }}
          path: ./dist
      
      - name: Create GitHub Release
        uses: softprops/action-gh-release@v1
        with:
          tag_name: ${{ needs.prepare-release.outputs.version }}
          generate_release_notes: true
      
      - name: Deploy to production
        run: |
          echo "Deploying ${{ needs.prepare-release.outputs.version }} to production"
           Deploy commands here
      
      - name: Notify team
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          channel: 'releases'
          text: 'Release ${{ needs.prepare-release.outputs.version }} deployed to production!'
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

```bash
!/bin/bash
 release-automation.sh - Complete release automation

set -euo pipefail

 Configuration
REPO_URL="git@github.com:company/myapp.git"
STAGING_ENV="staging"
PROD_ENV="production"
SLACK_WEBHOOK="${SLACK_WEBHOOK_URL}"

 Functions
notify() {
    local message=$1
    echo "$message"
    
    if [ -n "$SLACK_WEBHOOK" ]; then
        curl -X POST -H 'Content-type: application/json' \
            --data "{\"text\":\"$message\"}" \
            "$SLACK_WEBHOOK" || true
    fi
}

run_tests() {
    echo "Running test suite..."
    npm test
    echo "Tests passed!"
}

build_artifacts() {
    local version=$1
    
    echo "Building artifacts for version $version..."
    
     Build application
    npm run build
    
     Create Docker image
    docker build -t "myapp:$version" .
    docker tag "myapp:$version" "myapp:latest"
    
     Package artifacts
    tar -czf "myapp-$version.tar.gz" ./dist/
    
    echo "Build complete!"
}

deploy_to_staging() {
    local version=$1
    
    echo "Deploying $version to staging..."
    
     Deploy using blue-green strategy
    ./scripts/blue-green-deploy.sh "myapp:$version"
    
     Run smoke tests
    ./scripts/smoke-tests.sh "https://staging.myapp.com"
    
    notify "✅ Staging deployment complete for $version"
}

deploy_to_production() {
    local version=$1
    
    echo "Deploying $version to production..."
    
     Confirm deployment
    read -p "Deploy $version to production? (yes/no): " confirm
    if [ "$confirm" != "yes" ]; then
        echo "Deployment cancelled"
        exit 0
    fi
    
     Deploy using canary strategy
    ./scripts/canary-deploy.sh "myapp:$version" 10
    
     Monitor for 30 minutes
    echo "Monitoring deployment for 30 minutes..."
    sleep 1800
    
     Promote to full deployment
    ./scripts/promote-canary.sh
    
    notify "🚀 Production deployment complete for $version"
}

 Main release flow
main() {
    local version=$1
    local environment=${2:-staging}
    
    echo "Starting release process for $version..."
    
     Run tests
    run_tests
    
     Build artifacts
    build_artifacts "$version"
    
     Deploy based on environment
    case $environment in
        staging)
            deploy_to_staging "$version"
            ;;
        production)
            deploy_to_production "$version"
            ;;
        )
            echo "Unknown environment: $environment"
            exit 1
            ;;
    esac
    
    echo "Release process completed!"
}

 Usage
 main "v1.2.0" staging
 main "v1.2.0" production
```

 8. Release Checklists and Documentation

 8.1. Pre-Release Checklist
```markdown
 Pre-Release Checklist

 Code Quality
- [ ] All tests passing
- [ ] Code review completed
- [ ] Static analysis clean (linting, security scans)
- [ ] Documentation updated

 Testing
- [ ] Unit tests passing
- [ ] Integration tests passing
- [ ] End-to-end tests passing
- [ ] Performance tests acceptable
- [ ] Security scan clean

 Staging Validation
- [ ] Deployed to staging environment
- [ ] Smoke tests passing
- [ ] Feature flags configured
- [ ] Database migrations tested

 Release Preparation
- [ ] Version number updated
- [ ] Changelog updated
- [ ] Release notes drafted
- [ ] Rollback plan documented

 Infrastructure
- [ ] Monitoring dashboards reviewed
- [ ] Alerting rules verified
- [ ] Capacity planning confirmed
- [ ] Backup systems tested

 Communication
- [ ] Stakeholders notified
- [ ] Support team briefed
- [ ] Documentation team informed
- [ ] Marketing team notified (if applicable)
```

 8.2. Post-Release Validation
```bash
!/bin/bash
 post-release-validation.sh - Validate production release

set -euo pipefail

APP_URL="https://myapp.com"
HEALTH_ENDPOINT="/health"
METRICS_ENDPOINT="/metrics"

 Health checks
echo "=== Health Checks ==="

 Basic connectivity
if curl -f -s "${APP_URL}${HEALTH_ENDPOINT}" > /dev/null; then
    echo "✅ Application is responding"
else
    echo "❌ Application health check failed"
    exit 1
fi

 Check critical endpoints
endpoints=("/api/users" "/api/orders" "/api/products")
for endpoint in "${endpoints[@]}"; do
    if curl -f -s "${APP_URL}${endpoint}" > /dev/null 2>&1; then
        echo "✅ $endpoint is accessible"
    else
        echo "❌ $endpoint is not accessible"
    fi
done

 Metrics validation
echo ""
echo "=== Metrics Validation ==="

 Check error rate
error_rate=$(curl -s "http://prometheus:9090/api/v1/query?query=rate(http_requests_total{status=~\"5..\"}[5m])" | \
    jq -r '.data.result[0].value[1] // 0')
echo "Error rate: $error_rate%"

if (( $(echo "$error_rate > 1.0" | bc -l) )); then
    echo "❌ Error rate is too high!"
    exit 1
else
    echo "✅ Error rate is acceptable"
fi

 Check response time
response_time=$(curl -s "http://prometheus:9090/api/v1/query?query=histogram_quantile(0.95,rate(http_request_duration_seconds_bucket[5m]))" | \
    jq -r '.data.result[0].value[1] // 0')
echo "P95 response time: ${response_time}s"

if (( $(echo "$response_time > 1.0" | bc -l) )); then
    echo "⚠️ Response time is high"
else
    echo "✅ Response time is acceptable"
fi

 Resource utilization
echo ""
echo "=== Resource Utilization ==="

 CPU usage
cpu_usage=$(kubectl top pods -l app=myapp --no-headers | awk '{sum+=$2} END {print sum/NR}')
echo "Average CPU usage: ${cpu_usage}%"

 Memory usage
memory_usage=$(kubectl top pods -l app=myapp --no-headers | awk '{sum+=$3} END {print sum/NR}')
echo "Average memory usage: ${memory_usage}Mi"

echo ""
echo "=== Validation Complete ==="
echo "Release appears to be healthy!"
```

 9. Resources and Further Learning

 9.1. Documentation
- [Kubernetes Deployment Strategies](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Feature Flags Best Practices](https://docs.launchdarkly.com/guides/best-practices/)
- [Semantic Versioning Specification](https://semver.org/)
- [GitLab Release Management](https://docs.gitlab.com/ee/ci/review_apps/)

 9.2. Tools and Utilities
- ArgoCD: GitOps continuous delivery
- Flagger: Progressive delivery operator
- LaunchDarkly: Feature management platform
- GitVersion: Semantic versioning for Git
- Helm: Kubernetes package manager
- Spinnaker: Multi-cloud continuous delivery

 9.3. Best Practices Summary
- Use semantic versioning for clear release identification
- Implement multiple deployment strategies for different risk levels
- Always have a tested rollback plan
- Use feature flags for gradual rollouts
- Automate as much of the release process as possible
- Maintain comprehensive release checklists
- Monitor deployments with automated health checks
- Document all release procedures
- Communicate releases to all stakeholders
- Practice rollbacks regularly

---

Next Steps: Implement these release management practices in your projects, set up automated deployment pipelines, and establish clear rollback procedures for safe and reliable software delivery.
