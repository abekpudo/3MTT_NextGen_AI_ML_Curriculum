 Lecture 1: Linux Fundamentals

 What is Linux?

Linux is a free, open-source operating system kernel created by Linus Torvalds in 1991. It powers everything from web servers to smartphones.

---

 🎯 Learning Objectives

- Understand Linux OS architecture
- Navigate the file system
- Understand users and permissions
- Work with file permissions
- Master directory operations

---

 📚 Linux Architecture

 Kernel
The core of Linux that manages:
- Hardware resources (CPU, memory, disk)
- Process scheduling
- Memory management
- Device drivers

 Shell
Interface between user and kernel. We'll focus on Bash (Bourne Again Shell).

 File System
Hierarchical structure where everything is a file.

---

 📁 Linux File System Hierarchy

```
/                           Root directory
├── bin/                     Essential command binaries
├── etc/                     Configuration files
├── home/                    User home directories
│   ├── user1/
│   └── user2/
├── opt/                     Optional software
├── root/                    Root user home
├── tmp/                     Temporary files
├── usr/                     User programs and data
│   ├── bin/                 User command binaries
│   ├── local/               Locally installed software
│   └── share/               Shared data
├── var/                     Variable data
│   ├── log/                 Log files
│   └── tmp/                 Temporary variable data
├── dev/                     Device files
├── lib/                     System libraries
├── media/                   Mount points for media
└── proc/                    Process information
```

 Key Directories for Developers

| Directory | Purpose |
|-----------|---------|
| `~` or `$HOME` | User's home directory |
| `~/Desktop` | Desktop files |
| `~/Documents` | Documents |
| `~/Downloads` | Downloaded files |
| `/usr/bin` | User executable programs |
| `/usr/local/bin` | Locally installed programs |
| `/tmp` | Temporary files (cleared on reboot) |
| `/var/log` | System logs |
| `/etc` | System configuration |

---

 👥 Users and Groups

 User Types

1. Root User (uid 0)
   - Super user with all permissions
   - Should be used carefully
   - Use `sudo` for temporary elevation

2. System Users
   - Created for services (www-data, mysql, etc.)
   - Limited permissions

3. Regular Users
   - Normal user accounts
   - Limited to own files and directories

 User Management

```bash
 Current user
whoami                       Show username
id                           Show user and group IDs
groups                       Show groups for current user

 User information
finger username              User information
who                          Logged in users
last                         Login history

 Switch user
su username                  Switch user (with password)
sudo command                 Run with elevated privileges
sudo su                      Become root

 Add user (admin only)
sudo useradd username
sudo adduser username        Interactive

 Change password
passwd
passwd username              Admin can change others' passwords

 Remove user
sudo userdel username
```

---

 🔐 File Permissions

 Understanding Permissions

```
drwxr-xr-x  5  user  group  160  Mar 15 10:30  folder/
^          ^  ^     ^      ^    ^               ^
|          |  |     |      |    |               |
type       |  owner group  size date             name
           |
        permissions

Permissions: 755
            |||
            ||| 
    rwx r-x r-x
Owner Group Other
```

 Permission Breakdown

Three types of permissions:
- r (read): 4 - Can read file contents or list directory
- w (write): 2 - Can modify file or create/delete in directory
- x (execute): 1 - Can run file as program or enter directory

Three categories:
- Owner (first 3): User who owns the file
- Group (middle 3): Users in the file's group
- Other (last 3): Everyone else

 Examples

```
755 = rwxr-xr-x
  Owner: read + write + execute = 4+2+1 = 7
  Group: read + execute = 4+1 = 5
  Other: read + execute = 4+1 = 5
  Typical for executable programs and directories

644 = rw-r--r--
  Owner: read + write = 4+2 = 6
  Group: read = 4
  Other: read = 4
  Typical for regular files

600 = rw-------
  Owner: read + write = 4+2 = 6
  Group: nothing = 0
  Other: nothing = 0
  Sensitive files (keys, passwords)

700 = rwx------
  Owner: read + write + execute = 7
  Group: nothing = 0
  Other: nothing = 0
  Private executable directories
```

 Changing Permissions

```bash
 Numeric method
chmod 755 file.txt           Set to 755
chmod 644 script.sh          Set to 644

 Symbolic method
chmod +x script.sh           Add execute permission
chmod -w file.txt            Remove write permission
chmod u+rwx file.txt         User: add read, write, execute
chmod g-w file.txt           Group: remove write
chmod o-rwx file.txt         Others: remove all

 Recursive (for directories)
chmod -R 755 folder/         Change folder and all contents
```

 Changing Ownership

```bash
 Change owner
chown newuser file.txt

 Change owner and group
chown newuser:newgroup file.txt

 Change group
chgrp newgroup file.txt

 Recursive
chown -R user:group folder/
```

---

 📂 Directory Operations

 Create and Remove

```bash
 Create directory
mkdir folder

 Create nested
mkdir -p a/b/c/d

 Remove directory (must be empty)
rmdir folder

 Remove with contents (dangerous!)
rm -r folder
```

 Move and Copy

```bash
 Copy file
cp source.txt destination.txt

 Copy with contents
cp -r sourcefolder destfolder

 Copy preserving attributes
cp -p source.txt destination.txt

 Move/rename
mv oldname.txt newname.txt

 Move to different location
mv file.txt path/to/directory/
```

 List and View

```bash
 List files
ls
ls -l                        Detailed
ls -la                       Include hidden
ls -lh                       Human-readable sizes
ls -lS                       Sort by size
ls -lt                       Sort by modification time
ls -R                        Recursive

 Tree view (install if needed)
tree
tree -L 2                    Limit depth
```

---

 📝 Working with Files

 View Files

```bash
 Entire file
cat file.txt
cat file1.txt file2.txt      Multiple files

 First/last lines
head file.txt
head -n 20 file.txt          First 20 lines
tail file.txt
tail -n 50 file.txt          Last 50 lines
tail -f file.txt             Follow file (watch new lines)

 Page through
less file.txt                Space to page, q to quit
more file.txt                Simpler version

 Word and line count
wc file.txt                  Lines, words, bytes
wc -l file.txt               Lines only
```

 Create and Edit

```bash
 Create empty file
touch file.txt

 Create with content
cat > file.txt << 'EOF'
This is line 1
This is line 2
EOF

 Append to file
echo "New line" >> file.txt

 Overwrite file
echo "Replaced content" > file.txt
```

 Search and Compare

```bash
 Search within file
grep "pattern" file.txt
grep -i "pattern" file.txt   Case insensitive
grep -n "pattern" file.txt   Show line numbers
grep -r "pattern" .          Recursive search

 Compare files
diff file1.txt file2.txt

 Find files
find . -name "*.txt"
find . -type f -size +1M     Larger than 1MB
find . -mtime -1             Modified within 1 day
```

---

 🔄 Process Management

 View Processes

```bash
 List processes
ps                           Current shell processes
ps aux                       All processes
ps aux | grep python         Find specific process

 Monitor processes
top                          Interactive process monitor
top -u username              Filter by user

 Background/Foreground
command &                    Run in background
fg                           Bring to foreground
bg                           Run stopped process in background
jobs                         List background jobs
```

 Kill Processes

```bash
kill <pid>                   Terminate process
kill -9 <pid>                Force terminate
killall python               Kill all processes by name
pkill -f "pattern"           Kill by pattern
```

---

 💾 Disk and Storage

```bash
 Disk usage
df -h                        Disk space summary
du -sh folder/               Directory size
du -sh *                     Size of all items in current dir
du -sh .[^.]* *              Include hidden files

 Free memory
free -h                      Memory usage
```

---

 🔍 System Information

```bash
 OS Information
uname -a                     All system information
uname -r                     Kernel version

 Hostname
hostname
hostname -I                  IP address

 Uptime
uptime                       How long system has been running

 Date and Time
date
date +"%Y-%m-%d %H:%M:%S"    Custom format

 System load
cat /proc/loadavg
```

---

 🎓 Best Practices

1. Be careful with `rm` - There's no trash can in Linux!
2. Use `sudo` sparingly - Only when necessary
3. Organize your files - Use clear directory structure
4. Check permissions - Use `ls -l` to verify
5. Create backups - Before making changes
6. Use relative paths - When possible (more portable)

---

 ✅ What You Should Know

- [ ] Navigate file system with pwd, cd, ls
- [ ] Understand absolute vs relative paths
- [ ] Create, move, copy, delete files and directories
- [ ] Understand and change file permissions
- [ ] View and search file contents
- [ ] Monitor processes and system resources
- [ ] Understand users and groups
- [ ] Know Linux directory structure

---

 📚 Next Steps

1. Practice commands in exercises
2. Learn Bash scripting next week
3. Get comfortable with Linux command line
4. Start writing simple scripts

---

 🔗 Additional Resources

- [Linux File System Explained](https://www.linux.com/what-is-linux/)
- [File Permissions Tutorial](https://www.gnu.org/software/coreutils/manual/coreutils.htmlFile-permissions)
- [Linux Man Pages](https://man7.org/linux/man-pages/)
- [Linux Journey Tutorial](https://linuxjourney.com/)
