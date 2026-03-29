 Linux & Bash Cheatsheet

 Basic Navigation

```bash
pwd                          Print working directory
cd directory                 Change directory
cd ..                        Go to parent directory
cd ~                         Go to home directory
ls                           List files
ls -la                       List all with details
ls -lh                       Human-readable sizes
```

 File Operations

```bash
 Create
touch filename               Create empty file
mkdir dirname                Create directory
mkdir -p a/b/c               Create nested directories

 Copy
cp source dest               Copy file
cp -r source_dir dest_dir    Copy directory

 Move/Rename
mv old new                   Move or rename
mv file directory/           Move to directory

 Delete
rm file                      Delete file
rm -r directory              Delete directory
rmdir directory              Remove empty directory

 View
cat file                     Display file
less file                    Page through file
head -n 10 file              First 10 lines
tail -n 20 file              Last 20 lines
```

 File Permissions

```bash
chmod 755 file               Make executable
chmod 644 file               Make read-only
chmod +x file                Add execute permission
chmod -w file                Remove write permission

chown user file              Change owner
chgrp group file             Change group
chown user:group file        Change both
```

 Search & Find

```bash
grep "pattern" file          Search in file
grep -i "pattern" file       Case insensitive
grep -r "pattern" .          Recursive search
find . -name "*.txt"         Find files
find . -type f -size +1M     Find by size
```

 Text Processing

```bash
 sed (stream editor)
sed 's/old/new/' file        Replace first
sed 's/old/new/g' file       Replace all
sed '5d' file                Delete line 5
sed -i 's/old/new/g' file    Edit in place

 awk (pattern scanning)
awk '{print $1}' file        Print first column
awk -F',' '{print $2}' file  Use comma as delimiter
awk '{sum += $1} END {print sum}' file   Sum column

 sort and unique
sort file                    Sort lines
sort -u file                 Sort and remove duplicates
uniq file                    Remove consecutive duplicates
wc -l file                   Count lines
```

 System Information

```bash
uname -a                     System information
whoami                       Current user
groups                       User's groups
df -h                        Disk space
du -sh directory             Directory size
ps aux                       List processes
top                          Process monitor
free -h                      Memory usage
```

 Bash Scripting Essentials

 Variables
```bash
VAR="value"                  Assign variable
echo $VAR                    Use variable
VAR=$((VAR + 1))            Arithmetic
VAR=$(command)               Command substitution
```

 Conditionals
```bash
if [ $var -eq 5 ]; then
    echo "Equal"
elif [ $var -lt 5 ]; then
    echo "Less than"
else
    echo "Greater than"
fi

[ -f file ]                  File exists
[ -d directory ]             Directory exists
[ -z "$var" ]                Variable is empty
[ "$var1" = "$var2" ]        Strings equal
```

 Loops
```bash
for i in {1..5}; do
    echo $i
done

for file in *.txt; do
    echo $file
done

while [ $i -lt 10 ]; do
    echo $i
    i=$((i + 1))
done
```

 Functions
```bash
my_function() {
    local var="local value"
    echo "Function output"
    return 0
}

my_function               Call function
```

 Common Patterns

 Connect to remote server
```bash
ssh user@host
ssh -i keyfile user@host     With key
ssh -p 2222 user@host        Custom port
```

 Copy files from remote
```bash
scp user@host:/path/file .
scp -r user@host:/path/dir .
```

 Background/Foreground
```bash
command &                    Run in background
jobs                         List background jobs
fg %1                        Bring to foreground
Ctrl+Z                       Suspend process
bg                           Resume in background
```

 Pipes and Redirection
```bash
command > file               Redirect to file
command >> file              Append to file
command < file               Read from file
command 2> errors.txt        Redirect errors
command1 | command2          Pipe output
```

 Environment Variables

```bash
export VAR="value"           Set environment variable
echo $PATH                   Show PATH
echo $HOME                   Show home directory
echo $USER                   Show username
env                          Show all variables
```

 Package Management

 macOS (Homebrew)
```bash
brew install package
brew update
brew upgrade package
brew uninstall package
```

 Linux (apt - Debian/Ubuntu)
```bash
sudo apt update
sudo apt install package
sudo apt upgrade
sudo apt remove package
```

 Linux (yum - RedHat/CentOS)
```bash
sudo yum install package
sudo yum update
sudo yum remove package
```

 Useful Commands

```bash
alias                        List aliases
alias ll='ls -la'           Create alias
history                      Command history
history | grep "search"      Search history
date                         Current date
cal                          Calendar
man command                  Manual pages
which command                Location of command
```

 Tips

- Use `Tab` for autocomplete
- Use `Ctrl+R` for history search
- Use `Ctrl+L` to clear screen
- Use `!!` to repeat last command
- Use `!$` to use last argument
- Read man pages: `man command`
