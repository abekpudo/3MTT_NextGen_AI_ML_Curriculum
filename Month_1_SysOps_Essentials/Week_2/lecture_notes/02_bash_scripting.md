 Lecture 2: Bash Shell Scripting

 What is Bash?

Bash (Bourne Again Shell) is a command-line interface and scripting language. Instead of typing commands one at a time, you can write scripts to automate tasks.

---

 🎯 Learning Objectives

- Write Bash scripts from scratch
- Understand variables and data types
- Use conditionals and loops
- Create functions
- Handle command-line arguments
- Understand text processing

---

 📝 Your First Bash Script

 Basic Script Structure

```bash
!/bin/bash
 This is a comment

 Script purpose:
 This script demonstrates basic Bash structure

echo "Hello, World!"
echo "Script started"

 More commands...

echo "Script completed"
```

Line 1: Shebang (`!/bin/bash`)
- Tells system this is a Bash script
- Must be on first line

Lines 2+: Comments and commands
- `` starts a comment
- Commands execute in order

 Running Scripts

```bash
 Method 1: Make executable and run directly
chmod +x myscript.sh
./myscript.sh

 Method 2: Call bash explicitly
bash myscript.sh

 Method 3: Source the script
source myscript.sh
```

---

 🔤 Variables

 Declaring Variables

```bash
 Assign variable
name="John"
age=30
path="/home/user/documents"

 No spaces around = !
 ✅ correct: name="John"
 ❌ wrong: name = "John"

 Use variables
echo "Hello, $name"
echo "You are $age years old"
echo "Path: $path"

 Concatenation
greeting="Hello, $name!"
full_path="$path/file.txt"

 Using ${} for clarity
echo "My name is ${name} and I'm ${age}"
```

 Variable Types

```bash
 Strings
name="Alice"
path="/home/user"

 Numbers (stored as strings, but treated as numbers in arithmetic)
age=25
count=100

 Arrays
fruits=("apple" "banana" "orange")
echo ${fruits[0]}                apple
echo ${fruits[@]}                all elements
echo ${fruits[@]}               number of elements

 Associative arrays (like dictionaries)
declare -A person
person["name"]="John"
person["age"]="30"
echo ${person["name"]}
```

 Environment Variables

```bash
 View environment variables
env
printenv
printenv PATH

 Common environment variables
echo $HOME                       Home directory
echo $PWD                        Current directory
echo $USER                       Current user
echo $PATH                       Executable search path
echo $SHELL                      Current shell

 Set environment variable
export MY_VAR="value"
export PATH="$PATH:/new/path"    Add to PATH
```

 Command Substitution

```bash
 Execute command and use output
current_date=$(date)
echo "Today is $current_date"

 Older syntax (still works)
current_date=`date`

 Useful examples
file_count=$(ls -1 | wc -l)
echo "Files in directory: $file_count"

user=$(whoami)
echo "Current user: $user"
```

---

 🔀 Conditionals

 if Statements

```bash
!/bin/bash

 Simple if
if [ $age -gt 18 ]; then
    echo "You are an adult"
fi

 if-else
if [ $age -lt 13 ]; then
    echo "You are a child"
else
    echo "You are not a child"
fi

 if-elif-else
if [ $age -lt 13 ]; then
    echo "Child"
elif [ $age -lt 18 ]; then
    echo "Teenager"
else
    echo "Adult"
fi
```

 Test Operators

Numeric Comparisons
```bash
[ $a -eq $b ]                Equal
[ $a -ne $b ]                Not equal
[ $a -lt $b ]                Less than
[ $a -le $b ]                Less than or equal
[ $a -gt $b ]                Greater than
[ $a -ge $b ]                Greater than or equal
```

String Comparisons
```bash
[ "$name" = "John" ]          Equal
[ "$name" != "John" ]         Not equal
[ -z "$name" ]                Empty string
[ -n "$name" ]                Non-empty string
[ "$string1" < "$string2" ]   Less than (alphabetically)
```

File Tests
```bash
[ -f "$filename" ]            File exists and is regular file
[ -d "$dirname" ]             Directory exists
[ -e "$path" ]                File or directory exists
[ -r "$filename" ]            File is readable
[ -w "$filename" ]            File is writable
[ -x "$filename" ]            File is executable
[ -s "$filename" ]            File has size > 0
```

Logical Operators
```bash
[ $a -gt 0 ] && [ $b -lt 10 ]    AND
[ $a -gt 0 ] || [ $b -lt 10 ]    OR
[ ! -f "$file" ]                 NOT
```

 Example: File Checking

```bash
!/bin/bash

filename="$1"

if [ -z "$filename" ]; then
    echo "Error: Please provide filename"
    exit 1
fi

if [ -f "$filename" ]; then
    echo "File exists"
    
    if [ -r "$filename" ]; then
        echo "File is readable"
    else
        echo "File is not readable"
    fi
    
    if [ -s "$filename" ]; then
        echo "File has content"
    else
        echo "File is empty"
    fi
else
    echo "File does not exist"
    exit 1
fi
```

---

 🔁 Loops

 for Loop

```bash
 Loop through list
for fruit in apple banana orange; do
    echo "Fruit: $fruit"
done

 Loop through array
colors=("red" "green" "blue")
for color in "${colors[@]}"; do
    echo "Color: $color"
done

 Numeric loop
for i in {1..5}; do
    echo "Number: $i"
done

 Loop with step
for i in {1..10..2}; do
    echo $i                   1, 3, 5, 7, 9
done

 Classic C-style loop
for ((i=1; i<=5; i++)); do
    echo "Count: $i"
done

 Loop through files
for file in *.txt; do
    echo "Processing $file"
done

 Loop through command output
for line in $(cat file.txt); do
    echo "Line: $line"
done
```

 while Loop

```bash
 Basic while
count=1
while [ $count -le 5 ]; do
    echo "Count: $count"
    count=$((count + 1))
done

 Infinite loop (with break)
while true; do
    read -p "Enter command (q to quit): " cmd
    
    if [ "$cmd" = "q" ]; then
        break
    fi
    
    eval $cmd
done

 Read file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < file.txt

 Continue and break
for i in {1..10}; do
    if [ $i -eq 3 ]; then
        continue             Skip 3
    fi
    
    if [ $i -eq 8 ]; then
        break                Stop at 8
    fi
    
    echo $i
done
```

---

 🔧 Functions

 Defining Functions

```bash
 Basic function
greet() {
    echo "Hello!"
}

greet                        Call function

 Function with parameters
add() {
    local a=$1               First parameter
    local b=$2               Second parameter
    local sum=$((a + b))
    echo $sum
}

result=$(add 5 3)
echo "5 + 3 = $result"

 Function with return value
is_even() {
    local num=$1
    
    if [ $((num % 2)) -eq 0 ]; then
        return 0             Success (true)
    else
        return 1             Failure (false)
    fi
}

if is_even 4; then
    echo "4 is even"
else
    echo "4 is odd"
fi

 Function with multiple operations
process_file() {
    local filename=$1
    
    if [ ! -f "$filename" ]; then
        echo "Error: File not found"
        return 1
    fi
    
    local line_count=$(wc -l < "$filename")
    echo "Processing $filename"
    echo "File has $line_count lines"
    
    return 0
}

process_file "data.txt"
```

 Local vs Global Variables

```bash
!/bin/bash

global_var="I'm global"

my_func() {
    local local_var="I'm local"
    global_var="Changed"
    
    echo "Inside function:"
    echo "  global_var: $global_var"
    echo "  local_var: $local_var"
}

my_func

echo "After function:"
echo "  global_var: $global_var"      Changed!
echo "  local_var: $local_var"        Empty (undefined)
```

---

 📥 Command-Line Arguments

 Accessing Arguments

```bash
!/bin/bash

 $0 = script name
 $1 = first argument
 $2 = second argument
 $@ = all arguments
 $ = number of arguments

echo "Script name: $0"
echo "First arg: $1"
echo "Second arg: $2"
echo "All args: $@"
echo "Number of args: $"

 Example: backup.sh
if [ $ -lt 2 ]; then
    echo "Usage: $0 <source> <destination>"
    exit 1
fi

source=$1
destination=$2

if [ -d "$source" ]; then
    cp -r "$source" "$destination"
    echo "Backup complete"
else
    echo "Error: Source directory not found"
    exit 1
fi
```

 Getopts for Options

```bash
!/bin/bash

 Script with options: script.sh -v -f file.txt

verbose=0
filename=""

while getopts "vf:" opt; do
    case $opt in
        v)
            verbose=1
            ;;
        f)
            filename="$OPTARG"
            ;;
        \?)
            echo "Invalid option: -$OPTARG"
            exit 1
            ;;
    esac
done

if [ $verbose -eq 1 ]; then
    echo "Verbose mode on"
fi

if [ -n "$filename" ]; then
    echo "Working with file: $filename"
fi
```

---

 📝 Text Processing

 grep

```bash
 Search for pattern
grep "error" logfile.txt

 Case insensitive
grep -i "ERROR" logfile.txt

 Line numbers
grep -n "pattern" file.txt

 Count matches
grep -c "pattern" file.txt

 Invert match (lines without pattern)
grep -v "pattern" file.txt

 Recursive search
grep -r "pattern" .

 Multiple patterns
grep -E "pattern1|pattern2" file.txt
```

 sed (Stream Editor)

```bash
 Replace first occurrence per line
sed 's/old/new/' file.txt

 Replace all occurrences
sed 's/old/new/g' file.txt

 Replace in specific lines
sed '2,5s/old/new/g' file.txt

 Delete lines
sed '5d' file.txt

 Delete lines matching pattern
sed '/pattern/d' file.txt

 Print specific lines
sed -n '1,5p' file.txt

 Save changes
sed -i 's/old/new/g' file.txt    -i edits in place
```

 awk

```bash
 Print specific columns
awk '{print $1}' file.txt            First column
awk '{print $1, $3}' file.txt        First and third

 Print with condition
awk '$2 > 100 {print $1}' file.txt

 Print lines matching pattern
awk '/pattern/ {print}' file.txt

 Count lines
awk 'END {print NR}' file.txt

 Calculate sum
awk '{sum += $1} END {print sum}' numbers.txt

 Custom field separator
awk -F',' '{print $2}' data.csv
```

---

 🎓 Complete Example: Backup Script

```bash
!/bin/bash

 Backup Script
 Usage: backup.sh <source_dir> <backup_dir> [compress]

set -e                              Exit on error

 Color codes for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'                         No Color

 Check arguments
if [ $ -lt 2 ]; then
    echo -e "${RED}Error: Insufficient arguments${NC}"
    echo "Usage: $0 <source> <backup_dir> [compress]"
    exit 1
fi

source_dir=$1
backup_dir=$2
compress=${3:-false}

 Validate source
if [ ! -d "$source_dir" ]; then
    echo -e "${RED}Error: Source directory not found${NC}"
    exit 1
fi

 Create backup directory
if [ ! -d "$backup_dir" ]; then
    mkdir -p "$backup_dir"
    echo -e "${GREEN}Created backup directory${NC}"
fi

 Create timestamp
timestamp=$(date +"%Y%m%d_%H%M%S")
backup_name="backup_${timestamp}"
backup_path="$backup_dir/$backup_name"

 Perform backup
if [ "$compress" = true ]; then
    echo -e "${YELLOW}Backing up with compression...${NC}"
    tar -czf "${backup_path}.tar.gz" -C "$source_dir" . 2>/dev/null
    backup_file="${backup_path}.tar.gz"
else
    echo -e "${YELLOW}Backing up...${NC}"
    mkdir -p "$backup_path"
    cp -r "$source_dir"/* "$backup_path/" 2>/dev/null || true
    backup_file="$backup_path"
fi

 Get backup size
backup_size=$(du -sh "$backup_file" | awk '{print $1}')

echo -e "${GREEN}Backup successful!${NC}"
echo "Location: $backup_file"
echo "Size: $backup_size"
echo "Timestamp: $timestamp"
```

---

 ✅ Checklist: Bash Mastery

- [ ] Write basic Bash scripts
- [ ] Use variables and arrays
- [ ] Use if/elif/else statements
- [ ] Write for and while loops
- [ ] Create reusable functions
- [ ] Handle command-line arguments
- [ ] Understand file operations
- [ ] Use grep, sed, and awk for text processing

---

 📚 Next Steps

1. Practice Bash scripting in exercises
2. Write real utility scripts
3. Automate your workflow
4. Study system administration

---

 🔗 Additional Resources

- [Advanced Bash-Scripting Guide](https://www.gnu.org/software/bash/manual/)
- [Bash Scripting Tutorials](https://www.tutorialspoint.com/unix/unix_shell.htm)
- [GNU sed Manual](https://www.gnu.org/software/sed/manual/sed.html)
- [AWK Tutorials](https://www.gnu.org/software/gawk/manual/gawk.html)
