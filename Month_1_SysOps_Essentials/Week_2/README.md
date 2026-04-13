 Week 2: Linux & Bash Fundamentals

Duration: 5 days | Theme: Command Line Mastery & System Administration | Difficulty: Intermediate  
Prerequisite: Complete Week 1 (Git & Environment Setup)

---

 🎯 Learning Objectives

By the end of this week, you will:
- ✅ Master Linux file system navigation and manipulation
- ✅ Understand file permissions, ownership, and security
- ✅ Write production-ready Bash scripts with error handling
- ✅ Use powerful text processing tools (grep, sed, awk, cut)
- ✅ Manage processes, services, and system resources
- ✅ Automate tasks with cron and shell scripting
- ✅ Monitor system performance and troubleshoot issues

---

 📖 Detailed Daily Schedule

 Day 1: Linux Fundamentals - File System & Navigation
Lecture: [01_linux_fundamentals.md](./lecture_notes/01_linux_fundamentals.md)

Topics:
- Linux history and distributions (Ubuntu, CentOS, macOS)
- File system hierarchy (FHS) - /bin, /etc, /home, /var, /tmp
- Terminal basics and shell types (bash, zsh, fish)
- Navigation commands: `pwd`, `cd`, `ls`, `tree`
- File operations: `touch`, `mkdir`, `cp`, `mv`, `rm`, `rmdir`
- Viewing files: `cat`, `less`, `more`, `head`, `tail`

Hands-On:
```bash
 Explore file system
pwd                            Print working directory
cd ~                           Go to home directory
ls -la                         List all files with details
cd /etc && ls | head -20       View system config files

 Create and manipulate files
mkdir -p ~/linux-practice/{docs,scripts,logs}
cd ~/linux-practice
touch docs/readme.txt
echo "Hello, Linux!" > docs/readme.txt
cat docs/readme.txt

 Copy and move
cp docs/readme.txt scripts/
mv scripts/readme.txt scripts/backup.txt
ls -R .                        Recursive listing
```

Practice:
- [ ] Navigate entire file system from root to home
- [ ] Create nested directory structure (3 levels deep)
- [ ] Copy multiple files with wildcards
- [ ] View system logs in /var/log
- [ ] Practice relative vs absolute paths

---

 Day 2: File Permissions, Ownership & Security
Lecture: [02_file_permissions.md](./lecture_notes/02_file_permissions.md)

Topics:
- Users and groups in Linux
- File permissions: read (r), write (w), execute (x)
- Permission representation: numeric (755) vs symbolic (rwxr-xr-x)
- Commands: `chmod`, `chown`, `chgrp`, `umask`
- Special permissions: SUID, SGID, Sticky Bit
- Access Control Lists (ACLs) basics

Hands-On:
```bash
 Create test file and check permissions
cd ~/linux-practice
touch permissions_test.txt
ls -l permissions_test.txt

 Change permissions
chmod 644 permissions_test.txt     rw-r--r--
chmod u+x permissions_test.txt     Add execute for owner
chmod go-w permissions_test.txt    Remove write for group/others

 Change ownership
sudo chown $USER:$USER permissions_test.txt

 Create executable script
cat > scripts/hello.sh << 'EOF'
!/bin/bash
echo "Hello from $(whoami)"
EOF
chmod +x scripts/hello.sh
./scripts/hello.sh

 Check effective permissions
id                             Show user ID and groups
umask                          Show default file permissions
```

Practice:
- [ ] Create files with different permission sets
- [ ] Practice both numeric and symbolic chmod
- [ ] Create shared directory with group permissions
- [ ] Set up a script that only you can execute
- [ ] Understand umask impact on new files

---

 Day 3: Bash Scripting Fundamentals
Lecture: [03_bash_scripting.md](./lecture_notes/03_bash_scripting.md)

Topics:
- Shebang (`!/bin/bash`) and script structure
- Variables: local, environment, special variables ($0, $1, $@, $, $$)
- Input/output: `read`, `echo`, `printf`, redirection (>, >>, 2>, &>)
- Conditionals: `if`, `case`, `test`, `[[ ]]`, `(( ))`
- Loops: `for`, `while`, `until`, `break`, `continue`
- Functions: definition, arguments, return values, scope

Hands-On:
```bash
cd ~/linux-practice/scripts

 Create first script
cat > user_info.sh << 'EOF'
!/bin/bash
 User Information Script

 Variables
USER_NAME=$(whoami)
CURRENT_DIR=$(pwd)
DATE=$(date +%Y-%m-%d)

 Output
echo "================================"
echo "User Information Report"
echo "================================"
echo "Username: $USER_NAME"
echo "Current Directory: $CURRENT_DIR"
echo "Date: $DATE"
echo "Home Directory: $HOME"
echo "Shell: $SHELL"
echo "================================"
EOF
chmod +x user_info.sh
./user_info.sh

 Script with arguments
cat > greet.sh << 'EOF'
!/bin/bash
 Greeting script with arguments

if [ $ -eq 0 ]; then
    echo "Usage: $0 <name>"
    exit 1
fi

NAME=$1
echo "Hello, $NAME!"
echo "You provided $ arguments"
echo "Script name: $0"
EOF
chmod +x greet.sh
./greet.sh "3MTT Student"

 Script with functions
cat > calculator.sh << 'EOF'
!/bin/bash
 Simple calculator

add() {
    echo $(($1 + $2))
}

subtract() {
    echo $(($1 - $2))
}

multiply() {
    echo $(($1  $2))
}

 Main
echo "Calculator"
echo "5 + 3 = $(add 5 3)"
echo "10 - 4 = $(subtract 10 4)"
echo "6  7 = $(multiply 6 7)"
EOF
chmod +x calculator.sh
./calculator.sh
```

Practice:
- [ ] Create script that takes user input
- [ ] Write script with multiple functions
- [ ] Practice all special variables ($0-$9, $@, $)
- [ ] Use all loop types (for, while, until)
- [ ] Create script with error handling

---

 Day 4: Text Processing Power Tools
Lecture: [04_text_processing.md](./lecture_notes/04_text_processing.md)

Topics:
- `grep`: pattern searching, regular expressions, options (-i, -v, -n, -r, -E)
- `sed`: stream editor, substitutions, deletions, insertions
- `awk`: pattern scanning, field processing, built-in variables
- `cut`: extract columns/fields
- `sort` and `uniq`: sorting, duplicate removal, counting
- `wc`: word, line, character counting
- Pipes and command chaining

Hands-On:
```bash
cd ~/linux-practice

 Create sample data file
cat > data/sample.log << 'EOF'
2024-01-15 10:23:45 INFO User login successful: john
2024-01-15 10:24:12 ERROR Database connection failed
2024-01-15 10:25:33 INFO User logout: john
2024-01-15 10:26:01 WARN Low disk space
2024-01-15 10:27:45 ERROR File not found: config.txt
2024-01-15 10:28:12 INFO Backup completed successfully
EOF

 grep - pattern matching
grep "ERROR" data/sample.log               Find errors
grep -i "info" data/sample.log             Case insensitive
grep -n "login" data/sample.log            Show line numbers
grep -E "ERROR|WARN" data/sample.log       Multiple patterns
grep -v "INFO" data/sample.log             Exclude INFO lines

 sed - stream editing
sed 's/ERROR/CRITICAL/' data/sample.log    Substitute
cat data/sample.log | sed '2,4d'           Delete lines 2-4
sed -n '1,3p' data/sample.log              Print only lines 1-3

 awk - field processing
awk '{print $1}' data/sample.log           First column
awk '{print $1, $2}' data/sample.log        First two columns
awk '/ERROR/ {print $0}' data/sample.log   Lines with ERROR
awk -F: '{print $2}' data/sample.log        Use : as delimiter

 cut - extract fields
cut -d' ' -f1 data/sample.log              First field (space delimiter)
cut -d' ' -f1,2 data/sample.log            First two fields

 sort and uniq
sort data/sample.log                       Sort alphabetically
sort -k2 data/sample.log                   Sort by second field
grep "ERROR" data/sample.log | wc -l       Count errors
cut -d' ' -f3 data/sample.log | sort | uniq -c   Count occurrences

 Pipeline magic
cat data/sample.log | \
    grep "ERROR" | \
    awk '{print $1}' | \
    sort | \
    uniq -c | \
    sort -nr
```

Practice:
- [ ] Extract all IP addresses from a log file
- [ ] Count occurrences of each word in a file
- [ ] Find largest files in a directory
- [ ] Parse CSV file and extract specific columns
- [ ] Create report from log analysis

---

 Day 5: System Administration & Automation
Lecture: [05_system_administration.md](./lecture_notes/05_system_administration.md)

Topics:
- Process management: `ps`, `top`, `htop`, `kill`, `pkill`
- System monitoring: `df`, `du`, `free`, `vmstat`, `iostat`
- Package management: `apt`/`yum`/`brew`, `pip`, `conda`
- Cron jobs: `crontab`, scheduling syntax, common patterns
- System logs: `/var/log`, `journalctl`, `dmesg`
- Network basics: `ping`, `curl`, `wget`, `netstat`, `ss`

Hands-On:
```bash
 Process management
ps aux                       All processes
ps aux | grep python         Find Python processes
top -o %CPU                  Sort by CPU usage (quit with 'q')
 htop (if installed)       Interactive process viewer

 Disk and memory
df -h                        Disk space (human readable)
du -sh ~                     Directory size
du -h --max-depth=1 ~        Size of each subdirectory
free -h                      Memory usage

 System info
uname -a                     Kernel info
uptime                       System uptime
whoami                       Current user
who                          Logged in users

 Package management (Ubuntu/Debian)
sudo apt update              Update package lists
sudo apt upgrade             Upgrade packages
apt list --installed         List installed packages
apt search package_name      Search for package

 Cron jobs
crontab -l                   List current crontab
crontab -e                   Edit crontab

 Create cron job example (edit crontab -e)
 /5     echo "Cron job ran at $(date)" >> ~/linux-practice/logs/cron.log

 Network
curl -I https://github.com   Check headers
ping -c 4 google.com         Test connectivity
netstat -tuln                Show listening ports
ss -tuln                     Modern alternative to netstat
```

Practice:
- [ ] Create script that monitors disk space
- [ ] Set up cron job to run daily backup
- [ ] Write log rotation script
- [ ] Monitor system resources in real-time
- [ ] Create automated system health check

---

 📚 Essential Resources

 Required Reading
1. Enhanced Lecture Notes:
   - [01_linux_fundamentals.md](./lecture_notes/01_linux_fundamentals.md) - File system and navigation
   - [02_file_permissions.md](./lecture_notes/02_file_permissions.md) - Security and permissions
   - [03_bash_scripting.md](./lecture_notes/03_bash_scripting.md) - Scripting fundamentals
   - [04_text_processing.md](./lecture_notes/04_text_processing.md) - grep, sed, awk
   - [05_system_administration.md](./lecture_notes/05_system_administration.md) - Processes and automation

2. External Resources:
   - [Linux Command Line](http://linuxcommand.org/) - Complete guide
   - [Advanced Bash-Scripting Guide](https://tldp.org/LDP/abs/html/) - Comprehensive reference
   - [Explain Shell](https://explainshell.com/) - Command explanation tool
   - [ShellCheck](https://www.shellcheck.net/) - Script linter

 Tools & Setup
```bash
 Required
- Linux, macOS, or WSL on Windows
- Bash 4.0+ (check with bash --version)
- Terminal emulator (Terminal, iTerm2, Windows Terminal)
- Text editor (nano, vim, or VS Code)

 Recommended Tools
sudo apt install tree htop ncdu   Ubuntu/Debian
brew install tree htop ncdu       macOS

 VS Code Extensions for Shell
- Bash IDE
- ShellCheck
- Better TOML
```

---

 💼 Weekly Deliverables

 Required Submissions

1. File System Navigation (15%)
   - Screenshot showing navigation to 5 different directories
   - Directory tree structure you created
   - Commands used to manipulate files

2. Permission Management (15%)
   - Files with different permission sets documented
   - Script demonstrating permission changes
   - Group collaboration setup (if applicable)

3. Bash Scripts Collection (30%)
   - Minimum 5 working scripts:
     - Script with arguments and validation
     - Script with functions
     - Script with loops and conditionals
     - Script with error handling
     - Utility script (file organizer, backup, etc.)

4. Text Processing Pipeline (20%)
   - Log analysis script using grep/sed/awk
   - Data processing pipeline
   - Report generation from text files

5. System Administration (20%)
   - Cron job configuration
   - System monitoring script
   - Process management documentation

---

 🎯 Exercises

 Exercise 1: Directory Navigator
Create an interactive script that:
- Shows current directory contents
- Allows navigation (cd to selected directory)
- Displays file information (size, permissions, modification date)
- Provides "go back" functionality
- Includes help menu

 Exercise 2: Permission Auditor
Create a script that:
- Scans a directory recursively
- Lists all files with world-writable permissions
- Identifies SUID/SGID files
- Generates security report
- Suggests permission fixes

 Exercise 3: Log Analyzer Pipeline
Create a text processing pipeline that:
- Parses web server logs (or sample data)
- Extracts unique IP addresses
- Counts requests per hour
- Identifies error patterns
- Generates summary report

 Exercise 4: System Monitor Dashboard
Create a monitoring script that:
- Shows CPU usage
- Displays memory usage
- Lists top 5 processes by CPU
- Shows disk usage for key partitions
- Updates every 5 seconds
- Saves to log file

 Exercise 5: Automated Backup System
Create a comprehensive backup solution:
- Archives specified directories
- Creates timestamped backups
- Removes old backups (keep last 7 days)
- Logs all operations
- Scheduled via cron
- Sends notification on completion/failure

---

 📋 Assignment: System Administration Toolkit

 Part A: File System Mastery (25 points)
- [ ] Create complex directory structure (5 pts)
- [ ] Navigate using both relative and absolute paths (5 pts)
- [ ] Manipulate files efficiently (5 pts)
- [ ] Understand and use symlinks (5 pts)
- [ ] Search files using find command (5 pts)

 Part B: Scripting Challenge (35 points)
- [ ] Arguments handling and validation (7 pts)
- [ ] Functions with proper scope (7 pts)
- [ ] Error handling (exit codes, trap) (7 pts)
- [ ] Input/output redirection (7 pts)
- [ ] Script documentation and comments (7 pts)

 Part C: Text Processing (25 points)
- [ ] grep with regular expressions (5 pts)
- [ ] sed for text transformations (5 pts)
- [ ] awk for field processing (5 pts)
- [ ] Pipeline construction (5 pts)
- [ ] Report generation (5 pts)

 Part D: System Administration (15 points)
- [ ] Process monitoring (5 pts)
- [ ] Resource usage tracking (5 pts)
- [ ] Cron job implementation (5 pts)

---

 🔗 Quick Navigation

| Resource | Description |
|----------|-------------|
| [� Lecture Notes](./lecture_notes/) | Linux and Bash concepts |
| [🎯 Exercises](./exercises/) | Hands-on shell scripting |
| [📋 Assignments](./assignments/) | Graded system admin tasks |
| [📄 Cheatsheets](./cheatsheets/) | Command reference |

---

 ⚡ Daily Study Schedule

| Time | Activity |
|------|----------|
| 20 min | Review lecture materials |
| 40 min | Hands-on terminal practice |
| 20 min | Complete exercises |
| 10 min | Document and commit scripts |

Total Daily Time: ~1.5 hours

---

 � Success Tips for Week 2

1. Practice Every Day: Linux skills improve with repetition
2. Tab is Your Friend: Use tab completion constantly
3. Man Pages are Gold: `man command` for detailed help
4. Read Before Running: Understand scripts before executing
5. Use Script Shebang: Always start with `!/bin/bash`
6. Test Incrementally: Build scripts one feature at a time
7. Version Control: Commit scripts as you develop them
8. ShellCheck: Validate scripts before submission

---

 🆘 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| `Permission denied` | Check with `ls -l`, use `chmod +x` |
| `Command not found` | Check PATH, install package, or use full path |
| `Syntax error` | Check for missing quotes, brackets |
| `Variable empty` | Check assignment syntax (no spaces around =) |
| `Script won't run` | Verify shebang line, line endings (dos2unix) |
| `Cron job not working` | Check PATH in crontab, use absolute paths |

---

 🎓 Week 2 Completion Checklist

- [ ] Can navigate file system blindfolded
- [ ] Understand all permission combinations
- [ ] 5+ working bash scripts created
- [ ] Mastered grep, sed, awk basics
- [ ] Can monitor system resources
- [ ] Cron job running successfully
- [ ] All exercises completed
- [ ] Assignment submitted

---

Previous: [Week 1: Git & Environment ←](../Week_1/README.md) | Next: [Week 3: AWS Cloud →](../Week_3/README.md)
