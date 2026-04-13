 Shell Automation: Scheduling and Workflow Orchestration

 1. Introduction
Shell automation transforms manual, repetitive tasks into reliable, scheduled workflows. This lecture covers cron jobs, systemd timers, automation patterns, error handling, and building robust automated systems for production environments.

 2. Why Automation Matters
- Consistency: Eliminate human error and variability
- Efficiency: Free up time for high-value work
- Reliability: Ensure tasks run exactly when needed
- Scalability: Handle growing workloads automatically
- Compliance: Maintain audit trails and documentation

 3. Cron Jobs

 3.1. Cron Syntax and Basics
```bash
 Cron format: minute hour day month day-of-week command
  = any value
 , = value list
 - = range
 / = step values

 Examples
     command               Every minute
0     command               Every hour at minute 0
0 2    command               Every day at 2:00 AM
0 2   1 command               Every Monday at 2:00 AM
0 2 1   command               First day of month at 2:00 AM
0 2 1 1  command               January 1st at 2:00 AM
/15     command            Every 15 minutes
0 /2    command             Every 2 hours
0 8-18   1-5 command          Weekdays 8 AM to 6 PM
```

 3.2. Cron Job Management
```bash
 Edit user crontab
crontab -e                       Edit current user's crontab
crontab -l                       List current user's crontab
crontab -r                       Remove current user's crontab

 Edit system crontab
sudo crontab -e                  Edit root crontab
sudo crontab -u username -e      Edit specific user's crontab

 Cron job files
/etc/crontab                     System-wide crontab
/etc/cron.d/                     Additional cron job files
/etc/cron.hourly/                Hourly scripts
/etc/cron.daily/                 Daily scripts
/etc/cron.weekly/                Weekly scripts
/etc/cron.monthly/               Monthly scripts
```

 3.3. Practical Cron Examples
```bash
 Backup script daily at 2 AM
0 2    /home/user/scripts/backup.sh >> /var/log/backup.log 2>&1

 System update weekly on Sunday at 3 AM
0 3   0 /usr/bin/apt update && /usr/bin/apt upgrade -y >> /var/log/system_update.log 2>&1

 Log cleanup daily at midnight
0 0    find /var/log -name ".log" -mtime +30 -delete

 Database backup every 6 hours
0 /6    /home/user/scripts/db_backup.sh

 Health check every 5 minutes
/5     /home/user/scripts/health_check.sh

 Report generation monthly on first day
0 6 1   /home/user/scripts/monthly_report.sh
```

 3.4. Cron Job Best Practices
```bash
!/bin/bash
 backup.sh - Robust backup script for cron

 Lock file to prevent overlapping runs
LOCKFILE="/tmp/backup.lock"
if [ -f "$LOCKFILE" ]; then
    echo "Backup already running" >> /var/log/backup.log
    exit 1
fi
trap "rm -f $LOCKFILE" EXIT
touch "$LOCKFILE"

 Logging
LOG_FILE="/var/log/backup.log"
exec > >(tee -a "$LOG_FILE")
exec 2>&1

 Environment setup
export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
export HOME="/root"

 Main backup logic
echo "Backup started at $(date)"

 Backup commands here
tar -czf "/backup/system_$(date +%Y%m%d_%H%M%S).tar.gz" /important/data

echo "Backup completed at $(date)"
```

 4. Systemd Timers

 4.1. Systemd Timer Basics
```bash
 Create timer file
sudo nano /etc/systemd/system/backup.timer
```

```ini
[Unit]
Description=Run backup script daily
Requires=backup.service

[Timer]
OnCalendar=daily
Persistent=true
AccuracySec=1h

[Install]
WantedBy=timers.target
```

```bash
 Create service file
sudo nano /etc/systemd/system/backup.service
```

```ini
[Unit]
Description=Backup service

[Service]
Type=oneshot
ExecStart=/home/user/scripts/backup.sh
User=root
Group=root
```

 4.2. Advanced Timer Configurations
```ini
 Multiple schedules
[Timer]
OnCalendar=-- 02:00:00         Daily at 2 AM
OnCalendar=Mon..Fri -- 12:00:00  Weekdays at noon
OnCalendar=monthly                Monthly
Persistent=true

 Complex scheduling
[Timer]
OnCalendar=Mon,Tue,Wed,Thu,Fri -- 02:00:00   Weekdays at 2 AM
OnCalendar=Sat,Sun -- 06:00:00              Weekends at 6 AM
RandomizedDelaySec=300                       Random delay up to 5 minutes

 Boot-based scheduling
[Timer]
OnBootSec=1h                     1 hour after boot
OnUnitActiveSec=24h              24 hours after last run
```

 4.3. Timer Management
```bash
 Enable and start timer
sudo systemctl enable backup.timer
sudo systemctl start backup.timer

 Timer status
systemctl list-timers --all
systemctl status backup.timer

 View timer logs
journalctl -u backup.timer
journalctl -u backup.service

 Manual trigger
sudo systemctl start backup.service

 Timer operations
sudo systemctl daemon-reload       Reload timer configuration
sudo systemctl restart backup.timer
```

 5. Automation Workflows

 5.1. Sequential Task Automation
```bash
!/bin/bash
 workflow_sequential.sh - Sequential automation workflow

set -euo pipefail

 Configuration
LOG_FILE="/var/log/workflow.log"
WORKFLOW_DIR="/opt/workflows"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

 Logging function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

 Step 1: Data collection
collect_data() {
    log "Step 1: Starting data collection"
    
     Collect data from various sources
    python3 "$WORKFLOW_DIR/collect_data.py" >> "$LOG_FILE" 2>&1
    
    if [ $? -eq 0 ]; then
        log "Step 1: Data collection completed successfully"
        return 0
    else
        log "Step 1: Data collection failed"
        return 1
    fi
}

 Step 2: Data processing
process_data() {
    log "Step 2: Starting data processing"
    
     Process collected data
    python3 "$WORKFLOW_DIR/process_data.py" >> "$LOG_FILE" 2>&1
    
    if [ $? -eq 0 ]; then
        log "Step 2: Data processing completed successfully"
        return 0
    else
        log "Step 2: Data processing failed"
        return 1
    fi
}

 Step 3: Generate reports
generate_reports() {
    log "Step 3: Starting report generation"
    
     Generate reports
    python3 "$WORKFLOW_DIR/generate_reports.py" >> "$LOG_FILE" 2>&1
    
    if [ $? -eq 0 ]; then
        log "Step 3: Report generation completed successfully"
        return 0
    else
        log "Step 3: Report generation failed"
        return 1
    fi
}

 Step 4: Send notifications
send_notifications() {
    log "Step 4: Sending notifications"
    
     Send email notifications
    if command -v mail >/dev/null 2>&1; then
        echo "Workflow completed successfully at $(date)" | mail -s "Workflow Report" admin@company.com
    fi
    
    log "Step 4: Notifications sent"
    return 0
}

 Main workflow
main() {
    log "Starting workflow execution"
    
     Execute steps sequentially
    if collect_data && process_data && generate_reports; then
        send_notifications
        log "Workflow completed successfully"
        exit 0
    else
        log "Workflow failed"
        exit 1
    fi
}

main "$@"
```

 5.2. Parallel Task Automation
```bash
!/bin/bash
 workflow_parallel.sh - Parallel automation workflow

set -euo pipefail

 Configuration
LOG_FILE="/var/log/workflow_parallel.log"
MAX_PARALLEL_JOBS=4
JOB_DIR="/tmp/jobs"

 Logging function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

 Job function
process_job() {
    local job_id="$1"
    local job_data="$2"
    
    log "Processing job $job_id with data: $job_data"
    
     Simulate job processing
    sleep $((RANDOM % 10 + 5))
    
     Random success/failure for demonstration
    if [ $((RANDOM % 10)) -gt 2 ]; then
        log "Job $job_id completed successfully"
        return 0
    else
        log "Job $job_id failed"
        return 1
    fi
}

 Wait for jobs to complete
wait_for_jobs() {
    local job_count="$1"
    local completed=0
    local failed=0
    
    while [ $completed -lt $job_count ]; do
        sleep 1
        
         Check completed jobs
        for job_file in "$JOB_DIR"/job_; do
            if [ -f "$job_file" ]; then
                local job_id=$(basename "$job_file" | cut -d'_' -f2)
                local status=$(cat "$job_file")
                
                case "$status" in
                    "COMPLETED")
                        ((completed++))
                        rm -f "$job_file"
                        log "Job $job_id marked as completed ($completed/$job_count)"
                        ;;
                    "FAILED")
                        ((failed++))
                        ((completed++))
                        rm -f "$job_file"
                        log "Job $job_id marked as failed ($completed/$job_count)"
                        ;;
                esac
            fi
        done
    done
    
    log "All jobs completed. Success: $((job_count - failed)), Failed: $failed"
}

 Main execution
main() {
    log "Starting parallel workflow"
    
     Create job directory
    mkdir -p "$JOB_DIR"
    
     Define jobs
    declare -a jobs=("job1_data" "job2_data" "job3_data" "job4_data" "job5_data" "job6_data" "job7_data" "job8_data")
    
     Start jobs in parallel with limit
    local active_jobs=0
    local job_id=1
    
    for job_data in "${jobs[@]}"; do
         Wait for slot if max parallel jobs reached
        while [ $active_jobs -ge $MAX_PARALLEL_JOBS ]; do
            sleep 1
             Count active jobs
            active_jobs=$(ls "$JOB_DIR"/job_ 2>/dev/null | wc -l)
        done
        
         Start job in background
        process_job "$job_id" "$job_data" &
        
         Create job status file
        echo "RUNNING" > "$JOB_DIR/job_$job_id"
        
        ((active_jobs++))
        ((job_id++))
        
        log "Started job $((job_id - 1)), active jobs: $active_jobs"
    done
    
     Wait for all jobs to complete
    wait_for_jobs "${jobs[@]}"
    
     Cleanup
    rm -rf "$JOB_DIR"
    
    log "Parallel workflow completed"
}

main "$@"
```

 6. Error Handling and Recovery

 6.1. Robust Error Handling
```bash
!/bin/bash
 robust_automation.sh - Automation with comprehensive error handling

set -euo pipefail

 Configuration
LOG_FILE="/var/log/robust_automation.log"
ERROR_LOG="/var/log/robust_automation_errors.log"
MAX_RETRIES=3
RETRY_DELAY=30

 Logging functions
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

error_log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] ERROR: $1" | tee -a "$ERROR_LOG"
}

 Retry mechanism
retry_command() {
    local command="$1"
    local max_retries="${2:-$MAX_RETRIES}"
    local retry_delay="${3:-$RETRY_DELAY}"
    local attempt=1
    
    while [ $attempt -le $max_retries ]; do
        log "Executing: $command (attempt $attempt/$max_retries)"
        
        if eval "$command"; then
            log "Command succeeded on attempt $attempt"
            return 0
        else
            local exit_code=$?
            error_log "Command failed on attempt $attempt: $command (exit code: $exit_code)"
            
            if [ $attempt -lt $max_retries ]; then
                log "Retrying in $retry_delay seconds..."
                sleep $retry_delay
                ((attempt++))
            else
                error_log "Command failed after $max_retries attempts: $command"
                return $exit_code
            fi
        fi
    done
}

 Health check function
health_check() {
    local service="$1"
    
    if systemctl is-active --quiet "$service"; then
        log "Health check passed: $service is running"
        return 0
    else
        error_log "Health check failed: $service is not running"
        return 1
    fi
}

 Backup with verification
backup_with_verification() {
    local source="$1"
    local dest="$2"
    
    log "Starting backup: $source -> $dest"
    
     Create backup
    if tar -czf "$dest" "$source"; then
        log "Backup created successfully"
        
         Verify backup integrity
        if tar -tzf "$dest" >/dev/null 2>&1; then
            log "Backup verification passed"
            return 0
        else
            error_log "Backup verification failed"
            rm -f "$dest"
            return 1
        fi
    else
        error_log "Backup creation failed"
        return 1
    fi
}

 Notification function
send_notification() {
    local subject="$1"
    local message="$2"
    local priority="${3:-normal}"
    
     Email notification
    if command -v mail >/dev/null 2>&1; then
        echo "$message" | mail -s "$subject" admin@company.com
        log "Email notification sent: $subject"
    fi
    
     Slack notification (if webhook configured)
    if [ -n "${SLACK_WEBHOOK:-}" ]; then
        curl -X POST -H 'Content-type: application/json' \
            --data "{\"text\":\"$subject: $message\"}" \
            "$SLACK_WEBHOOK" 2>/dev/null || true
        log "Slack notification sent: $subject"
    fi
}

 Cleanup function
cleanup() {
    log "Performing cleanup..."
    
     Remove temporary files
    find /tmp -name "automation_" -mtime +1 -delete 2>/dev/null || true
    
     Compress old logs
    find /var/log -name "robust_automation.log" -mtime +7 -exec gzip {} \; 2>/dev/null || true
    
    log "Cleanup completed"
}

 Main automation workflow
main() {
    log "Starting robust automation workflow"
    
     Set up cleanup trap
    trap cleanup EXIT
    
     Health checks
    if ! health_check "nginx" || ! health_check "mysql"; then
        send_notification "Health Check Failed" "Critical services are not running" "high"
        exit 1
    fi
    
     Backup with retry
    if ! retry_command "backup_with_verification /var/www/html /backup/web_$(date +%Y%m%d_%H%M%S).tar.gz"; then
        send_notification "Backup Failed" "Web backup failed after retries" "high"
        exit 1
    fi
    
     System update with retry
    if ! retry_command "apt update && apt upgrade -y"; then
        send_notification "System Update Failed" "System update failed after retries" "medium"
        exit 1
    fi
    
     Success notification
    send_notification "Automation Completed" "All tasks completed successfully" "low"
    
    log "Robust automation workflow completed successfully"
}

main "$@"
```

 7. Monitoring and Alerting

 7.1. Automation Monitoring
```bash
!/bin/bash
 monitor_automation.sh - Monitor automation jobs

set -euo pipefail

 Configuration
MONITOR_LOG="/var/log/automation_monitor.log"
ALERT_THRESHOLD=3
CHECK_INTERVAL=300   5 minutes

 Logging function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$MONITOR_LOG"
}

 Check cron jobs
check_cron_jobs() {
    log "Checking cron jobs"
    
    local failed_jobs=0
    
     Check if cron daemon is running
    if ! systemctl is-active --quiet cron; then
        log "ERROR: Cron daemon is not running"
        ((failed_jobs++))
    fi
    
     Check for recent cron activity
    local last_cron_activity=$(journalctl -u cron --since "1 hour ago" | wc -l)
    if [ "$last_cron_activity" -eq 0 ]; then
        log "WARNING: No cron activity in the last hour"
        ((failed_jobs++))
    fi
    
     Check specific cron job logs
    if [ ! -f "/var/log/backup.log" ]; then
        log "ERROR: Backup log file not found"
        ((failed_jobs++))
    else
        local last_backup=$(tail -1 /var/log/backup.log | grep "completed" | wc -l)
        if [ "$last_backup" -eq 0 ]; then
            log "ERROR: No successful backup found in recent logs"
            ((failed_jobs++))
        fi
    fi
    
    return $failed_jobs
}

 Check systemd timers
check_systemd_timers() {
    log "Checking systemd timers"
    
    local failed_timers=0
    
     List all timers
    while IFS= read -r timer; do
        local timer_name=$(echo "$timer" | awk '{print $1}')
        local timer_status=$(echo "$timer" | awk '{print $3}')
        
        if [ "$timer_status" = "failed" ]; then
            log "ERROR: Timer $timer_name has failed"
            ((failed_timers++))
        fi
    done < <(systemctl list-timers --all | grep -v "NEXT" | grep -v "LAST")
    
    return $failed_timers
}

 Check disk space
check_disk_space() {
    log "Checking disk space"
    
    local threshold=90
    local critical_disks=0
    
    while IFS= read -r line; do
        local filesystem=$(echo "$line" | awk '{print $1}')
        local usage=$(echo "$line" | awk '{print $5}' | cut -d'%' -f1)
        
        if [ "$usage" -gt "$threshold" ]; then
            log "ERROR: Disk $filesystem is ${usage}% full (threshold: ${threshold}%)"
            ((critical_disks++))
        fi
    done < <(df -h | grep -vE '^Filesystem|tmpfs|cdrom')
    
    return $critical_disks
}

 Send alert
send_alert() {
    local message="$1"
    local severity="${2:-warning}"
    
    log "ALERT: $message"
    
     Email alert
    if command -v mail >/dev/null 2>&1; then
        echo "Automation Alert: $message" | mail -s "Automation Monitor Alert ($severity)" admin@company.com
    fi
    
     Slack alert
    if [ -n "${SLACK_WEBHOOK:-}" ]; then
        local color="warning"
        [ "$severity" = "critical" ] && color="danger"
        
        curl -X POST -H 'Content-type: application/json' \
            --data "{\"attachments\":[{\"color\":\"$color\",\"text\":\"$message\"}]}" \
            "$SLACK_WEBHOOK" 2>/dev/null || true
    fi
}

 Main monitoring loop
main() {
    log "Starting automation monitoring"
    
    local total_issues=0
    
     Check cron jobs
    check_cron_jobs
    total_issues=$((total_issues + $?))
    
     Check systemd timers
    check_systemd_timers
    total_issues=$((total_issues + $?))
    
     Check disk space
    check_disk_space
    total_issues=$((total_issues + $?))
    
     Send alert if issues exceed threshold
    if [ "$total_issues" -ge "$ALERT_THRESHOLD" ]; then
        send_alert "Multiple automation issues detected ($total_issues total)" "critical"
    elif [ "$total_issues" -gt 0 ]; then
        send_alert "Automation issues detected ($total_issues total)" "warning"
    else
        log "All automation systems healthy"
    fi
    
    log "Automation monitoring completed"
}

main "$@"
```

 8. Configuration Management

 8.1. Environment-based Configuration
```bash
!/bin/bash
 config_manager.sh - Configuration management for automation

set -euo pipefail

 Configuration
CONFIG_DIR="/etc/automation"
ENV_FILE="$CONFIG_DIR/environment.conf"
LOCAL_CONFIG="$CONFIG_DIR/local.conf"

 Load configurations
load_configurations() {
     Default configuration
    declare -A DEFAULT_CONFIG=(
        ["BACKUP_DIR"]="/backup"
        ["LOG_LEVEL"]="INFO"
        ["MAX_RETRIES"]="3"
        ["NOTIFICATION_EMAIL"]="admin@company.com"
        ["SLACK_WEBHOOK"]=""
    )
    
     Environment-specific configuration
    if [ -f "$ENV_FILE" ]; then
        source "$ENV_FILE"
        log "Loaded environment configuration from $ENV_FILE"
    else
        log "No environment configuration found, using defaults"
    fi
    
     Local override configuration
    if [ -f "$LOCAL_CONFIG" ]; then
        source "$LOCAL_CONFIG"
        log "Loaded local configuration from $LOCAL_CONFIG"
    fi
}

 Validate configuration
validate_config() {
    local errors=0
    
     Check required directories
    if [ ! -d "${BACKUP_DIR:-/backup}" ]; then
        echo "ERROR: Backup directory $BACKUP_DIR does not exist"
        ((errors++))
    fi
    
     Check email configuration
    if [ -z "${NOTIFICATION_EMAIL:-}" ]; then
        echo "WARNING: No notification email configured"
    fi
    
     Check log level
    case "${LOG_LEVEL:-INFO}" in
        DEBUG|INFO|WARN|ERROR)
            ;;
        )
            echo "ERROR: Invalid log level: $LOG_LEVEL"
            ((errors++))
            ;;
    esac
    
    return $errors
}

 Generate configuration template
generate_config_template() {
    cat > "$ENV_FILE.template" << EOF
 Environment Configuration Template
BACKUP_DIR="/backup"
LOG_LEVEL="INFO"
MAX_RETRIES="3"
NOTIFICATION_EMAIL="admin@company.com"
SLACK_WEBHOOK=""

 Environment-specific settings
ENVIRONMENT="production"
DEBUG_MODE="false"
EOF
    
    echo "Configuration template generated: $ENV_FILE.template"
}

 Main function
main() {
    local action="${1:-load}"
    
    case "$action" in
        "load")
            load_configurations
            validate_config
            ;;
        "template")
            generate_config_template
            ;;
        "validate")
            load_configurations
            validate_config
            ;;
        )
            echo "Usage: $0 {load|template|validate}"
            exit 1
            ;;
    esac
}

main "$@"
```

 9. Integration with External Systems

 9.1. API Integration
```bash
!/bin/bash
 api_integration.sh - Integration with external APIs

set -euo pipefail

 Configuration
API_BASE_URL="https://api.example.com"
API_TOKEN="${API_TOKEN:-}"
TIMEOUT=30

 API request function
api_request() {
    local method="$1"
    local endpoint="$2"
    local data="${3:-}"
    local response_file=$(mktemp)
    
    local curl_cmd="curl -s -w '%{http_code}' -X $method"
    curl_cmd="$curl_cmd -H 'Content-Type: application/json'"
    curl_cmd="$curl_cmd -H 'Authorization: Bearer $API_TOKEN'"
    curl_cmd="$curl_cmd --connect-timeout $TIMEOUT"
    
    if [ -n "$data" ]; then
        curl_cmd="$curl_cmd -d '$data'"
    fi
    
    curl_cmd="$curl_cmd '$API_BASE_URL$endpoint'"
    
    local response=$(eval "$curl_cmd")
    local status_code="${response: -3}"
    local body="${response%???}"
    
    echo "$body" > "$response_file"
    
    if [ "$status_code" -ge 200 ] && [ "$status_code" -lt 300 ]; then
        echo "$body"
        rm -f "$response_file"
        return 0
    else
        echo "API request failed with status $status_code: $body" >&2
        rm -f "$response_file"
        return 1
    fi
}

 Send metrics to monitoring system
send_metrics() {
    local metric_name="$1"
    local metric_value="$2"
    local metric_type="${3:-gauge}"
    
    local data='{
        "name": "'$metric_name'",
        "value": '$metric_value',
        "type": "'$metric_type'",
        "timestamp": '$(date +%s)'
    }'
    
    if api_request "POST" "/metrics" "$data"; then
        log "Metric sent successfully: $metric_name = $metric_value"
    else
        log "Failed to send metric: $metric_name"
    fi
}

 Get configuration from API
get_config_from_api() {
    local config_key="$1"
    
    local response=$(api_request "GET" "/config/$config_key")
    
    if [ $? -eq 0 ]; then
        echo "$response" | jq -r '.value'
    else
        return 1
    fi
}

 Report automation status
report_status() {
    local status="$1"
    local message="$2"
    
    local data='{
        "automation": "backup_script",
        "status": "'$status'",
        "message": "'$message'",
        "timestamp": '$(date +%s)',
        "hostname": "'$(hostname)'"
    }'
    
    if api_request "POST" "/status" "$data"; then
        log "Status reported successfully: $status"
    else
        log "Failed to report status"
    fi
}
```

 10. Complete Automation Examples

 10.1. Complete Backup Automation
```bash
!/bin/bash
 complete_backup_automation.sh - Comprehensive backup automation

set -euo pipefail

 Configuration
CONFIG_FILE="/etc/backup/config.conf"
LOG_FILE="/var/log/backup.log"
LOCK_FILE="/var/run/backup.lock"
BACKUP_DIR="/backup"
RETENTION_DAYS=30

 Load configuration
if [ -f "$CONFIG_FILE" ]; then
    source "$CONFIG_FILE"
fi

 Logging function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

 Lock mechanism
setup_lock() {
    if [ -f "$LOCK_FILE" ]; then
        local pid=$(cat "$LOCK_FILE")
        if kill -0 "$pid" 2>/dev/null; then
            log "Backup already running (PID: $pid)"
            exit 1
        else
            log "Removing stale lock file"
            rm -f "$LOCK_FILE"
        fi
    fi
    
    echo $$ > "$LOCK_FILE"
    trap 'rm -f "$LOCK_FILE"' EXIT
}

 Pre-backup checks
pre_backup_checks() {
    log "Performing pre-backup checks"
    
     Check disk space
    local available_space=$(df "$BACKUP_DIR" | awk 'NR==2 {print $4}')
    local required_space=1048576   1GB in KB
    
    if [ "$available_space" -lt "$required_space" ]; then
        log "ERROR: Insufficient disk space for backup"
        return 1
    fi
    
     Check source directories
    local sources=("/var/www/html" "/etc" "/home")
    for source in "${sources[@]}"; do
        if [ ! -d "$source" ]; then
            log "WARNING: Source directory $source does not exist"
        fi
    done
    
    log "Pre-backup checks completed"
    return 0
}

 Perform backup
perform_backup() {
    log "Starting backup process"
    
    local timestamp=$(date +%Y%m%d_%H%M%S)
    local backup_file="$BACKUP_DIR/system_backup_$timestamp.tar.gz"
    
     Create backup
    tar --exclude=/proc --exclude=/sys --exclude=/dev \
        --exclude=/run --exclude=/tmp --exclude=/backup \
        --exclude=/var/cache \
        -czf "$backup_file" / 2>> "$LOG_FILE"
    
    if [ $? -eq 0 ]; then
        log "Backup created: $backup_file"
        
         Verify backup
        if tar -tzf "$backup_file" >/dev/null 2>&1; then
            log "Backup verification passed"
            echo "$backup_file"
            return 0
        else
            log "ERROR: Backup verification failed"
            rm -f "$backup_file"
            return 1
        fi
    else
        log "ERROR: Backup creation failed"
        return 1
    fi
}

 Post-backup tasks
post_backup_tasks() {
    local backup_file="$1"
    
    log "Performing post-backup tasks"
    
     Update backup manifest
    local manifest="$BACKUP_DIR/manifest.txt"
    echo "$(date +%Y-%m-%d_%H:%M:%S) $backup_file $(stat -c%s "$backup_file")" >> "$manifest"
    
     Clean old backups
    find "$BACKUP_DIR" -name "system_backup_.tar.gz" -mtime +$RETENTION_DAYS -delete
    
     Test restore (optional)
    if [ "${TEST_RESTORE:-false}" = "true" ]; then
        log "Testing backup restore"
        local test_dir="/tmp/backup_test_$$"
        mkdir -p "$test_dir"
        
        if tar -xzf "$backup_file" -C "$test_dir" --strip-components=1 etc/hosts 2>/dev/null; then
            log "Backup test restore successful"
        else
            log "WARNING: Backup test restore failed"
        fi
        
        rm -rf "$test_dir"
    fi
    
    log "Post-backup tasks completed"
}

 Send notifications
send_notifications() {
    local status="$1"
    local message="$2"
    
     Email notification
    if command -v mail >/dev/null 2>&1 && [ -n "${NOTIFICATION_EMAIL:-}" ]; then
        echo "$message" | mail -s "Backup $status" "$NOTIFICATION_EMAIL"
        log "Email notification sent"
    fi
    
     Slack notification
    if [ -n "${SLACK_WEBHOOK:-}" ]; then
        local color="good"
        [ "$status" = "FAILED" ] && color="danger"
        
        curl -X POST -H 'Content-type: application/json' \
            --data "{\"attachments\":[{\"color\":\"$color\",\"text\":\"Backup $status: $message\"}]}" \
            "$SLACK_WEBHOOK" 2>/dev/null || true
        
        log "Slack notification sent"
    fi
}

 Main backup workflow
main() {
    log "Starting backup automation"
    
     Setup
    setup_lock
    
     Pre-backup checks
    if ! pre_backup_checks; then
        send_notifications "FAILED" "Pre-backup checks failed"
        exit 1
    fi
    
     Perform backup
    local backup_file
    if backup_file=$(perform_backup); then
         Post-backup tasks
        post_backup_tasks "$backup_file"
        
         Success notification
        send_notifications "SUCCESS" "Backup completed successfully: $backup_file"
        
        log "Backup automation completed successfully"
    else
         Failure notification
        send_notifications "FAILED" "Backup process failed"
        
        log "Backup automation failed"
        exit 1
    fi
}

main "$@"
```

 11. Troubleshooting Automation Issues

 11.1. Common Problems and Solutions
```bash
!/bin/bash
 troubleshoot_automation.sh - Automation troubleshooting guide

 Check cron daemon
check_cron_daemon() {
    echo "=== Checking Cron Daemon ==="
    
    if systemctl is-active --quiet cron; then
        echo "Cron daemon is running"
    else
        echo "ERROR: Cron daemon is not running"
        echo "Solution: sudo systemctl start cron"
    fi
    
    echo "Cron status:"
    systemctl status cron --no-pager
}

 Check systemd timers
check_systemd_timers() {
    echo "=== Checking Systemd Timers ==="
    
    echo "Active timers:"
    systemctl list-timers --no-pager
    
    echo "Failed timers:"
    systemctl list-timers --all | grep failed || echo "No failed timers"
}

 Check permissions
check_permissions() {
    echo "=== Checking Permissions ==="
    
    local scripts=("/home/user/scripts/backup.sh" "/home/user/scripts/maintenance.sh")
    
    for script in "${scripts[@]}"; do
        if [ -f "$script" ]; then
            echo "Script: $script"
            ls -la "$script"
            
            if [ ! -x "$script" ]; then
                echo "WARNING: Script is not executable"
                echo "Solution: chmod +x $script"
            fi
        else
            echo "WARNING: Script $script does not exist"
        fi
    done
}

 Check environment variables
check_environment() {
    echo "=== Checking Environment ==="
    
    echo "PATH: $PATH"
    echo "HOME: $HOME"
    echo "USER: $USER"
    
     Check required commands
    local commands=("tar" "gzip" "mail" "curl" "jq")
    for cmd in "${commands[@]}"; do
        if command -v "$cmd" >/dev/null 2>&1; then
            echo "Command $cmd: $(command -v "$cmd")"
        else
            echo "WARNING: Command $cmd not found"
        fi
    done
}

 Check logs
check_logs() {
    echo "=== Checking Logs ==="
    
    local logs=("/var/log/cron.log" "/var/log/syslog" "/var/log/backup.log")
    
    for log in "${logs[@]}"; do
        if [ -f "$log" ]; then
            echo "=== Last 10 lines of $log ==="
            tail -10 "$log"
            echo ""
        else
            echo "Log file $log does not exist"
        fi
    done
}

 Test cron job
test_cron_job() {
    echo "=== Testing Cron Job ==="
    
     Create test cron job
    local test_script="/tmp/test_cron.sh"
    cat > "$test_script" << 'EOF'
!/bin/bash
echo "Cron job test: $(date)" >> /tmp/cron_test.log
EOF
    
    chmod +x "$test_script"
    
     Add to crontab
    (crontab -l; echo "     $test_script") | crontab -
    
    echo "Test cron job added. Check /tmp/cron_test.log in 1-2 minutes"
    echo "To remove: crontab -e and delete the test line"
}

 Main troubleshooting function
main() {
    local check_type="${1:-all}"
    
    case "$check_type" in
        "cron")
            check_cron_daemon
            ;;
        "timers")
            check_systemd_timers
            ;;
        "permissions")
            check_permissions
            ;;
        "environment")
            check_environment
            ;;
        "logs")
            check_logs
            ;;
        "test")
            test_cron_job
            ;;
        "all")
            check_cron_daemon
            check_systemd_timers
            check_permissions
            check_environment
            check_logs
            ;;
        )
            echo "Usage: $0 {cron|timers|permissions|environment|logs|test|all}"
            exit 1
            ;;
    esac
}

main "$@"
```

 12. Resources and Best Practices

 12.1. Documentation
- [Cron Tutorial](https://crontab.guru/)
- [Systemd Timer Documentation](https://www.freedesktop.org/software/systemd/man/systemd.timer.html)
- [Bash Scripting Guide](https://www.gnu.org/software/bash/manual/)

 12.2. Tools and Utilities
- Cronicle: Web-based cron job management
- JobScheduler: Advanced job scheduling
- Rundeck: Automation and orchestration
- Ansible: Configuration management and automation

 12.3. Best Practices
- Use absolute paths in cron jobs
- Set proper environment variables
- Implement comprehensive logging
- Add error handling and retry mechanisms
- Use lock files to prevent overlapping runs
- Monitor automation health and performance
- Test automation in staging before production
- Document automation workflows and dependencies

---

Next Steps: Implement these automation patterns in your environment, start with simple cron jobs, and gradually build more complex workflows with proper error handling and monitoring.
