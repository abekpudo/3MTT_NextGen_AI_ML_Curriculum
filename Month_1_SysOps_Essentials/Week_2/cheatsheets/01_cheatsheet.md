 Week 2 Cheatsheet: Linux & Bash Mastery

 🚀 Quick Reference for Command Line

---

 📁 File System Navigation

 Basic Navigation
```bash
pwd                       Print working directory
cd /path/to/dir          Change to absolute path
cd relative/path         Change to relative path
cd ..                    Go up one directory
cd ~                     Go to home directory
cd -                     Go to previous directory
cd                       Go to home (same as cd ~)
```

 Listing Files
```bash
ls                       List files
ls -l                    Long format (details)
ls -la                   Include hidden files
ls -lh                   Human readable sizes
ls -ltr                  Sort by time, reversed
ls -R                    Recursive listing
ls -S                    Sort by size
ls -X                    Sort by extension
```

 File Operations
```bash
 Create
touch file.txt           Create empty file
mkdir dirname            Create directory
mkdir -p a/b/c           Create nested directories

 Copy
cp file.txt backup.txt   Copy file
cp -r dir1 dir2          Copy directory recursively
cp -i file.txt dest/     Interactive (prompt before overwrite)

 Move/Rename
mv old.txt new.txt       Rename file
mv file.txt dest/        Move file
mv -i file.txt dest/     Interactive

 Remove
rm file.txt              Remove file
rm -i file.txt           Interactive remove
rm -r dirname            Remove directory
rm -rf dirname           Force remove directory (DANGEROUS)

 View
 cat file.txt            Display entire file
less file.txt            View with pagination (q to quit)
head file.txt            First 10 lines
head -n 20 file.txt      First 20 lines
tail file.txt            Last 10 lines
tail -f file.txt         Follow file (for logs)
```

---

 🔐 File Permissions & Ownership

 Permission Basics
```
-rwxr-xr-x   1 owner group  1234 Jan 01 12:00 file.txt
│└┬┘└┬┘└┬┘
│ │  │  └── Others permissions
│ │  └───── Group permissions
│ └──────── Owner permissions
└────────── File type (- file, d directory, l link)

r = read (4)
w = write (2)
x = execute (1)
```

 Numeric Permissions
```
7 = rwx (4+2+1)   - Full access
6 = rw- (4+2)     - Read/write
5 = r-x (4+1)     - Read/execute
4 = r-- (4)       - Read only
0 = --- (0)       - No access

Common combinations:
755 = rwxr-xr-x    Executable
644 = rw-r--r--    Standard file
700 = rwx------    Private
777 = rwxrwxrwx    Full access (DANGEROUS)
```

 Permission Commands
```bash
 Change permissions
chmod 755 file.sh            Numeric
chmod u+x file.sh            Add execute for user
chmod go-w file.txt          Remove write for group/others
chmod a+r file.txt           Add read for all
chmod -R 755 directory/      Recursive

 Change ownership
sudo chown user file.txt               Change owner
sudo chown user:group file.txt         Change owner and group
sudo chown -R user:group directory/    Recursive
sudo chgrp group file.txt              Change group only

 View permissions
ls -l file.txt               Detailed listing
stat file.txt                Full file stats
namei -l /path/to/file       Trace ownership

 Default permissions
umask                        Show current umask
umask 022                    Set umask (644 default files)
```

 Special Permissions
```bash
 SUID (Set User ID) - run as file owner
chmod u+s /usr/bin/passwd    Add SUID
chmod 4755 file              With SUID

 SGID (Set Group ID) - run as file group
chmod g+s /shared/directory  Add SGID to directory
chmod 2755 file              With SGID

 Sticky Bit - only owner can delete
chmod +t /tmp                Add sticky bit
chmod 1777 directory         With sticky bit

 View special permissions
ls -ld /tmp /usr/bin/passwd
```

---

 📂 Directory & File Management

 Directory Operations
```bash
 Create structure
mkdir -p project/{src,docs,tests}/{2023,2024}

 Find files
find . -name ".py"                     Find by name
find . -type f -size +100M              Find large files (>100MB)
find . -type d -name "node_modules"     Find directories
find . -mtime -7                        Modified in last 7 days
find . -user username                   Files owned by user
find /var/log -name ".log" -mtime +30 -delete   Delete old logs

 Locate (uses database, faster)
locate filename                         Find file anywhere
sudo updatedb                           Update locate database

 Search file contents
grep "pattern" file.txt                 Search in file
grep -r "pattern" directory/            Recursive search
grep -i "pattern" file.txt              Case insensitive
grep -n "pattern" file.txt              Show line numbers
grep -v "pattern" file.txt              Invert match
grep -E "pattern1|pattern2" file.txt   Extended regex
```

 Links
```bash
 Hard link (same inode)
ln file.txt file_hard.txt

 Symbolic link (pointer)
ln -s /path/to/file link_name
ln -s /path/to/dir dir_link

 View links
ls -li                     Show inodes
readlink link_name         Show target of symlink
```

 Disk Usage
```bash
df -h                      Disk free (human readable)
du -sh directory           Directory size summary
du -h --max-depth=1 ~      Size of each subdirectory
du -ah /path | sort -rh | head -20   Largest files
ncdu                       Interactive disk usage analyzer
```

---

 🔧 Bash Scripting

 Script Structure
```bash
!/bin/bash
 Script description

set -euo pipefail          Strict mode
 -e: exit on error
 -u: treat unset variables as error
 -o pipefail: catch errors in pipes

 Script body here
```

 Variables
```bash
 Assignment (NO spaces around =)
name="John"
age=25

 Access (use $)
echo $name
echo ${name}               Recommended syntax

 Default values
echo ${name:-"Unknown"}    Default if unset
echo ${name:="Default"}    Set default if unset

 Variable operations
length=${name}            String length
upper=${name^^}           Uppercase
lower=${name,,}           Lowercase
substring=${name:0:3}      First 3 characters

 Command substitution
date_now=$(date +%Y-%m-%d)
current_dir=`pwd`          Old syntax (avoid)

 Environment variables
echo $HOME
echo $USER
echo $PATH
echo $PWD
echo $SHELL
echo $RANDOM               Random number
```

 Special Variables
```bash
$0           Script name
$1, $2, ...  Positional arguments
$           Number of arguments
$@           All arguments (quoted)
$           All arguments (single word)
$$           Process ID
$?           Exit status of last command
$_           Last argument of previous command
```

 Input/Output
```bash
 Output
echo "Hello World"
printf "Name: %s\nAge: %d\n" "$name" "$age"

 Input
read -p "Enter name: " name
echo "Hello, $name!"

 Hide input (passwords)
read -s -p "Password: " password

 Multiple values
read -p "Enter first and last name: " first last

 Redirection
echo "text" > file.txt         Overwrite
echo "more" >> file.txt        Append
command 2> errors.log          Redirect stderr
command &> output.log          Redirect stdout and stderr
command < input.txt            Redirect stdin
command > out.log 2>&1         Combine stdout and stderr
```

 Conditionals
```bash
 If statement
if [ "$age" -gt 18 ]; then
    echo "Adult"
fi

 If-else
if [ -f "$file" ]; then
    echo "File exists"
else
    echo "File not found"
fi

 If-elif-else
if [ "$score" -ge 90 ]; then
    grade="A"
elif [ "$score" -ge 80 ]; then
    grade="B"
else
    grade="C"
fi

 Test operators
 Strings:
[ -z "$str" ]           Empty string
[ -n "$str" ]           Non-empty string
[ "$a" = "$b" ]         Equal
[ "$a" != "$b" ]        Not equal

 Numbers:
[ "$a" -eq "$b" ]       Equal
[ "$a" -ne "$b" ]       Not equal
[ "$a" -gt "$b" ]       Greater than
[ "$a" -ge "$b" ]       Greater or equal
[ "$a" -lt "$b" ]       Less than
[ "$a" -le "$b" ]       Less or equal

 Files:
[ -e "$file" ]          Exists
[ -f "$file" ]          Regular file
[ -d "$file" ]          Directory
[ -r "$file" ]          Readable
[ -w "$file" ]          Writable
[ -x "$file" ]          Executable
[ -s "$file" ]          Non-zero size
[ "$a" -nt "$b" ]       Newer than
[ "$a" -ot "$b" ]       Older than

 Case statement
case "$variable" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart)
        echo "Restarting..."
        ;;
    )
        echo "Unknown command"
        ;;
esac
```

 Loops
```bash
 For loop (list)
for item in apple banana cherry; do
    echo "Fruit: $item"
done

 For loop (range)
for i in {1..10}; do
    echo "Number: $i"
done

 For loop (C-style)
for ((i=0; i<10; i++)); do
    echo "Count: $i"
done

 While loop
counter=0
while [ $counter -lt 10 ]; do
    echo "Counter: $counter"
    ((counter++))
done

 Until loop
until [ $counter -eq 0 ]; do
    echo "Countdown: $counter"
    ((counter--))
done

 Loop over files
for file in .txt; do
    echo "Processing: $file"
done

 Read file line by line
while IFS= read -r line; do
    echo "$line"
done < file.txt

 Break and continue
for i in {1..100}; do
    [ $i -eq 50 ] && break       Exit loop
    [ $((i % 2)) -eq 0 ] && continue   Skip even numbers
    echo $i
done
```

 Functions
```bash
 Function definition
greet() {
    echo "Hello, $1!"
}

 Function with return value
add() {
    return $(($1 + $2))
}

 Call functions
greet "World"
add 5 3
echo "Sum: $?"               $? contains return value

 Better: echo result
add() {
    echo $(($1 + $2))
}
result=$(add 5 3)
echo "Sum: $result"

 Local variables
process() {
    local var="local value"    Only available in function
    global="global value"      Available everywhere
}
```

---

 📊 Text Processing

 grep - Pattern Search
```bash
 Basic usage
grep "pattern" file.txt

 Common options
-i     Case insensitive
-v     Invert match (exclude)
-n     Show line numbers
-r     Recursive
-l     Show only filenames
-c     Count matches
-w     Match whole words
-E     Extended regex
-A 3   Show 3 lines after match
-B 3   Show 3 lines before match
-C 3   Show 3 lines before and after

 Examples
grep -r "TODO" src/                Find TODOs
grep -E "error|warning" log.txt     Multiple patterns
grep -v "^" config.txt            Remove comment lines
ps aux | grep python                Filter processes
```

 sed - Stream Editor
```bash
 Substitution
sed 's/old/new/' file.txt          Replace first occurrence per line
sed 's/old/new/g' file.txt         Replace all occurrences
sed 's/old/new/2' file.txt         Replace 2nd occurrence
sed 's/old/new/gi' file.txt        Case insensitive
sed 's|/usr/local|/opt|g' file.txt  Different delimiter

 Delete lines
sed '5d' file.txt                  Delete line 5
sed '2,10d' file.txt               Delete lines 2-10
sed '/pattern/d' file.txt          Delete matching lines
sed '/^$/d' file.txt               Delete empty lines

 Print specific lines
sed -n '5p' file.txt               Print line 5
sed -n '2,10p' file.txt            Print lines 2-10

 In-place editing
sed -i 's/old/new/g' file.txt      Edit file in place
sed -i.bak 's/old/new/g' file.txt  Create backup

 Multiple commands
sed -e 's/foo/bar/' -e 's/baz/qux/' file.txt
```

 awk - Text Processing
```bash
 Print columns
awk '{print $1}' file.txt          First column
awk '{print $1, $3}' file.txt       Columns 1 and 3
awk '{print $NF}' file.txt           Last column

 With delimiter
awk -F: '{print $1}' /etc/passwd     Use : as delimiter
awk -F',' '{print $2}' file.csv      CSV processing

 Conditions
awk '/pattern/ {print}' file.txt    Lines matching pattern
awk '$3 > 100 {print}' file.txt      Where column 3 > 100
awk 'NR==10 {print}' file.txt        Line 10 only

 Calculations
awk '{sum+=$1} END {print sum}' file.txt      Sum of column 1
awk '{sum+=$1; count++} END {print sum/count}' file.txt   Average

 Format output
awk '{printf "%-10s %5d\n", $1, $2}' file.txt

 BEGIN/END blocks
awk 'BEGIN {print "Header"} {print $0} END {print "Footer"}' file.txt
```

 cut - Field Extraction
```bash
cut -d' ' -f1 file.txt             Cut by space delimiter, field 1
cut -d',' -f2,4 file.csv           Fields 2 and 4 from CSV
cut -c1-10 file.txt                Characters 1-10
cut -c1-10,20-30 file.txt          Multiple ranges
```

 Other Text Tools
```bash
 Sort
sort file.txt                      Alphabetical sort
sort -n file.txt                   Numeric sort
sort -r file.txt                   Reverse sort
sort -k2 file.txt                  Sort by 2nd column
sort -t',' -k3 -n file.csv         Sort CSV by 3rd column numerically

 Unique
sort file.txt | uniq               Remove duplicates
sort file.txt | uniq -c            Count occurrences
sort file.txt | uniq -d            Show only duplicates

 Count
wc file.txt                        Lines, words, bytes
wc -l file.txt                     Lines only
wc -w file.txt                     Words only
ls | wc -l                         Count files

 Translate
tr 'a-z' 'A-Z' < file.txt          Lower to upper
tr -d '0-9' < file.txt             Delete digits
tr -s ' ' < file.txt               Squeeze spaces

 Compare
diff file1.txt file2.txt           Compare files
comm file1.txt file2.txt           Compare sorted files
```

---

 ⚙️ Process Management

 Viewing Processes
```bash
ps aux                     All processes detailed
ps aux | grep python       Find Python processes
ps -ef                     Full format listing
ps aux --sort=-%mem        Sort by memory usage
ps aux --sort=-%cpu        Sort by CPU usage
pstree                     Process tree view
pgrep process_name         Get process ID by name
pgrep -l process_name      With name
pidof process_name         Get PID
```

 Process Control
```bash
top                        Interactive process viewer (q to quit)
htop                       Enhanced top (if installed)

 Kill processes
kill PID                   Terminate gracefully
kill -9 PID                Force kill
killall process_name       Kill by name
pkill pattern              Kill matching processes

 Nice (priority)
nice -n 10 command         Run with lower priority
renice -n 5 -p PID         Change priority

 Background/Foreground
command &                  Run in background
jobs                       List background jobs
fg %1                      Bring job 1 to foreground
bg %1                      Resume job 1 in background
Ctrl+Z                     Suspend foreground job
```

 System Information
```bash
uname -a                   Kernel info
uname -r                   Kernel release
lsb_release -a             Distribution info (Ubuntu/Debian)
cat /etc/os-release        OS info (universal)
uptime                     System uptime
whoami                     Current user
who                        Logged in users
w                          Who and what they're doing
last                       Login history
```

---

 💾 System Monitoring

 Memory
```bash
free -h                    Memory usage (human readable)
free -m                    In megabytes
cat /proc/meminfo          Detailed memory info
vmstat 1 5                 Virtual memory stats (5 samples, 1 sec apart)
```

 Disk
```bash
df -h                      Disk free (human readable)
df -i                      Inode usage
du -sh /path               Directory size
du -ah /path | sort -rh | head -10   Top 10 largest
```

 CPU
```bash
cat /proc/cpuinfo          CPU details
nproc                      Number of CPUs
lscpu                      CPU architecture
mpstat                     CPU stats (if installed)
sar -u 1 5                 CPU utilization
```

 Network
```bash
ifconfig                   Network interfaces (legacy)
ip addr                    Modern alternative
ip link                    Interface status
ss -tuln                   Listening ports
netstat -tuln              Legacy alternative
ping -c 4 google.com       Test connectivity
traceroute google.com      Trace route
curl -I https://google.com  HTTP headers
wget https://example.com/file.txt   Download file
```

 Logs
```bash
tail -f /var/log/syslog    Follow system log (Ubuntu/Debian)
tail -f /var/log/messages  Follow system log (CentOS/RHEL)
journalctl                 Systemd logs
journalctl -u service      Service specific logs
journalctl -f              Follow logs
journalctl --since "1 hour ago"
dmesg | tail               Kernel messages
```

---

 ⏰ Scheduling & Automation

 Cron Jobs
```bash
crontab -l                 List cron jobs
crontab -e                 Edit cron jobs
crontab -r                 Remove all cron jobs
```

 Cron Syntax
```
     command
│ │ │ │ │
│ │ │ │ └── Day of week (0-7, 0=Sunday)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)

Special strings:
@reboot       Run at startup
@yearly       Once a year (0 0 1 1 )
@monthly      Once a month (0 0 1  )
@weekly       Once a week (0 0   0)
@daily        Once a day (0 0   )
@hourly       Once an hour (0    )
```

 Cron Examples
```bash
 Every 5 minutes
/5     /path/to/script.sh

 Every day at 2:30 AM
30 2    /path/to/backup.sh

 Every Monday at 8 AM
0 8   1 /path/to/weekly-report.sh

 Every hour during business hours
0 9-17   1-5 /path/to/work-hours-task.sh

 Redirect output
     /path/to/script.sh >> /var/log/script.log 2>&1
```

 At (One-time scheduling)
```bash
atq                        List pending jobs
at now + 1 hour            Run in 1 hour
at 2:00 PM tomorrow        Specific time
echo "command" | at now + 5 minutes
atrm 1                     Remove job 1
```

---

 📦 Package Management

 APT (Ubuntu/Debian)
```bash
sudo apt update            Update package lists
sudo apt upgrade           Upgrade all packages
sudo apt install pkg       Install package
sudo apt remove pkg        Remove package
sudo apt purge pkg         Remove with config files
sudo apt autoremove        Remove unused dependencies
apt search term            Search packages
apt show pkg               Package details
apt list --installed       List installed packages
dpkg -l | grep pkg         Check if installed
```

 YUM/DNF (CentOS/RHEL/Fedora)
```bash
sudo yum update            Update packages
sudo yum install pkg       Install
sudo yum remove pkg        Remove
sudo yum search term       Search
sudo yum info pkg          Package info
yum list installed         List installed
```

 Brew (macOS)
```bash
brew update                Update formulae
brew upgrade               Upgrade packages
brew install pkg           Install
brew uninstall pkg         Remove
brew search term           Search
brew list                  List installed
brew services start pkg    Start service
```

---

 🛠️ Useful One-Liners

 File Operations
```bash
 Find and delete empty directories
find . -type d -empty -delete

 Find and remove files older than 30 days
find /path -type f -mtime +30 -delete

 Count files by extension
find . -type f | sed 's/.\.//' | sort | uniq -c | sort -rn

 Create backup with timestamp
cp file.txt "file_$(date +%Y%m%d_%H%M%S).txt"

 Replace text in multiple files
sed -i 's/old/new/g' .txt
```

 System Info
```bash
 Quick system overview
uname -a && lsb_release -a && uptime

 Top 10 memory-consuming processes
ps aux --sort=-%mem | head -11

 Top 10 CPU-consuming processes
ps aux --sort=-%cpu | head -11

 Network connections
ss -tuln | grep LISTEN
```

 Text Processing
```bash
 Extract emails from file
grep -E -o '\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b' file.txt

 Extract URLs
grep -E -o 'https?://[^ ]+' file.txt

 CSV to JSON (requires jq)
cat file.csv | python3 -c "import csv,json,sys; print(json.dumps(list(csv.DictReader(sys.stdin))))"

 Word frequency
cat file.txt | tr ' ' '\n' | sort | uniq -c | sort -rn | head -20
```

---

 🐛 Debugging & Troubleshooting

 Script Debugging
```bash
bash -x script.sh          Trace execution
bash -v script.sh          Print input lines
set -x                     Enable tracing in script
set +x                     Disable tracing
```

 Common Error Messages
```bash
 Permission denied
chmod +x script.sh         Make executable
sudo command               Run as root

 Command not found
which command              Check if in PATH
export PATH=$PATH:/new/path   Add to PATH
apt install package        Install missing package

 No such file or directory
ls -la /path               Verify path exists
file filename              Check file type
pwd                        Confirm location
```

 Log Analysis
```bash
 View last N lines
tail -n 100 /var/log/syslog

 View first N lines
head -n 50 /var/log/syslog

 Search in logs
grep "error" /var/log/syslog
grep -E "error|fail|critical" /var/log/syslog

 Real-time monitoring
tail -f /var/log/syslog | grep "pattern"
```

---

 🎯 Quick Reference Tables

 Exit Codes
| Code | Meaning |
|------|---------|
| 0    | Success |
| 1    | General error |
| 2    | Misuse of shell builtins |
| 126  | Command invoked cannot execute |
| 127  | Command not found |
| 128  | Invalid argument to exit |
| 130  | Script terminated by Ctrl+C |
| 137  | Process killed (SIGKILL) |
| 255  | Exit status out of range |

 File Test Operators
| Operator | Description |
|----------|-------------|
| -e file  | Exists |
| -f file  | Regular file |
| -d file  | Directory |
| -s file  | Non-zero size |
| -r file  | Readable |
| -w file  | Writable |
| -x file  | Executable |

---

Previous: [Week 2 README ←](../README.md) | Next: [Week 3 Cheatsheet →](../Week_3/cheatsheets/01_cheatsheet.md)
