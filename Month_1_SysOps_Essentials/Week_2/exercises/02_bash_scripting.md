 Exercise 2: Bash Scripting Essentials

 🎯 Objective
Develop practical Bash scripting skills including variables, conditionals, loops, and functions.

---

 📋 Part 1: Variable Practice

 Task 1.1: Create Variable Demo Script

Create `~/linux-mastery/scripts/bash/variables_demo.sh`:

```bash
!/bin/bash

 Variable Demonstration Script

echo "================================"
echo "   VARIABLE TYPES IN BASH"
echo "================================"

 Simple variables
echo ""
echo "1. Simple Variables:"
name="3MTT Student"
age=25
course="Linux & Bash"

echo "Name: $name"
echo "Age: $age"
echo "Course: $course"

 Command substitution
echo ""
echo "2. Command Substitution:"
current_date=$(date +%Y-%m-%d)
current_time=$(date +%H:%M:%S)
user=$(whoami)
hostname=$(hostname)

echo "Date: $current_date"
echo "Time: $current_time"
echo "User: $user"
echo "Hostname: $hostname"

 Environment variables
echo ""
echo "3. Environment Variables:"
echo "Home: $HOME"
echo "Shell: $SHELL"
echo "Path: $PATH"
echo "PWD: $PWD"

 Variable operations
echo ""
echo "4. Variable Operations:"
word="Hello"
echo "Original: $word"
echo "Length: ${word}"
echo "Uppercase: ${word^^}"
echo "Lowercase: ${word,,}"
echo "Substring (0-2): ${word:0:2}"

 Default values
echo ""
echo "5. Default Values:"
unset_var=""
echo "With value: ${unset_var:-"default"}"
echo "Set default: ${unset_var:="new_default"}"
echo "Now unset_var is: $unset_var"

echo ""
echo "================================"
```

Test it:
```bash
chmod +x ~/linux-mastery/scripts/bash/variables_demo.sh
~/linux-mastery/scripts/bash/variables_demo.sh
```

 Task 1.2: Special Variables Script

Create `~/linux-mastery/scripts/bash/special_vars.sh`:

```bash
!/bin/bash

 Special Variables Demonstration

echo "================================"
echo "   SPECIAL VARIABLES"
echo "================================"

echo "Script name (\$0): $0"
echo "Arguments count (\$): $"
echo "All arguments (\$@): $@"
echo "All arguments (\$): $"
echo "Process ID (\$\$): $$"
echo "Last exit code (\$?): $?"

echo ""
echo "Individual arguments:"
echo "\$1 (First arg): ${1:-"not provided"}"
echo "\$2 (Second arg): ${2:-"not provided"}"
echo "\$3 (Third arg): ${3:-"not provided"}"

echo ""
echo "Loop through arguments:"
count=1
for arg in "$@"; do
    echo "  Arg $count: $arg"
    ((count++))
done

echo "================================"
```

Test with arguments:
```bash
~/linux-mastery/scripts/bash/special_vars.sh arg1 arg2 arg3
```

---

 📋 Part 2: Conditionals Practice

 Task 2.1: Create Interactive Menu Script

Create `~/linux-mastery/scripts/bash/calculator.sh`:

```bash
!/bin/bash

 Interactive Calculator

echo "================================"
echo "   SIMPLE CALCULATOR"
echo "================================"

 Get input
read -p "Enter first number: " num1
read -p "Enter operator (+, -, , /): " operator
read -p "Enter second number: " num2

 Validate numbers
if ! [[ "$num1" =~ ^-?[0-9]+([.][0-9]+)?$ ]]; then
    echo "Error: First input is not a valid number"
    exit 1
fi

if ! [[ "$num2" =~ ^-?[0-9]+([.][0-9]+)?$ ]]; then
    echo "Error: Second input is not a valid number"
    exit 1
fi

 Perform calculation
case $operator in
    +)
        result=$(echo "$num1 + $num2" | bc)
        echo "Result: $num1 + $num2 = $result"
        ;;
    -)
        result=$(echo "$num1 - $num2" | bc)
        echo "Result: $num1 - $num2 = $result"
        ;;
    )
        result=$(echo "$num1  $num2" | bc)
        echo "Result: $num1  $num2 = $result"
        ;;
    /)
        if [ "$num2" = "0" ]; then
            echo "Error: Cannot divide by zero"
            exit 1
        fi
        result=$(echo "scale=2; $num1 / $num2" | bc)
        echo "Result: $num1 / $num2 = $result"
        ;;
    )
        echo "Error: Unknown operator '$operator'"
        echo "Valid operators: +, -, , /"
        exit 1
        ;;
esac

echo "================================"
```

 Task 2.2: File Checker Script

Create `~/linux-mastery/scripts/bash/file_checker.sh`:

```bash
!/bin/bash

 File Information Checker

echo "================================"
echo "   FILE CHECKER"
echo "================================"

 Check if argument provided
if [ $ -eq 0 ]; then
    echo "Usage: $0 <filename>"
    exit 1
fi

file="$1"

echo "Checking: $file"
echo ""

 Check existence
if [ -e "$file" ]; then
    echo "✓ File exists"
    
     Type checks
    if [ -f "$file" ]; then
        echo "✓ Regular file"
    elif [ -d "$file" ]; then
        echo "✓ Directory"
    elif [ -L "$file" ]; then
        echo "✓ Symbolic link"
    fi
    
     Permission checks
    if [ -r "$file" ]; then
        echo "✓ Readable"
    else
        echo "✗ Not readable"
    fi
    
    if [ -w "$file" ]; then
        echo "✓ Writable"
    else
        echo "✗ Not writable"
    fi
    
    if [ -x "$file" ]; then
        echo "✓ Executable"
    else
        echo "✗ Not executable"
    fi
    
     Size
    if [ -s "$file" ]; then
        size=$(stat -f%z "$file" 2>/dev/null || stat -c%s "$file" 2>/dev/null)
        echo "✓ Size: $size bytes"
    else
        echo "⚠ File is empty"
    fi
    
else
    echo "✗ File does not exist"
fi

echo "================================"
```

---

 📋 Part 3: Loops Practice

 Task 3.1: Create Loop Demonstrations

Create `~/linux-mastery/scripts/bash/loops_demo.sh`:

```bash
!/bin/bash

 Loop Demonstrations

echo "================================"
echo "   LOOP EXAMPLES"
echo "================================"

 For loop with list
echo ""
echo "1. For loop (list):"
for fruit in apple banana cherry date; do
    echo "  Fruit: $fruit"
done

 For loop with range
echo ""
echo "2. For loop (range):"
for i in {1..5}; do
    echo "  Number: $i"
done

 For loop with C-style syntax
echo ""
echo "3. C-style for loop:"
for ((i=0; i<5; i++)); do
    echo "  Counter: $i"
done

 While loop
echo ""
echo "4. While loop:"
counter=5
while [ $counter -gt 0 ]; do
    echo "  Countdown: $counter"
    ((counter--))
done

 Until loop
echo ""
echo "5. Until loop:"
count=0
until [ $count -ge 5 ]; do
    echo "  Count up: $count"
    ((count++))
done

 Loop over files
echo ""
echo "6. Loop over files:"
for file in .sh; do
    if [ -f "$file" ]; then
        echo "  Script: $file"
    fi
done

 Break and continue
echo ""
echo "7. Break and continue:"
for i in {1..20}; do
     Skip even numbers
    if [ $((i % 2)) -eq 0 ]; then
        continue
    fi
    
     Stop at 15
    if [ $i -gt 15 ]; then
        break
    fi
    
    echo "  Odd number: $i"
done

echo "================================"
```

 Task 3.2: Batch Processing Script

Create `~/linux-mastery/scripts/bash/batch_rename.sh`:

```bash
!/bin/bash

 Batch File Renamer

 Usage: batch_rename.sh <extension> <prefix>
 Example: batch_rename.sh txt backup_

if [ $ -lt 2 ]; then
    echo "Usage: $0 <extension> <prefix>"
    echo "Example: $0 txt backup_"
    exit 1
fi

extension="$1"
prefix="$2"
count=0

echo "Renaming .$extension files with prefix '$prefix'"

for file in .$extension; do
    if [ -f "$file" ]; then
         Skip if already has prefix
        if [[ "$file" == ${prefix} ]]; then
            echo "Skipping $file (already renamed)"
            continue
        fi
        
        newname="${prefix}${file}"
        echo "Renaming: $file -> $newname"
        mv "$file" "$newname"
        ((count++))
    fi
done

echo ""
echo "Renamed $count files"
```

---

 📋 Part 4: Functions Practice

 Task 4.1: Create Function Library

Create `~/linux-mastery/scripts/bash/function_lib.sh`:

```bash
!/bin/bash

 Bash Function Library

 Function to check if command exists
command_exists() {
    command -v "$1" >/dev/null 2>&1
}

 Function to print colored text
print_color() {
    local color=$1
    local text=$2
    
    case $color in
        red)    echo -e "\033[31m${text}\033[0m" ;;
        green)  echo -e "\033[32m${text}\033[0m" ;;
        yellow) echo -e "\033[33m${text}\033[0m" ;;
        blue)   echo -e "\033[34m${text}\033[0m" ;;
        )      echo "$text" ;;
    esac
}

 Function to calculate factorial
factorial() {
    local n=$1
    local result=1
    
    if [ $n -lt 0 ]; then
        echo "Error: Negative number"
        return 1
    fi
    
    for ((i=2; i<=n; i++)); do
        result=$((result  i))
    done
    
    echo $result
}

 Function to check if number is prime
is_prime() {
    local num=$1
    
    if [ $num -le 1 ]; then
        echo "false"
        return
    fi
    
    for ((i=2; ii<=num; i++)); do
        if [ $((num % i)) -eq 0 ]; then
            echo "false"
            return
        fi
    done
    
    echo "true"
}

 Function to generate random string
random_string() {
    local length=${1:-10}
    cat /dev/urandom | tr -dc 'a-zA-Z0-9' | head -c $length
    echo
}

 Main demonstration
echo "================================"
echo "   FUNCTION LIBRARY DEMO"
echo "================================"

echo ""
echo "1. Command Check:"
if command_exists "git"; then
    print_color green "✓ Git is installed"
else
    print_color red "✗ Git is not installed"
fi

echo ""
echo "2. Factorial Calculation:"
for n in 0 1 5 10; do
    result=$(factorial $n)
    echo "  $n! = $result"
done

echo ""
echo "3. Prime Check:"
for num in 2 7 10 17 20; do
    if [ "$(is_prime $num)" = "true" ]; then
        print_color green "  $num is prime"
    else
        print_color yellow "  $num is not prime"
    fi
done

echo ""
echo "4. Random Strings:"
echo "  8 chars: $(random_string 8)"
echo "  16 chars: $(random_string 16)"

echo ""
echo "================================"
```

---

 📋 Part 5: Error Handling

 Task 5.1: Robust Script Template

Create `~/linux-mastery/scripts/bash/robust_template.sh`:

```bash
!/bin/bash

 Robust Script Template with Error Handling

set -euo pipefail
 -e: Exit immediately if command exits with non-zero status
 -u: Treat unset variables as error
 -o pipefail: Return value of failed command in pipeline

 Error handler
error_handler() {
    local line_no=$1
    local error_code=$2
    echo ""
    echo "ERROR: Script failed at line $line_no with exit code $error_code"
    exit $error_code
}

 Set trap
trap 'error_handler ${LINENO} $?' ERR

 Logging function
log() {
    local level=$1
    shift
    local message="$"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    echo "[$timestamp] [$level] $message"
}

 Cleanup function
cleanup() {
    log INFO "Cleaning up..."
     Add cleanup tasks here
    rm -f /tmp/temp_file_$$ 2>/dev/null || true
}

trap cleanup EXIT

 Main script
echo "================================"
echo "   ROBUST SCRIPT DEMO"
echo "================================"

log INFO "Starting script"

 Create temp file
temp_file="/tmp/temp_file_$$"
log INFO "Creating temp file: $temp_file"
echo "Test content" > "$temp_file"

 Check if file exists
if [ ! -f "$temp_file" ]; then
    log ERROR "Failed to create temp file"
    exit 1
fi

log INFO "Temp file created successfully"

 Read content
content=$(cat "$temp_file")
log INFO "File content: $content"

log INFO "Script completed successfully"
echo "================================"
```

---

 ✅ Verification Commands

Test all your scripts:

```bash
cd ~/linux-mastery/scripts/bash

 Make all scripts executable
chmod +x .sh

 Run each script
./variables_demo.sh
./special_vars.sh test1 test2 test3
./calculator.sh   Interactive
./file_checker.sh ~/linux-mastery/README.md
./loops_demo.sh
./function_lib.sh
./robust_template.sh
```

---

 📊 Deliverables

Submit all scripts created:
1. `variables_demo.sh`
2. `special_vars.sh`
3. `calculator.sh`
4. `file_checker.sh`
5. `loops_demo.sh`
6. `batch_rename.sh`
7. `function_lib.sh`
8. `robust_template.sh`

Include:
- Screenshots of script outputs
- Brief documentation of what each script does

---

Previous: [Exercise 1: File System Navigation ←](./01_file_system_navigation.md) | Next: [Exercise 3: Text Processing →](./03_text_processing.md)
