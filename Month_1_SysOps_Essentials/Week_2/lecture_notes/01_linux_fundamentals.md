 Linux Fundamentals: Mastering the Command Line

 1. Introduction
Linux is the foundation of cloud infrastructure, ML servers, and development environments. This lecture provides hands-on mastery of essential Linux commands, file system navigation, permissions management, and system monitoring.

 2. Why Linux Matters
- Cloud Infrastructure: AWS, GCP, Azure run on Linux
- ML/AI Servers: Most ML frameworks optimized for Linux
- DevOps & Automation: Shell scripting enables powerful automation
- Professional Development: Essential for technical roles

 3. Linux Filesystem Hierarchy

 3.1. Directory Structure
```bash
 Root directory structure
/
|-- bin/      Essential binaries
|-- sbin/     System binaries
|-- etc/      Configuration files
|-- home/     User directories
|-- var/      Variable data (logs, cache)
|-- usr/      User programs
|-- tmp/      Temporary files
|-- opt/      Optional packages
|-- lib/      System libraries
|-- dev/      Device files
|-- proc/     Process information
|-- sys/      System information
```

 3.2. Navigation Commands
```bash
 Basic navigation
pwd                     Print working directory
ls                      List directory contents
ls -la                  List all files with details
ls -lh                  Human-readable file sizes
cd /path/to/dir         Change directory
cd ..                   Go up one level
cd ~                    Go to home directory
cd -                    Previous directory

 Directory creation and removal
mkdir new_directory     Create directory
mkdir -p path/to/deep/dir   Create nested directories
rmdir empty_dir         Remove empty directory
rm -r directory         Remove directory and contents
```

 4. File Operations

 4.1. File Manipulation
```bash
 File creation and viewing
touch new_file.txt      Create empty file
echo "Hello World" > file.txt   Write to file
echo "Append" >> file.txt       Append to file
cat file.txt            View file content
less file.txt           View file with pagination
more file.txt           View file with simple pagination

 File copying and moving
cp source.txt dest.txt          Copy file
cp -r source_dir/ dest_dir/     Copy directory
mv old_name.txt new_name.txt    Rename/move file
mv file.txt /path/to/dest/      Move file

 File deletion
rm file.txt             Remove file
rm -f file.txt          Force remove (no prompt)
rm -r directory         Remove directory recursively
rm -rf directory        Force remove directory
```

 4.2. File Content Operations
```bash
 Text searching and filtering
grep "pattern" file.txt          Search for pattern
grep -r "pattern" /path/         Recursive search
grep -i "pattern" file.txt       Case-insensitive search
grep -n "pattern" file.txt       Show line numbers
grep -v "pattern" file.txt       Invert match

 Text processing
head -n 10 file.txt      Show first 10 lines
tail -n 10 file.txt      Show last 10 lines
tail -f file.txt         Follow file (live updates)
wc -l file.txt           Count lines
wc -w file.txt           Count words
wc -c file.txt           Count characters

 Text editing with sed
sed 's/old/new/g' file.txt       Replace text
sed '1,5d' file.txt               Delete lines 1-5
sed -n '10,20p' file.txt          Print lines 10-20
```

 5. File Permissions and Ownership

 5.1. Understanding Permissions
```bash
 View permissions
ls -la file.txt
 Output: -rw-r--r-- 1 user group 1024 Jan 1 12:00 file.txt
           ^^^ ^^^ ^^^
           |   |   |
        owner group others
        rwx rwx rwx
```

 5.2. Permission Commands
```bash
 Symbolic notation
chmod u+x file.txt        Add execute for owner
chmod g-w file.txt        Remove write for group
chmod o+r file.txt        Add read for others
chmod a+x file.txt        Add execute for all

 Numeric notation (4=read, 2=write, 1=execute)
chmod 755 file.txt        rwxr-xr-x (owner: rwx, group: r-x, others: r-x)
chmod 644 file.txt        rw-r--r-- (owner: rw, group: r, others: r)
chmod 777 file.txt        rwxrwxrwx (all permissions)
chmod 600 file.txt        rw------- (owner only)

 Directory permissions
chmod 755 directory/      Standard directory permissions
chmod 700 private_dir/    Private directory (owner only)
```

 5.3. Ownership Management
```bash
 Change owner
chown new_user file.txt
chown new_user:new_group file.txt

 Change group only
chgrp new_group file.txt

 Recursive ownership change
chown -R user:group directory/

 View ownership
ls -ln file.txt           Show numeric IDs
stat file.txt             Detailed file information
```

 6. Process Management

 6.1. Viewing Processes
```bash
 Process listing
ps                       Current user processes
ps aux                   All processes in detail
ps -ef                   All processes with full format
top                      Interactive process viewer
htop                     Enhanced top (if installed)

 Process searching
ps aux | grep python     Find Python processes
pgrep python             Find process IDs
pidof python             Find process ID of command
```

 6.2. Process Control
```bash
 Process termination
kill 1234                Graceful termination
kill -9 1234             Force termination
killall python           Kill all processes named python
pkill python             Kill processes by name

 Process signals
kill -l                  List all signals
kill -TERM 1234          Termination signal (15)
kill -KILL 1234          Kill signal (9)
kill -HUP 1234           Hangup signal (1)
```

 6.3. Background and Foreground
```bash
 Run in background
command &                Start command in background
jobs                     List background jobs
fg %1                    Bring job 1 to foreground
bg %1                    Resume job 1 in background
kill %1                  Kill background job 1

 Process control
Ctrl+C                   Interrupt foreground process
Ctrl+Z                   Suspend foreground process
```

 7. System Monitoring

 7.1. System Information
```bash
 System overview
uname -a                 System information
df -h                    Disk usage (human-readable)
free -h                  Memory usage (human-readable)
uptime                   System uptime
date                     Current date/time

 Hardware information
lscpu                    CPU information
lsblk                    Block devices
lsusb                    USB devices
lspci                    PCI devices
```

 7.2. Resource Monitoring
```bash
 Real-time monitoring
top                      Process and resource usage
htop                     Enhanced top
iotop                    I/O monitoring
nethogs                  Network usage by process

 Log monitoring
tail -f /var/log/syslog      Follow system log
journalctl -f                 Follow systemd logs
dmesg | tail                  Kernel messages
```

 8. Package Management

 8.1. APT (Debian/Ubuntu)
```bash
 Package operations
sudo apt update            Update package lists
sudo apt upgrade           Upgrade packages
sudo apt install package   Install package
sudo apt remove package    Remove package
sudo apt autoremove        Remove unused packages

 Package information
apt search package         Search for packages
apt show package           Show package details
apt list --installed       List installed packages
```

 8.2. YUM/DNF (RedHat/CentOS)
```bash
 Package operations
sudo yum update            Update packages
sudo yum install package   Install package
sudo yum remove package    Remove package
sudo yum clean all         Clean cache

 Package information
yum search package         Search packages
yum info package          Package information
```

 8.3. Homebrew (macOS)
```bash
 Package operations
brew update               Update Homebrew
brew install package      Install package
brew uninstall package    Remove package
brew cleanup              Clean up

 Package information
brew search package       Search packages
brew info package        Package information
```

 9. Text Processing Tools

 9.1. Advanced Text Processing
```bash
 Sort and unique
sort file.txt             Sort lines
sort -n file.txt          Sort numerically
sort -r file.txt          Reverse sort
uniq file.txt             Remove duplicate lines
sort file.txt | uniq      Sort and remove duplicates

 Cut and paste
cut -d':' -f1,3 file.txt    Extract fields (delimiter ':', fields 1,3)
cut -c1-10 file.txt          Extract characters 1-10
paste file1.txt file2.txt     Merge files line by line

 Join files
join -t':' file1.txt file2.txt   Join on common field
```

 9.2. AWK Examples
```bash
 AWK basics
awk '{print $1}' file.txt        Print first column
awk '{print $NF}' file.txt       Print last column
awk '{print NR, $0}' file.txt    Print line number and content

 AWK with conditions
awk '$3 > 100 {print $1, $3}' file.txt   Print if column 3 > 100
awk '/pattern/ {print}' file.txt         Print lines matching pattern

 AWK calculations
awk '{sum += $1} END {print sum}' file.txt   Sum first column
awk '{sum += $1; count++} END {print sum/count}' file.txt   Average
```

 10. Practical Examples

 10.1. File Management Workflow
```bash
 Create project structure
mkdir -p project/{src,docs,tests,data}
cd project
touch src/main.py
touch docs/README.md
touch tests/test_main.py

 Set permissions
chmod 755 src/
chmod 644 src/main.py
chmod +x src/main.py   Make executable

 Find and organize
find . -name ".py" -exec chmod +x {} \;
find . -name ".log" -delete
find . -type f -name ".tmp" -exec rm {} \;
```

 10.2. Log Analysis
```bash
 Analyze web server logs
grep "ERROR" /var/log/nginx/access.log | wc -l
grep "404" /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c | sort -nr
tail -1000 /var/log/nginx/access.log | grep "GET" | cut -d' ' -f7 | sort | uniq -c | sort -nr
```

 10.3. System Maintenance
```bash
 Disk cleanup
find /tmp -mtime +7 -delete
find /var/log -name ".log" -mtime +30 -delete
du -sh /home/ | sort -hr | head -10

 Process monitoring
ps aux | sort -rk 3,3 | head -10   Top 10 CPU processes
ps aux | sort -rk 4,4 | head -10   Top 10 memory processes
```

 11. Best Practices

 11.1. Command Line Safety
```bash
 Safe file operations
cp file.txt file.txt.backup     Backup before editing
mv file.txt file.txt.bak        Rename before deletion

 Use confirmations
rm -i file.txt                  Confirm before deletion
mv -i source dest                Confirm before overwrite

 Test commands first
echo "rm .log"                  Preview command
find . -name ".log" -type f     Find files before deletion
```

 11.2. Productivity Tips
```bash
 Command history
history | grep command           Find previous commands
Ctrl+R                           Reverse search history
!!                               Repeat last command
!123                             Repeat command 123 from history

 Aliases for common tasks
alias ll='ls -la'
alias la='ls -la'
alias grep='grep --color=auto'
alias ..='cd ..'
```

 12. Troubleshooting Common Issues

 12.1. Permission Issues
```bash
 Check permissions
ls -la file.txt
stat file.txt

 Fix permissions
chmod 644 file.txt               Standard file permissions
chmod 755 directory/             Standard directory permissions
chown user:group file.txt         Fix ownership
```

 12.2. Disk Space Issues
```bash
 Find large files
du -ah / | sort -rh | head -20
find / -type f -size +100M 2>/dev/null

 Clean up
sudo apt autoremove
sudo apt clean
rm -rf ~/.cache/
```

 12.3. Process Issues
```bash
 Find zombie processes
ps aux | awk '$8 ~ /^Z/ {print $2}'

 Kill hanging processes
kill -9 $(ps aux | grep 'process_name' | awk '{print $2}')
```

 13. Resources

 13.1. Documentation
- [Linux Command Line Guide](https://www.gnu.org/software/bash/manual/)
- [Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/FHS_3.0/)
- [Linux Permissions Guide](https://linuxize.com/post/linux-file-permissions/)

 13.2. Practice Tools
- [LinuxJourney](https://linuxjourney.com/)
- [OverTheBandit](https://overthewire.org/wargames/bandit/)
- [ExplainShell](https://explainshell.com/)

---

Next Steps: Practice these commands in your terminal environment before moving to shell scripting.
