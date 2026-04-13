 Bash Scripting: Automation and Workflow Mastery

 1. Introduction
Bash scripting transforms repetitive manual tasks into automated, reliable workflows. This lecture covers script structure, variables, control flow, functions, and real-world automation patterns for system administration and data engineering.

 2. Why Bash Scripting Matters
- Automation: Eliminate repetitive manual tasks
- Reproducibility: Ensure consistent execution across environments
- Integration: Connect different tools and systems
- Efficiency: Process large datasets and complex workflows
- Professional Development: Essential skill for DevOps and engineering roles

 3. Script Structure and Best Practices

 3.1. Script Template
```bash
!/bin/bash
 Script: backup_database.sh
 Author: Your Name
 Date: $(date +%Y-%m-%d)
 Description: Automated database backup with logging
 Usage: ./backup_database.sh [options]

set -euo pipefail   Exit on error, undefined vars, pipe failures
IFS=$'\n\t'          Safer IFS

 Global variables
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
LOG_FILE="/var/log/backup.log"
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")

 Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'  No Color
```

 3.2. Error Handling and Logging
```bash
 Logging functions
log_info() {
    echo -e "${GREEN}[INFO]${NC} $(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a "$LOG_FILE"
}

log_warn() {
    echo -e "${YELLOW}[WARN]${NC} $(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a "$LOG_FILE"
}

log_error() {
    echo -e "${RED}[ERROR]${NC} $(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a "$LOG_FILE"
}

 Error handling
handle_error() {
    local exit_code=$?
    local line_number=$1
    log_error "Script failed on line $line_number with exit code $exit_code"
    cleanup
    exit $exit_code
}

trap 'handle_error $LINENO' ERR

 Cleanup function
cleanup() {
    log_info "Performing cleanup..."
     Remove temporary files, close connections, etc.
}
```

 4. Variables and Data Types

 4.1. Variable Declaration and Usage
```bash
 String variables
name="John Doe"
greeting="Hello, $name!"

 Numeric variables
age=25
count=$((count + 1))

 Arrays
fruits=("apple" "banana" "orange")
fruits+=("grape")   Add element

 Associative arrays (bash 4+)
declare -A person
person[name]="John"
person[age]=25
person[city]="New York"

 Environment variables
export PATH="$PATH:/usr/local/bin"
export EDITOR="vim"

 Command substitution
current_dir=$(pwd)
file_count=$(ls -1 | wc -l)
timestamp=$(date +"%Y-%m-%d %H:%M:%S")

 Process substitution
while read -r line; do
    echo "Processing: $line"
done < <(find . -name ".txt")
```

 4.2. Variable Expansion and Manipulation
```bash
 Parameter expansion
filename="document.txt"
echo "${filename%.}"      document (remove extension)
echo "${filename.}"     txt (extract extension)
echo "${filename^}"       DOCUMENT.txt (uppercase first char)
echo "${filename^^}"      DOCUMENT.TXT (uppercase all)
echo "${filename,,}"      document.txt (lowercase all)

 Default values
name="${1:-"Anonymous"}"   Use first argument or "Anonymous"
debug="${DEBUG:-false}"    Use DEBUG env var or "false"

 Length and substring
text="Hello World"
echo ${text}             11 (length)
echo ${text:6:5}          World (substring from position 6, length 5)

 Array operations
fruits=("apple" "banana" "orange")
echo ${fruits[@]}        3 (array length)
echo ${fruits[1]}         banana (second element)
echo ${fruits[@]}         apple banana orange (all elements)
```

 5. Control Flow

 5.1. Conditional Statements
```bash
 If-elif-else
if [[ $age -ge 18 ]]; then
    echo "Adult"
elif [[ $age -ge 13 ]]; then
    echo "Teenager"
else
    echo "Child"
fi

 String comparisons
if [[ "$name" == "John" ]]; then
    echo "Welcome, John!"
elif [[ -z "$name" ]]; then
    echo "Name is empty"
fi

 File tests
if [[ -f "$file" ]]; then
    echo "File exists"
elif [[ -d "$file" ]]; then
    echo "Directory exists"
fi

 Numeric comparisons
if (( count > 10 )); then
    echo "Count is greater than 10"
fi

 Logical operators
if [[ -f "$file" && -r "$file" ]]; then
    echo "File exists and is readable"
fi

 Case statement
case "$1" in
    start)
        echo "Starting service..."
        ;;
    stop)
        echo "Stopping service..."
        ;;
    restart)
        echo "Restarting service..."
        ;;
    )
        echo "Usage: $0 {start|stop|restart}"
        exit 1
        ;;
esac
```

 5.2. Loops
```bash
 For loop with range
for i in {1..10}; do
    echo "Processing item $i"
done

 For loop with array
for fruit in "${fruits[@]}"; do
    echo "Fruit: $fruit"
done

 For loop with file glob
for file in .txt; do
    echo "Processing $file"
    mv "$file" "${file%.txt}.bak"
done

 While loop
counter=0
while [[ $counter -lt 5 ]]; do
    echo "Counter: $counter"
    ((counter++))
done

 While loop reading file
while IFS= read -r line; do
    echo "Line: $line"
done < input.txt

 Until loop
until [[ $counter -ge 10 ]]; do
    echo "Counter: $counter"
    ((counter++))
done

 Break and continue
for i in {1..20}; do
    if [[ $i -eq 10 ]]; then
        break   Exit loop
    fi
    if [[ $((i % 2)) -eq 0 ]]; then
        continue   Skip even numbers
    fi
    echo "Odd number: $i"
done
```

 6. Functions

 6.1. Function Definition and Usage
```bash
 Basic function
greet() {
    echo "Hello, $1!"
}

 Function with return value
add() {
    local result=$(( $1 + $2 ))
    echo $result
}

 Function with multiple returns
get_file_info() {
    local file="$1"
    local size=$(stat -f%z "$file" 2>/dev/null || stat -c%s "$file" 2>/dev/null)
    local lines=$(wc -l < "$file" 2>/dev/null || echo "0")
    echo "$size:$lines"
}

 Function with local variables
calculate_stats() {
    local numbers=("$@")
    local sum=0
    local count=${numbers[@]}
    
    for num in "${numbers[@]}"; do
        ((sum += num))
    done
    
    local average=$((sum / count))
    echo "Sum: $sum, Count: $count, Average: $average"
}

 Recursive function
factorial() {
    local n=$1
    if [[ $n -le 1 ]]; then
        echo 1
    else
        local prev=$(factorial $((n - 1)))
        echo $((n  prev))
    fi
}

 Function usage
greet "Alice"
result=$(add 5 3)
echo "Result: $result"

file_info=$(get_file_info "document.txt")
size="${file_info%%:}"
lines="${file_info:}"
echo "Size: $size bytes, Lines: $lines"

calculate_stats 10 20 30 40 50
echo "Factorial of 5: $(factorial 5)"
```

 7. Command Line Arguments

 7.1. Positional Parameters
```bash
!/bin/bash
 Usage: ./script.sh [options] <input> <output>

 Basic argument handling
input_file="$1"
output_file="$2"
verbose="$3"

echo "Input: $input_file"
echo "Output: $output_file"
echo "Verbose: $verbose"

 All arguments
echo "All arguments: $@"
echo "Number of arguments: $"
echo "Script name: $0"

 Shift arguments
shift   Remove first argument
echo "After shift: $1"
```

 7.2. Advanced Argument Parsing with getopts
```bash
!/bin/bash
 Usage: ./backup.sh [-v] [-f] [-d directory] [-o output]

 Initialize variables
verbose=false
force=false
directory="/backup"
output="backup.tar.gz"

 Parse options
while getopts ":vfd:o:" opt; do
    case $opt in
        v)
            verbose=true
            ;;
        f)
            force=true
            ;;
        d)
            directory="$OPTARG"
            ;;
        o)
            output="$OPTARG"
            ;;
        \?)
            echo "Invalid option: -$OPTARG" >&2
            usage
            ;;
        :)
            echo "Option -$OPTARG requires an argument." >&2
            usage
            ;;
    esac
done

 Usage function
usage() {
    echo "Usage: $0 [-v] [-f] [-d directory] [-o output]"
    echo "  -v  Verbose mode"
    echo "  -f  Force overwrite"
    echo "  -d  Source directory (default: /backup)"
    echo "  -o  Output file (default: backup.tar.gz)"
    exit 1
}

 Validate arguments
if [[ ! -d "$directory" ]]; then
    echo "Error: Directory $directory does not exist"
    exit 1
fi

 Show configuration
echo "Configuration:"
echo "  Verbose: $verbose"
echo "  Force: $force"
echo "  Directory: $directory"
echo "  Output: $output"
```

 8. File Processing and Text Manipulation

 8.1. Reading and Writing Files
```bash
!/bin/bash
 File processing examples

 Read file line by line
process_file() {
    local file="$1"
    while IFS= read -r line; do
        echo "Processing: $line"
         Process each line here
    done < "$file"
}

 Process CSV file
process_csv() {
    local csv_file="$1"
    while IFS=',' read -r field1 field2 field3; do
        echo "Name: $field1, Age: $field2, City: $field3"
    done < "$csv_file"
}

 Write to file with error handling
write_file() {
    local content="$1"
    local file="$2"
    
    if ! echo "$content" > "$file"; then
        echo "Error: Failed to write to $file"
        return 1
    fi
    
    echo "Successfully wrote to $file"
}

 Append to file
append_log() {
    local message="$1"
    local log_file="$2"
    
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $message" >> "$log_file"
}
```

 8.2. Text Processing with sed and awk
```bash
!/bin/bash
 Advanced text processing

 Replace text in files
replace_text() {
    local search="$1"
    local replace="$2"
    local file="$3"
    
    sed -i.bak "s|$search|$replace|g" "$file"
    echo "Replaced '$search' with '$replace' in $file"
}

 Extract specific columns
extract_columns() {
    local file="$1"
    local col1="$2"
    local col3="$3"
    
    awk -F',' '{print $'$col1','$'$col3'}' "$file"
}

 Calculate statistics
calculate_file_stats() {
    local file="$1"
    
    echo "File statistics for $file:"
    echo "Lines: $(wc -l < "$file")"
    echo "Words: $(wc -w < "$file")"
    echo "Characters: $(wc -c < "$file")"
    
     Most common words
    tr '[:space:]' '\n' < "$file" | sort | uniq -c | sort -nr | head -5
}

 Filter and transform data
filter_data() {
    local file="$1"
    local threshold="$2"
    
    awk -F',' 'NR>1 && $3 > '$threshold' {print $1,$2,$3}' "$file" | sort -k3 -nr
}
```

 9. System Integration

 9.1. Working with External Commands
```bash
!/bin/bash
 System integration examples

 Execute command with error handling
execute_command() {
    local cmd="$1"
    local timeout="$2"
    
    if timeout "$timeout" $cmd; then
        echo "Command executed successfully"
        return 0
    else
        echo "Command failed or timed out"
        return 1
    fi
}

 Check service status
check_service() {
    local service="$1"
    
    if systemctl is-active --quiet "$service"; then
        echo "$service is running"
        return 0
    else
        echo "$service is not running"
        return 1
    fi
}

 Monitor system resources
monitor_system() {
    echo "=== System Monitor ==="
    echo "CPU Usage: $(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)"
    echo "Memory Usage: $(free | grep Mem | awk '{printf("%.2f%%"), $3/$2  100.0}')"
    echo "Disk Usage: $(df -h / | awk 'NR==2 {print $5}')"
    echo "Load Average: $(uptime | awk -F'load average:' '{print $2}')"
}

 Backup with compression
create_backup() {
    local source="$1"
    local dest="$2"
    
    if [[ -d "$source" ]]; then
        tar -czf "$dest" -C "$(dirname "$source")" "$(basename "$source")"
        echo "Backup created: $dest"
    else
        echo "Error: Source directory $source does not exist"
        return 1
    fi
}
```

 9.2. Network Operations
```bash
!/bin/bash
 Network operations

 Check connectivity
check_connectivity() {
    local host="$1"
    local port="${2:-80}"
    
    if nc -z "$host" "$port" 2>/dev/null; then
        echo "Connection to $host:$port successful"
        return 0
    else
        echo "Connection to $host:$port failed"
        return 1
    fi
}

 Download file with retry
download_file() {
    local url="$1"
    local output="$2"
    local max_retries="${3:-3}"
    
    local retry=0
    while [[ $retry -lt $max_retries ]]; do
        if curl -fsSL "$url" -o "$output"; then
            echo "Downloaded: $output"
            return 0
        else
            ((retry++))
            echo "Retry $retry/$max_retries..."
            sleep 2
        fi
    done
    
    echo "Failed to download after $max_retries attempts"
    return 1
}

 HTTP request with authentication
api_request() {
    local method="$1"
    local url="$2"
    local data="$3"
    local token="$4"
    
    local curl_cmd="curl -s -X $method"
    
    if [[ -n "$token" ]]; then
        curl_cmd="$curl_cmd -H 'Authorization: Bearer $token'"
    fi
    
    if [[ -n "$data" ]]; then
        curl_cmd="$curl_cmd -H 'Content-Type: application/json' -d '$data'"
    fi
    
    eval "$curl_cmd '$url'"
}
```

 10. Complete Practical Examples

 10.1. Automated Backup Script
```bash
!/bin/bash
 backup_system.sh - Comprehensive system backup

set -euo pipefail

 Configuration
BACKUP_DIR="/backup"
SOURCE_DIRS=("/home" "/etc" "/var/www")
EXCLUDE_PATTERNS=(".tmp" ".log" "cache/")
RETENTION_DAYS=30
LOG_FILE="/var/log/backup.log"

 Functions
log_message() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

create_exclude_file() {
    local exclude_file=$(mktemp)
    for pattern in "${EXCLUDE_PATTERNS[@]}"; do
        echo "$pattern" >> "$exclude_file"
    done
    echo "$exclude_file"
}

backup_directory() {
    local source_dir="$1"
    local backup_file="$BACKUP_DIR/$(basename "$source_dir")_$(date +%Y%m%d_%H%M%S).tar.gz"
    local exclude_file=$(create_exclude_file)
    
    log_message "Starting backup of $source_dir"
    
    if tar --exclude-from="$exclude_file" -czf "$backup_file" "$source_dir" 2>> "$LOG_FILE"; then
        log_message "Backup completed: $backup_file"
        rm -f "$exclude_file"
        return 0
    else
        log_message "Backup failed for $source_dir"
        rm -f "$exclude_file"
        return 1
    fi
}

cleanup_old_backups() {
    log_message "Cleaning up backups older than $RETENTION_DAYS days"
    find "$BACKUP_DIR" -name ".tar.gz" -mtime +$RETENTION_DAYS -delete
}

 Main execution
main() {
    log_message "Starting system backup"
    
     Create backup directory if needed
    mkdir -p "$BACKUP_DIR"
    
     Backup each directory
    local failed_backups=0
    for dir in "${SOURCE_DIRS[@]}"; do
        if [[ -d "$dir" ]]; then
            backup_directory "$dir" || ((failed_backups++))
        else
            log_message "Warning: Directory $dir does not exist"
        fi
    done
    
     Cleanup old backups
    cleanup_old_backups
    
     Summary
    if [[ $failed_backups -eq 0 ]]; then
        log_message "All backups completed successfully"
        exit 0
    else
        log_message "$failed_backups backups failed"
        exit 1
    fi
}

main "$@"
```

 10.2. Log Analysis Script
```bash
!/bin/bash
 analyze_logs.sh - Web server log analysis

set -euo pipefail

 Configuration
LOG_FILE="/var/log/nginx/access.log"
REPORT_DIR="/var/log/reports"
TOP_IPS=10
TOP_PAGES=10
TIME_WINDOW="24 hours ago"

 Functions
parse_log_line() {
    local line="$1"
     Extract IP, timestamp, method, URL, status, size
    echo "$line" | awk '{print $1 " " $4 " " $5 " " $6 " " $7 " " $9 " " $10}'
}

get_time_range() {
    local start_time=$(date -d "$TIME_WINDOW" +%s)
    local current_time=$(date +%s)
    echo "$start_time $current_time"
}

analyze_ips() {
    local start_time="$1"
    local end_time="$2"
    
    echo "=== Top $TOP_IPS IP Addresses ==="
    awk -v start="$start_time" -v end="$end_time" '
    function time_to_epoch(t) {
        gsub(/\[/, "", t);
        gsub(/\//, " ", t);
        gsub(/:/, " ", t);
        return mktime(t);
    }
    {
        timestamp = time_to_epoch($4 " " $5);
        if (timestamp >= start && timestamp <= end) {
            ips[$1]++;
            total_requests++;
        }
    }
    END {
        for (ip in ips) {
            printf "%-15s %6d (%.2f%%)\n", ip, ips[ip], (ips[ip]/total_requests)100;
        }
    }
    ' "$LOG_FILE" | sort -k2 -nr | head -n "$TOP_IPS"
}

analyze_pages() {
    local start_time="$1"
    local end_time="$2"
    
    echo "=== Top $TOP_PAGES Pages ==="
    awk -v start="$start_time" -v end="$end_time" '
    function time_to_epoch(t) {
        gsub(/\[/, "", t);
        gsub(/\//, " ", t);
        gsub(/:/, " ", t);
        return mktime(t);
    }
    {
        timestamp = time_to_epoch($4 " " $5);
        if (timestamp >= start && timestamp <= end) {
            pages[$7]++;
            total_requests++;
        }
    }
    END {
        for (page in pages) {
            printf "%-50s %6d (%.2f%%)\n", page, pages[page], (pages[page]/total_requests)100;
        }
    }
    ' "$LOG_FILE" | sort -k2 -nr | head -n "$TOP_IPS"
}

analyze_errors() {
    local start_time="$1"
    local end_time="$2"
    
    echo "=== Error Analysis ==="
    awk -v start="$start_time" -v end="$end_time" '
    function time_to_epoch(t) {
        gsub(/\[/, "", t);
        gsub(/\//, " ", t);
        gsub(/:/, " ", t);
        return mktime(t);
    }
    {
        timestamp = time_to_epoch($4 " " $5);
        if (timestamp >= start && timestamp <= end && $9 >= 400) {
            errors[$9]++;
            total_errors++;
        }
    }
    END {
        for (code in errors) {
            printf "HTTP %s: %d errors\n", code, errors[code];
        }
        printf "Total errors: %d\n", total_errors;
    }
    ' "$LOG_FILE"
}

 Main execution
main() {
    if [[ ! -f "$LOG_FILE" ]]; then
        echo "Error: Log file $LOG_FILE not found"
        exit 1
    fi
    
    mkdir -p "$REPORT_DIR"
    
     Get time range
    read -r start_time end_time <<< $(get_time_range)
    
     Generate report
    local report_file="$REPORT_DIR/log_analysis_$(date +%Y%m%d_%H%M%S).txt"
    
    {
        echo "Log Analysis Report"
        echo "Generated: $(date)"
        echo "Time Window: $TIME_WINDOW"
        echo "Log File: $LOG_FILE"
        echo ""
        
        analyze_ips "$start_time" "$end_time"
        echo ""
        
        analyze_pages "$start_time" "$end_time"
        echo ""
        
        analyze_errors "$start_time" "$end_time"
        
    } > "$report_file"
    
    echo "Report generated: $report_file"
}

main "$@"
```

 11. Testing and Debugging

 11.1. Debugging Techniques
```bash
!/bin/bash
 Debugging examples

 Enable debug mode
set -x   Print each command before execution
set -v   Print shell input lines

 Debug function
debug() {
    if [[ "${DEBUG:-false}" == "true" ]]; then
        echo "[DEBUG] $1" >&2
    fi
}

 Test mode
test_mode() {
    if [[ "${TEST_MODE:-false}" == "true" ]]; then
        echo "[TEST] Would execute: $1"
        return 0
    else
        eval "$1"
    fi
}

 Mock commands for testing
mock_command() {
    if [[ "${MOCK_MODE:-false}" == "true" ]]; then
        echo "Mock: $1"
        return 0
    else
        "$@"
    fi
}
```

 11.2. Unit Testing Framework
```bash
!/bin/bash
 Simple test framework

 Test variables
TESTS_PASSED=0
TESTS_FAILED=0

 Test assertion functions
assert_equals() {
    local expected="$1"
    local actual="$2"
    local message="$3"
    
    if [[ "$expected" == "$actual" ]]; then
        echo "PASS: $message"
        ((TESTS_PASSED++))
    else
        echo "FAIL: $message"
        echo "  Expected: '$expected'"
        echo "  Actual: '$actual'"
        ((TESTS_FAILED++))
    fi
}

assert_file_exists() {
    local file="$1"
    local message="$2"
    
    if [[ -f "$file" ]]; then
        echo "PASS: $message"
        ((TESTS_PASSED++))
    else
        echo "FAIL: $message"
        echo "  File '$file' does not exist"
        ((TESTS_FAILED++))
    fi
}

 Test runner
run_tests() {
    echo "Running tests..."
    
     Test function
    test_function() {
        echo "test"
    }
    assert_equals "test" "$(test_function)" "Function returns expected value"
    
     Test file creation
    local test_file="/tmp/test_file.txt"
    echo "test content" > "$test_file"
    assert_file_exists "$test_file" "File creation test"
    
     Cleanup
    rm -f "$test_file"
    
     Summary
    echo ""
    echo "Test Summary:"
    echo "  Passed: $TESTS_PASSED"
    echo "  Failed: $TESTS_FAILED"
    echo "  Total: $((TESTS_PASSED + TESTS_FAILED))"
    
    if [[ $TESTS_FAILED -eq 0 ]]; then
        echo "All tests passed!"
        return 0
    else
        echo "Some tests failed!"
        return 1
    fi
}
```

 12. Performance Optimization

 12.1. Efficient Scripting Practices
```bash
!/bin/bash
 Performance optimization examples

 Use built-in operations instead of external commands
 Slow:
count=$(ls | wc -l)

 Fast:
count=()
count=${count[@]}

 Avoid subshells when possible
 Slow:
while read -r line; do
    echo "$line" | grep "pattern"
done < file.txt

 Fast:
while read -r line; do
    if [[ "$line" =~ pattern ]]; then
        echo "$line"
    fi
done < file.txt

 Use arrays instead of strings for lists
 Slow:
files="file1.txt file2.txt file3.txt"
for file in $files; do
    echo "$file"
done

 Fast:
files=("file1.txt" "file2.txt" "file3.txt")
for file in "${files[@]}"; do
    echo "$file"
done
```

 13. Security Best Practices

 13.1. Secure Scripting
```bash
!/bin/bash
 Security best practices

 Input validation
validate_input() {
    local input="$1"
    local pattern="$2"
    
    if [[ ! "$input" =~ $pattern ]]; then
        echo "Invalid input: $input"
        return 1
    fi
}

 Sanitize filenames
sanitize_filename() {
    local filename="$1"
    echo "$filename" | sed 's/[^a-zA-Z0-9._-]/_/g'
}

 Prevent command injection
safe_execute() {
    local cmd="$1"
    local arg="$2"
    
     Use arrays to prevent word splitting
    local args=("$cmd" "$arg")
    "${args[@]}"
}

 Handle secrets securely
load_secrets() {
    local secrets_file="$1"
    
    if [[ -f "$secrets_file" ]]; then
        source "$secrets_file"
    else
        echo "Secrets file not found"
        return 1
    fi
}
```

 14. Resources and Further Learning

 14.1. Documentation
- [Bash Reference Manual](https://www.gnu.org/software/bash/manual/)
- [Advanced Bash Scripting Guide](https://www.tldp.org/LDP/abs/html/)
- [ShellCheck Static Analysis](https://www.shellcheck.net/)

 14.2. Practice Platforms
- [Exercism Bash Track](https://exercism.io/tracks/bash)
- [Codewars Shell/Bash Kata](https://www.codewars.com/?language=bash)
- [Linux Command Line Challenges](https://cmdchallenge.com/)

 14.3. Tools and Utilities
- ShellCheck: Static analysis for shell scripts
- Bash Debugger: bashdb for debugging bash scripts
- Bash-completion: Command-line completion for bash

---

Next Steps: Practice writing scripts for real-world tasks, focus on error handling, and explore integration with other tools like Python and Docker.
