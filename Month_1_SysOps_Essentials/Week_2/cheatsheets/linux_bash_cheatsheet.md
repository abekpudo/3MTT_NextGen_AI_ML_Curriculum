Week 2 Cheatsheet: Linux Bash & Automation

Key Concepts
- Filesystem navigation and inspection
- Permissions and ownership
- Process and service management
- Bash scripting patterns
- Automation and scheduling (cron, systemd)

Essential Commands
- `ls -la` — List files with details
- `cd <dir>` — Change directory
- `pwd` — Print working directory
- `find <dir> -type f -size +10M` — Find large files
- `chmod 755 <file>` — Set permissions
- `chown user:group <file>` — Change ownership
- `ps aux | grep <name>` — Find running processes
- `systemctl status <service>` — Check service status
- `grep`, `awk`, `sed` — Text processing
- `crontab -e` — Edit cron jobs
- `bash script.sh` — Run a script

Scripting Tips
- Quote variables: `"$var"`
- Use `set -euo pipefail` for robust scripts
- Check exit codes: `$?`
- Log output: `echo "message" >> logfile.txt`
- Use functions for reusable code

Troubleshooting
- Check permissions: `ls -l`
- Debug scripts: `bash -x script.sh`
- View logs: `tail -f /var/log/syslog`

---
