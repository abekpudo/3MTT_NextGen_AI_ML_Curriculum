 System Administration: Managing Linux Servers

 1. Introduction
System administration is the foundation of maintaining reliable, secure, and efficient Linux servers. This lecture covers user management, service control, system monitoring, security hardening, and operational best practices for production environments.

 2. Why System Administration Matters
- Infrastructure Stability: Keep servers running reliably
- Security: Protect systems and data from threats
- Performance: Optimize resource utilization
- Compliance: Meet organizational and regulatory requirements
- Scalability: Prepare systems for growth and load

 3. User and Group Management

 3.1. User Account Operations
```bash
 Create new user
sudo useradd -m -s /bin/bash username
sudo useradd -m -s /bin/bash -c "John Doe" -G sudo,john

 Useradd options:
 -m: Create home directory
 -s: Specify shell
 -c: Add comment (full name)
 -G: Add to supplementary groups
 -u: Specify user ID
 -g: Specify primary group

 Set password
sudo passwd username
sudo passwd -l username   Lock account
sudo passwd -u username   Unlock account

 Modify user account
sudo usermod -aG docker username   Add to group
sudo usermod -s /bin/zsh username   Change shell
sudo usermod -c "New Name" username   Change comment
sudo usermod -l newname oldname   Change username

 Delete user
sudo userdel -r username   Remove user and home directory
sudo userdel username      Remove user but keep home directory
```

 3.2. Group Management
```bash
 Create groups
sudo groupadd developers
sudo groupadd -g 1500 sysadmins   Specify group ID

 Add users to groups
sudo usermod -aG developers username   Add to group (append)
sudo usermod -G developers username    Set as only supplementary group

 Remove from groups
sudo gpasswd -d username developers

 Group membership commands
groups username           Show user's groups
getent group developers    Show group members
id username               Show user ID and group IDs

 Modify group
sudo groupmod -n newname oldname   Rename group
sudo groupmod -g 1600 groupname    Change group ID

 Delete group
sudo groupdel groupname
```

 3.3. User Account Security
```bash
 Password policies
sudo chage -l username            Show password aging info
sudo chage -M 90 username          Set max password age (days)
sudo chage -W 7 username           Set warning period
sudo chage -E 2024-12-31 username  Set account expiration

 Force password change on next login
sudo chage -d 0 username

 Account locking
sudo usermod -L username           Lock account
sudo usermod -U username           Unlock account

 Check account status
sudo passwd -S username            Show password status
sudo chage -l username             Show aging information
```

 4. Service Management with Systemd

 4.1. Systemd Basics
```bash
 Service status and control
sudo systemctl status nginx         Show service status
sudo systemctl start nginx         Start service
sudo systemctl stop nginx          Stop service
sudo systemctl restart nginx       Restart service
sudo systemctl reload nginx        Reload configuration

 Enable/disable services
sudo systemctl enable nginx        Enable at boot
sudo systemctl disable nginx       Disable at boot
sudo systemctl is-enabled nginx    Check if enabled

 Service information
systemctl list-units --type=service --state=running
systemctl list-units --type=service --state=failed
systemctl list-unit-files --type=service
```

 4.2. Service Configuration
```bash
 Create custom service
sudo nano /etc/systemd/system/myapp.service
```

```ini
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=myapp
Group=myapp
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/bin/myapp
ExecReload=/bin/kill -HUP $MAINPID
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

```bash
 Reload and enable service
sudo systemctl daemon-reload
sudo systemctl enable myapp.service
sudo systemctl start myapp.service

 Service logs
sudo journalctl -u myapp.service            Show service logs
sudo journalctl -u myapp.service -f          Follow logs
sudo journalctl -u myapp.service --since "1 hour ago"
```

 4.3. Service Troubleshooting
```bash
 Check service configuration
sudo systemctl cat nginx.service             Show service file
sudo systemd-analyze verify nginx.service   Validate service file

 Debug failed services
sudo systemctl status nginx --full           Detailed status
sudo journalctl -u nginx.service -n 50       Last 50 log lines
sudo journalctl -u nginx.service -p err      Error messages only

 Service dependencies
systemctl list-dependencies nginx.service
systemctl list-dependencies nginx.service --reverse
```

 5. Package Management

 5.1. APT (Debian/Ubuntu)
```bash
 Update package lists
sudo apt update

 Upgrade packages
sudo apt upgrade                     Upgrade installed packages
sudo apt full-upgrade                Upgrade with dependency changes
sudo apt dist-upgrade                Distribution upgrade

 Package operations
sudo apt install package-name        Install package
sudo apt install package=version      Install specific version
sudo apt remove package-name         Remove package
sudo apt purge package-name          Remove with config files

 Package information
apt show package-name                Package details
apt policy package-name              Available versions
apt-cache search keyword             Search packages
apt-cache depends package-name       Show dependencies

 Maintenance
sudo apt autoremove                  Remove unused packages
sudo apt autoclean                   Clean old package files
sudo apt clean                       Clean all package files
```

 5.2. YUM/DNF (RedHat/CentOS)
```bash
 Update packages
sudo yum update                      Update all packages
sudo yum update package-name         Update specific package

 Package operations
sudo yum install package-name        Install package
sudo yum remove package-name         Remove package
sudo yum erase package-name          Remove with dependencies

 Package information
yum info package-name                Package details
yum search keyword                   Search packages
yum deplist package-name             Show dependencies

 Maintenance
sudo yum clean all                   Clean cache
sudo yum autoremove                  Remove unused packages
```

 5.3. Package Security
```bash
 Security updates (Debian/Ubuntu)
sudo apt update
sudo apt list --upgradable           Show upgradable packages
sudo apt upgrade -y                   Apply updates
sudo unattended-upgrade --dry-run    Preview automatic updates

 Security updates (RedHat/CentOS)
sudo yum update --security           Apply security updates only
sudo yum check-update                Check for updates
sudo yum updateinfo security         Security update information

 Package verification
sudo apt-get --just-print upgrade     Preview changes
sudo yum check-update                Check available updates
```

 6. Log Management and Monitoring

 6.1. System Logs
```bash
 Traditional log files
sudo tail -f /var/log/syslog          Follow system log
sudo tail -f /var/log/auth.log        Follow authentication log
sudo tail -f /var/log/kern.log        Follow kernel log

 Log rotation
sudo logrotate -f /etc/logrotate.conf   Force log rotation
sudo logrotate -d /etc/logrotate.conf   Debug log rotation

 Log analysis
grep "error" /var/log/syslog | tail -20
grep "failed" /var/log/auth.log | awk '{print $1,$2,$3,$NF}'
```

 6.2. Journalctl (Systemd Logs)
```bash
 Basic journalctl usage
sudo journalctl                       Show all logs
sudo journalctl -f                    Follow logs
sudo journalctl -n 100                Last 100 lines

 Filter logs
sudo journalctl -u nginx.service      Service logs
sudo journalctl -p err                Error messages only
sudo journalctl --since "1 hour ago"  Time-based filter
sudo journalctl --since "2024-01-01" --until "2024-01-02"

 Boot and system logs
sudo journalctl -b                     Current boot logs
sudo journalctl -b -1                 Previous boot logs
sudo journalctl -k                    Kernel messages only

 Log analysis
sudo journalctl -u nginx.service | grep "error"
sudo journalctl --since today | grep "authentication"
```

 6.3. Custom Logging
```bash
 Create custom log rotation
sudo nano /etc/logrotate.d/myapp
```

```
/var/log/myapp/.log {
    daily
    missingok
    rotate 30
    compress
    delaycompress
    notifempty
    create 644 myapp myapp
    postrotate
        systemctl reload myapp.service
    endscript
}
```

```bash
 Test log rotation
sudo logrotate -f /etc/logrotate.d/myapp
```

 7. System Monitoring

 7.1. Resource Monitoring
```bash
 System resources
top                                Interactive process viewer
htop                               Enhanced top (if installed)
iotop                              I/O monitoring
nethogs                            Network usage by process

 Memory usage
free -h                            Human-readable memory info
free -m                            Memory in MB
vmstat 1 10                        Virtual memory statistics

 Disk usage
df -h                              Disk usage by filesystem
du -sh /path/to/directory          Directory size
du -ah / | sort -rh | head -20     Largest files and directories

 Process information
ps aux                             All processes in detail
ps -ef                             All processes with full format
ps aux --sort=-%cpu | head -10     Top 10 CPU processes
ps aux --sort=-%mem | head -10     Top 10 memory processes
```

 7.2. Performance Monitoring
```bash
 CPU monitoring
mpstat 1 5                         CPU statistics every 1 second
lscpu                              CPU information
cat /proc/cpuinfo                   Detailed CPU info

 Memory monitoring
vmstat 1                           Virtual memory stats
sar -r 1 10                        Memory utilization
cat /proc/meminfo                  Detailed memory info

 Disk I/O monitoring
iostat 1 5                         Disk I/O statistics
iotop                              I/O by process
sar -d 1 10                        Disk utilization

 Network monitoring
netstat -tulnp                      Network connections
ss -tulnp                          Socket statistics
sar -n DEV 1 10                    Network interface statistics
```

 7.3. System Health Scripts
```bash
!/bin/bash
 health_check.sh - System health monitoring

 Thresholds
CPU_THRESHOLD=80
MEMORY_THRESHOLD=80
DISK_THRESHOLD=90

 Check CPU usage
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
if (( $(echo "$CPU_USAGE > $CPU_THRESHOLD" | bc -l) )); then
    echo "ALERT: CPU usage is ${CPU_USAGE}%"
fi

 Check memory usage
MEMORY_USAGE=$(free | grep Mem | awk '{printf("%.2f"), $3/$2  100.0}')
if (( $(echo "$MEMORY_USAGE > $MEMORY_THRESHOLD" | bc -l) )); then
    echo "ALERT: Memory usage is ${MEMORY_USAGE}%"
fi

 Check disk usage
DISK_USAGE=$(df / | awk 'NR==2 {print $5}' | cut -d'%' -f1)
if [ "$DISK_USAGE" -gt "$DISK_THRESHOLD" ]; then
    echo "ALERT: Disk usage is ${DISK_USAGE}%"
fi

 Check failed services
FAILED_SERVICES=$(systemctl list-units --type=service --state=failed | wc -l)
if [ "$FAILED_SERVICES" -gt 0 ]; then
    echo "ALERT: $FAILED_SERVICES failed services"
fi
```

 8. Security Hardening

 8.1. SSH Security
```bash
 SSH configuration
sudo nano /etc/ssh/sshd_config
```

```ini
 Security settings
Port 2222                          Change default port
PermitRootLogin no                Disable root login
PasswordAuthentication no         Disable password auth
PubkeyAuthentication yes          Enable key auth
MaxAuthTries 3                    Limit authentication attempts
ClientAliveInterval 300           Keep-alive timeout
AllowUsers user1 user2            Restrict allowed users
```

```bash
 Restart SSH service
sudo systemctl restart sshd

 SSH key management
ssh-keygen -t ed25519 -C "user@host"   Generate SSH key
ssh-copy-id user@server                Copy key to server

 SSH security checks
sudo sshd -T                           Test SSH configuration
sudo fail2ban-client status sshd       Check fail2ban status
```

 8.2. Firewall Configuration
```bash
 UFW (Ubuntu)
sudo ufw enable                        Enable firewall
sudo ufw default deny incoming        Default deny incoming
sudo ufw default allow outgoing        Default allow outgoing
sudo ufw allow 22/tcp                  Allow SSH
sudo ufw allow 80/tcp                  Allow HTTP
sudo ufw allow 443/tcp                 Allow HTTPS
sudo ufw status                        Show status

 UFW advanced rules
sudo ufw allow from 192.168.1.0/24 to any port 22
sudo ufw deny from 203.0.113.0/24
sudo ufw delete allow 80/tcp           Remove rule

 FirewallD (RedHat/CentOS)
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

 8.3. System Hardening
```bash
 Update system
sudo apt update && sudo apt upgrade -y

 Remove unnecessary packages
sudo apt autoremove
sudo apt purge $(dpkg -l | awk '/^rc/ {print $2}')

 Secure file permissions
sudo chmod 600 /etc/ssh/sshd_config
sudo chmod 644 /etc/passwd
sudo chmod 600 /etc/shadow
sudo chmod 644 /etc/group

 Set file permissions
find /var/www -type f -exec chmod 644 {} \;
find /var/www -type d -exec chmod 755 {} \;

 Disable unused services
sudo systemctl disable bluetooth
sudo systemctl disable cups
sudo systemctl disable avahi-daemon

 Configure automatic security updates
sudo apt install unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades
```

 9. Backup and Recovery

 9.1. System Backup
```bash
!/bin/bash
 system_backup.sh - Complete system backup

BACKUP_DIR="/backup/system"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/system_backup_$DATE.tar.gz"

 Create backup directory
mkdir -p "$BACKUP_DIR"

 Backup critical directories
tar --exclude=/proc --exclude=/sys --exclude=/dev \
    --exclude=/run --exclude=/tmp --exclude=/backup \
    -czf "$BACKUP_FILE" /

 Verify backup
if [ $? -eq 0 ]; then
    echo "Backup completed: $BACKUP_FILE"
     Clean old backups (keep last 7 days)
    find "$BACKUP_DIR" -name "system_backup_.tar.gz" -mtime +7 -delete
else
    echo "Backup failed"
    exit 1
fi
```

 9.2. Database Backup
```bash
!/bin/bash
 database_backup.sh - MySQL/MariaDB backup

DB_USER="backup_user"
DB_PASS="backup_password"
BACKUP_DIR="/backup/database"
DATE=$(date +%Y%m%d_%H%M%S)

 Create backup directory
mkdir -p "$BACKUP_DIR"

 Backup all databases
mysqldump -u "$DB_USER" -p"$DB_PASS" --all-databases > "$BACKUP_DIR/all_databases_$DATE.sql"

 Backup individual databases
for db in $(mysql -u "$DB_USER" -p"$DB_PASS" -e "SHOW DATABASES;" | grep -v Database); do
    if [ "$db" != "information_schema" ] && [ "$db" != "performance_schema" ]; then
        mysqldump -u "$DB_USER" -p"$DB_PASS" "$db" > "$BACKUP_DIR/${db}_$DATE.sql"
    fi
done

 Compress backups
gzip "$BACKUP_DIR"/.sql

 Clean old backups (keep 7 days)
find "$BACKUP_DIR" -name ".sql.gz" -mtime +7 -delete

echo "Database backup completed"
```

 10. Automation and Scripting

 10.1. System Maintenance Script
```bash
!/bin/bash
 maintenance.sh - Automated system maintenance

LOG_FILE="/var/log/maintenance.log"
DATE=$(date '+%Y-%m-%d %H:%M:%S')

 Logging function
log() {
    echo "[$DATE] $1" | tee -a "$LOG_FILE"
}

 System update
log "Starting system update"
apt update && apt upgrade -y >> "$LOG_FILE" 2>&1
log "System update completed"

 Clean package cache
log "Cleaning package cache"
apt autoremove -y >> "$LOG_FILE" 2>&1
apt autoclean >> "$LOG_FILE" 2>&1
log "Package cache cleaned"

 Check disk space
log "Checking disk space"
df -h >> "$LOG_FILE" 2>&1

 Check failed services
log "Checking failed services"
systemctl list-units --type=service --state=failed >> "$LOG_FILE" 2>&1

 Rotate logs
log "Rotating logs"
logrotate -f /etc/logrotate.conf >> "$LOG_FILE" 2>&1

log "Maintenance completed"
```

 10.2. User Account Management Script
```bash
!/bin/bash
 user_management.sh - Automated user account management

 Create new user with standard setup
create_user() {
    local username="$1"
    local full_name="$2"
    local groups="${3:-users}"
    
     Create user
    useradd -m -s /bin/bash -c "$full_name" -G "$groups" "$username"
    
     Set random password
    password=$(openssl rand -base64 12)
    echo "$username:$password" | chpasswd
    
     Force password change on first login
    chage -d 0 "$username"
    
    echo "Created user: $username"
    echo "Initial password: $password"
}

 Disable inactive users
disable_inactive_users() {
    local days_inactive="${1:-90}"
    
    for user in $(awk -F: '($3 >= 1000) && ($7 != "/sbin/nologin") {print $1}' /etc/passwd); do
        last_login=$(lastlog -u "$user" | awk 'NR==2 {print $9}')
        if [ "$last_login" = "Never" ]; then
            echo "User $user has never logged in"
        fi
    done
}

 Remove expired accounts
remove_expired_accounts() {
    for user in $(awk -F: '($3 >= 1000) {print $1}' /etc/passwd); do
        expiry=$(chage -l "$user" | grep "Account expires" | cut -d: -f2)
        if [ "$expiry" != "never" ]; then
            expiry_timestamp=$(date -d "$expiry" +%s 2>/dev/null || echo 0)
            current_timestamp=$(date +%s)
            if [ "$expiry_timestamp" -lt "$current_timestamp" ]; then
                userdel -r "$user"
                echo "Removed expired account: $user"
            fi
        fi
    done
}
```

 11. Troubleshooting Common Issues

 11.1. Service Problems
```bash
 Check service status
systemctl status service_name

 View service logs
journalctl -u service_name -n 50

 Check configuration
systemd-analyze verify service_name.service

 Check dependencies
systemctl list-dependencies service_name

 Manual service testing
/usr/bin/service_name --config-test
```

 11.2. Performance Issues
```bash
 Check system load
uptime
top
htop

 Check memory usage
free -h
ps aux --sort=-%mem | head -10

 Check disk I/O
iotop
iostat 1 5

 Check network
netstat -i
ss -s
```

 11.3. Security Issues
```bash
 Check failed login attempts
grep "Failed password" /var/log/auth.log | tail -20

 Check for suspicious processes
ps aux | grep -E "(sh|bash|python|perl)" | grep -v grep

 Check open ports
netstat -tulnp
ss -tulnp

 Check file permissions
find / -type f -perm /4000 2>/dev/null   SUID files
find / -type f -perm /2000 2>/dev/null   SGID files
```

 12. Best Practices

 12.1. Documentation
```bash
 Create system documentation
mkdir -p /etc/system-docs

 Document server configuration
echo "Server: $(hostname)" > /etc/system-docs/server_info.txt
echo "OS: $(lsb_release -d | cut -f2)" >> /etc/system-docs/server_info.txt
echo "Kernel: $(uname -r)" >> /etc/system-docs/server_info.txt
echo "IP: $(ip addr show | grep 'inet ' | awk '{print $2}' | cut -d/ -f1)" >> /etc/system-docs/server_info.txt

 Document custom configurations
cp -r /etc/nginx/sites-available/ /etc/system-docs/nginx_configs/
cp -r /etc/systemd/system/.service /etc/system-docs/services/
```

 12.2. Change Management
```bash
 Track system changes
echo "$(date): System update applied" >> /var/log/system_changes.log
echo "$(date): User $USER modified /etc/ssh/sshd_config" >> /var/log/system_changes.log

 Configuration backup
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup.$(date +%Y%m%d)
```

 12.3. Monitoring Setup
```bash
 Setup basic monitoring
 Install monitoring tools
apt install htop iotop nethogs sysstat

 Enable system statistics collection
sed -i 's/ENABLED="false"/ENABLED="true"/' /etc/default/sysstat
systemctl enable sysstat
systemctl start sysstat
```

 13. Resources

 13.1. Documentation
- [Linux System Administration Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/)
- [Ubuntu Server Guide](https://ubuntu.com/server/docs)
- [Systemd Documentation](https://www.freedesktop.org/software/systemd/man/)

 13.2. Tools and Utilities
- Webmin: Web-based system administration
- Cockpit: Web-based server management
- Ansible: Configuration management
- Puppet: Infrastructure automation

---

Next Steps: Practice these commands in a safe environment, create automation scripts for routine tasks, and implement proper monitoring and logging for production systems.
