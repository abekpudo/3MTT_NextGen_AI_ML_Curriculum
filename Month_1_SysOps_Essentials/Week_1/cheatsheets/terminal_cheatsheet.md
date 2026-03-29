 Command Line & Terminal Cheatsheet

 Basics

```bash
 Print current directory
pwd

 List files and folders
ls                           Basic list
ls -l                        Detailed list
ls -la                       Show hidden files
ls -lah                      Human-readable sizes
ls -R                        Recursive listing
ls -S                        Sort by size

 Change directory
cd ~                         Home directory
cd /                         Root directory
cd ..                        Parent directory
cd -                         Previous directory
```

 File Operations

```bash
 Create files
touch filename.txt           Create empty file
cat > file.txt << 'EOF'      Create with content
 type content
 EOF

 Create directories
mkdir folder                 Create folder
mkdir -p a/b/c               Create nested folders

 Copy
cp file.txt file_copy.txt    Copy file
cp -r folder folder_copy     Copy folder

 Move/Rename
mv old.txt new.txt           Rename
mv file.txt folder/          Move to folder

 Delete
rm file.txt                  Delete file (careful!)
rm -r folder                 Delete folder (careful!)
rm -f file.txt               Force delete

 View files
cat filename.txt             Display entire file
less filename.txt            Scroll through file (q to quit)
head -n 10 filename.txt      First 10 lines
tail -n 10 filename.txt      Last 10 lines
```

 Search & Find

```bash
 Search within files
grep "pattern" file.txt      Find pattern in file
grep -i "pattern" file.txt   Case insensitive
grep -r "pattern" .          Search recursively

 Find files
find . -name "*.py"          Find Python files
find . -type f -name "*.txt"  Find all text files
find . -type d -name "*test*"  Find directories

 Search in history
history                      View command history
history | grep "git"         Search history
```

 Working with Paths

```bash
 Absolute paths start with /
/Users/godslove/Documents/file.txt

 Relative paths are relative to current location
../folder/file.txt           Go up one level
./file.txt                   Current directory
folder/subfolder/file.txt    Navigate down

 Shortcuts
~                            Home directory
.                            Current directory
..                           Parent directory
```

 File Permissions

```bash
 View permissions
ls -l

 Change permissions (Owner, Group, Others)
chmod 755 script.sh          Make executable

 Make file executable
chmod +x script.sh

 Change owner
sudo chown user:group file.txt
```

 System Information

```bash
 Who are you?
whoami                       Current user
id                           User ID info

 System info
uname -a                     System information
df -h                        Disk space
du -sh folder                Folder size
top                          Running processes

 Environment variables
echo $PATH                   Show PATH
env                          All variables
export VAR="value"           Set variable
```

 Redirects & Pipes

```bash
 Redirect output
ls > files.txt               Save to file
ls >> files.txt              Append to file
ls 2> errors.txt             Save errors
ls > output.txt 2>&1         Save both

 Pipe output to another command
ls | grep ".py"              Pipe to grep
cat file.txt | wc -l         Count lines
ps aux | grep python         Find processes
```

 Useful Commands

```bash
 Count
wc -l file.txt               Count lines
wc -w file.txt               Count words
ls | wc -l                   Count items

 Sort & unique
sort file.txt                Sort lines
sort -u file.txt             Sort, remove duplicates
uniq file.txt                Remove consecutive duplicates

 Text processing
sed 's/old/new/g' file.txt   Replace text
awk '{print $1}' file.txt    Print column 1

 Compare files
diff file1.txt file2.txt     Show differences
```

 Shortcuts

```bash
 Keyboard shortcuts
Ctrl + C                     Cancel command
Ctrl + L                     Clear screen
Ctrl + A                     Beginning of line
Ctrl + E                     End of line
Ctrl + R                     Search history
Tab                          Autocomplete
!!                           Last command
!$                           Last argument
```

 Practical Examples

```bash
 Create project structure
mkdir -p ~/project/{src,tests,docs}
cd ~/project
touch README.md

 Find all Python files
find . -name "*.py"

 Count lines of code
find . -name "*.py" | xargs wc -l

 Create backup
cp -r project project.backup

 Remove all .pyc files
find . -name "*.pyc" -delete

 Search for pattern in all files
grep -r "TODO" .

 Show top 10 largest files
ls -lhS | head -n 10

 Check if directory exists
if [ -d "folder" ]; then echo "exists"; fi

 List files modified today
find . -type f -newermt "today"

 Monitor directory changes
watch -n 2 'ls -la'
```

 zsh Specific (macOS Default)

```bash
 Oh-my-zsh is popular
 Install: sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

 Oh-my-zsh themes
ZSH_THEME="robbyrussell"     Edit ~/.zshrc

 Useful plugins
 Edit ~/.zshrc and add to plugins:
plugins=(git python pip venv)
```

 Environment

```bash
 Python environment
python3 --version            Check Python version
which python3                Where is Python?
python3 -m venv venv         Create venv
source venv/bin/activate     Activate venv
deactivate                   Deactivate venv

 pip (Python package manager)
pip list                     List packages
pip install package          Install
pip install -r requirements.txt   Install from file
pip freeze > requirements.txt     Generate requirements
```

 Tips

- Use `Tab` for autocomplete
- Use `Ctrl+R` to search command history
- Use aliases for common commands
- Learn to use pipes `|` to combine commands
- Read manual pages: `man command`
- Most commands have help: `command --help`

---

 Common Errors & Solutions

| Error | Cause | Solution |
|-------|-------|----------|
| `command not found` | Command doesn't exist or not in PATH | Check spelling, install program |
| `Permission denied` | Don't have execute permission | `chmod +x file` |
| `No such file or directory` | File doesn't exist | Check path with `pwd`, `ls` |
| `Directory not empty` | Can't remove non-empty dir | Use `rm -r` |

---

 Advanced Patterns

```bash
 Loop through files
for file in *.py; do
  echo "Processing $file"
done

 Check file exists
if [ -f "file.txt" ]; then
  cat file.txt
fi

 Get current date
date +"%Y-%m-%d"

 Create timestamp backup
cp file.txt file.txt.$(date +%s)
```
