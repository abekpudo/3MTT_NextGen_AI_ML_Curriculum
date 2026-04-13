 Exercise 1: File System Navigation & Management

 🎯 Objective
Master Linux file system navigation, file operations, and directory management through hands-on practice.

---

 📋 Part 1: Directory Structure Creation

 Task 1.1: Create Complex Directory Structure
Create the following directory hierarchy:
```
~/linux-mastery/
├── projects/
│   ├── web/
│   │   ├── frontend/
│   │   ├── backend/
│   │   └── database/
│   └── mobile/
│       ├── android/
│       └── ios/
├── documents/
│   ├── work/
│   │   ├── 2023/
│   │   └── 2024/
│   └── personal/
├── scripts/
│   ├── bash/
│   └── python/
└── logs/
```

Commands to use:
```bash
 Create structure in one command
mkdir -p ~/linux-mastery/{projects/{web/{frontend,backend,database},mobile/{android,ios}},documents/{work/{2023,2024},personal},scripts/{bash,python},logs}

 Verify creation
tree ~/linux-mastery
 or
find ~/linux-mastery -type d
```

---

 Task 1.2: Navigate and Explore
Practice navigation commands:

```bash
 Start from home
cd ~

 Navigate to different directories using absolute paths
cd ~/linux-mastery/projects/web/frontend
pwd                            Verify location
cd ~/linux-mastery/scripts/bash
pwd

 Navigate using relative paths
cd ~/linux-mastery/projects/web
cd backend                     Relative to current
cd ../frontend                 Go up, then to frontend
cd ../../mobile/android        Multiple levels
cd ~                           Back to home

 Navigate using shortcuts
cd ~/linux-mastery
cd logs                        Subdirectory
cd ..                          Parent directory
cd -                           Previous directory
cd                             Home directory
```

---

 📋 Part 2: File Creation and Manipulation

 Task 2.1: Create Files in Each Directory

```bash
cd ~/linux-mastery

 Create files with touch
touch projects/web/frontend/index.html
touch projects/web/backend/server.js
touch projects/web/database/schema.sql

 Create files with content
echo "Frontend README" > projects/web/frontend/README.md
echo "Backend API Docs" > projects/web/backend/API.md
echo "Database Schema" > projects/web/database/README.md

 Create multiple files at once
touch documents/work/2023/{report1.md,report2.md,summary.md}
touch documents/work/2024/{q1-report.md,q2-plan.md}

 Create files with specific content
cat > documents/personal/notes.txt << 'EOF'
Personal Notes
==============

Learning Linux file management.
Practice makes perfect!
EOF
```

 Task 2.2: Practice Copy Operations

```bash
cd ~/linux-mastery

 Copy single file
cp documents/personal/notes.txt documents/work/2023/

 Copy with new name
cp documents/personal/notes.txt documents/work/2023/notes-backup.txt

 Copy multiple files
cp documents/work/2023/.md documents/work/2024/

 Copy directory recursively
cp -r projects/web/frontend projects/web/frontend-backup

 Interactive copy (prompt before overwrite)
cp -i documents/personal/notes.txt documents/work/2023/

 Preserve attributes
cp -p documents/personal/notes.txt documents/work/preserved-copy.txt
```

 Task 2.3: Practice Move Operations

```bash
cd ~/linux-mastery

 Move file
cd documents/work/2023
mv report1.md ../2024/

 Move and rename
cd ~/linux-mastery
mv documents/work/2023/report2.md documents/work/2024/archived-report.md

 Move multiple files
mv projects/web/frontend-backup/ projects/web/frontend/
rmdir projects/web/frontend-backup   Remove empty directory

 Interactive move
mv -i documents/work/2023/summary.md documents/work/2024/
```

---

 📋 Part 3: File Viewing and Inspection

 Task 3.1: View File Contents

```bash
cd ~/linux-mastery

 Display full content
cat documents/personal/notes.txt

 View with pagination
cat projects/web/backend/server.js | less

 View first lines
head documents/personal/notes.txt
head -n 5 documents/work/2024/.md

 View last lines
tail logs/   Check if any logs exist
tail -f /var/log/syslog   Follow system log (Ctrl+C to exit)

 View specific lines
sed -n '2,4p' documents/personal/notes.txt   Lines 2-4
```

 Task 3.2: File Information

```bash
cd ~/linux-mastery

 Detailed listing
ls -la projects/web/

 Human readable sizes
ls -lh documents/work/2023/

 Sort by modification time
ls -lt projects/web/backend/

 Sort by size
ls -lSh documents/work/

 File type information
file documents/personal/notes.txt
file projects/web/backend/server.js

 Detailed file stats
stat documents/personal/notes.txt
```

---

 📋 Part 4: File Searching

 Task 4.1: Find Files

```bash
cd ~/linux-mastery

 Find by name
find . -name ".md"
find . -name "README"

 Find by type
find . -type d -name "backup"
find . -type f -name ".txt"

 Find by size
find . -type f -size +1k      Larger than 1KB
find . -type f -size -10k     Smaller than 10KB

 Find by modification time
find . -type f -mtime -1      Modified in last day
find . -type f -mtime +7      Modified more than 7 days ago

 Find and execute command
find . -name ".md" -exec ls -lh {} \;
find . -name ".txt" -exec wc -l {} \;
```

 Task 4.2: Search File Contents

```bash
cd ~/linux-mastery

 Search in files
grep "README" documents/work/2023/.md
grep -r "practice" .   Recursive search

 Case insensitive search
grep -i "notes" documents/personal/

 Show line numbers
grep -n "Linux" documents/personal/notes.txt

 Count matches
grep -c "README" documents/work/2024/.md

 Invert match (exclude)
grep -v "^" documents/personal/notes.txt   Exclude comments
```

---

 📋 Part 5: Cleanup and Organization

 Task 5.1: Remove Files Safely

```bash
cd ~/linux-mastery

 Remove single file (use -i for interactive)
rm -i documents/work/2023/notes.txt

 Remove multiple files
rm documents/work/2023/.md

 Remove empty directory
rmdir documents/work/2023   Only works if empty

 Remove directory and contents (USE WITH CAUTION)
rm -r projects/web/frontend-backup 2>/dev/null || true

 Remove files matching pattern
rm documents/work/2024/.md
```

 Task 5.2: Create Symbolic Links

```bash
cd ~/linux-mastery

 Create symlink to file
ln -s documents/personal/notes.txt quick-notes

 Create symlink to directory
ln -s projects/web web-projects

 Verify symlinks
ls -la quick-notes
ls -la web-projects

 Test symlinks
cat quick-notes
cd web-projects && ls

 Remove symlinks (removes link, not target)
rm quick-notes
rm web-projects
```

---

 📋 Part 6: Create Navigation Script

 Task 6.1: Write Interactive Navigator

Create `~/linux-mastery/scripts/bash/navigator.sh`:

```bash
!/bin/bash

 File System Navigator Script
 Provides interactive navigation and file operations

show_menu() {
    clear
    echo "================================"
    echo "   FILE SYSTEM NAVIGATOR"
    echo "================================"
    echo "Current Directory: $(pwd)"
    echo ""
    echo "1. List files (detailed)"
    echo "2. Change directory"
    echo "3. View file content"
    echo "4. Create new file"
    echo "5. Create new directory"
    echo "6. Search for files"
    echo "7. Show disk usage"
    echo "8. Go to home directory"
    echo "0. Exit"
    echo "================================"
}

list_files() {
    echo "Files in current directory:"
    ls -la
    read -p "Press Enter to continue..."
}

change_directory() {
    read -p "Enter directory path: " path
    if [ -d "$path" ]; then
        cd "$path" && echo "Changed to: $(pwd)"
    else
        echo "Directory not found: $path"
    fi
    read -p "Press Enter to continue..."
}

view_file() {
    read -p "Enter filename: " filename
    if [ -f "$filename" ]; then
        if [ "$(wc -l < "$filename")" -gt 50 ]; then
            less "$filename"
        else
            cat "$filename"
        fi
    else
        echo "File not found: $filename"
    fi
    read -p "Press Enter to continue..."
}

create_file() {
    read -p "Enter filename: " filename
    read -p "Enter content (or press Enter for empty file): " content
    if [ -z "$content" ]; then
        touch "$filename"
    else
        echo "$content" > "$filename"
    fi
    echo "Created: $filename"
    read -p "Press Enter to continue..."
}

create_directory() {
    read -p "Enter directory name: " dirname
    mkdir -p "$dirname"
    echo "Created directory: $dirname"
    read -p "Press Enter to continue..."
}

search_files() {
    read -p "Enter search pattern: " pattern
    echo "Searching for: $pattern"
    find . -name "$pattern" -type f 2>/dev/null
    read -p "Press Enter to continue..."
}

disk_usage() {
    echo "Disk usage for current directory:"
    du -sh . 2>/dev/null
    echo ""
    echo "Top 10 largest items:"
    du -ah . 2>/dev/null | sort -rh | head -10
    read -p "Press Enter to continue..."
}

go_home() {
    cd ~
    echo "Returned to home directory"
    read -p "Press Enter to continue..."
}

 Main loop
while true; do
    show_menu
    read -p "Enter choice [0-8]: " choice
    
    case $choice in
        1) list_files ;;
        2) change_directory ;;
        3) view_file ;;
        4) create_file ;;
        5) create_directory ;;
        6) search_files ;;
        7) disk_usage ;;
        8) go_home ;;
        0) echo "Goodbye!"; exit 0 ;;
        ) echo "Invalid choice"; sleep 1 ;;
    esac
done
```

Make executable and test:
```bash
chmod +x ~/linux-mastery/scripts/bash/navigator.sh
~/linux-mastery/scripts/bash/navigator.sh
```

---

 ✅ Verification Checklist

- [ ] Directory structure created correctly
- [ ] Files created in appropriate locations
- [ ] Copy operations successful
- [ ] Move operations successful
- [ ] File viewing commands working
- [ ] Search commands returning results
- [ ] Symlinks created and working
- [ ] Navigator script runs without errors
- [ ] All changes committed to Git

---

 📊 Deliverables

Submit the following:

1. Screenshot 1: Directory tree output (`tree ~/linux-mastery` or `find`)
2. Screenshot 2: File listings showing various operations
3. navigator.sh: Your navigation script
4. commands.log: Log of commands used (create with `script` command)

---

 💡 Tips

1. Use Tab Completion: Press Tab to complete filenames
2. Use Up Arrow: Recall previous commands
3. Use `history`: View command history
4. Use `man`: Get help for any command
5. Practice Regularly: File operations become muscle memory

---

 🎓 Extension Challenges

1. Add color coding to navigator script
2. Implement file backup functionality
3. Add recursive copy option
4. Create batch rename function
5. Add file comparison feature

---

Next: [Exercise 2: Permission Management →](./02_permission_management.md)
