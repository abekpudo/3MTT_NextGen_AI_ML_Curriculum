 Week 2 Assignment: System Administration Toolkit

 📋 Assignment Overview

Create a comprehensive system administration toolkit demonstrating mastery of Linux file systems, Bash scripting, text processing, and automation.

Weight: 100 points (Week 2 Grade)  
Due: End of Week 2  
Submission: GitHub repository link

---

 🎯 Learning Objectives

By completing this assignment, you will:
- Master Linux file system operations
- Write production-quality Bash scripts
- Implement text processing pipelines
- Create automated system monitoring solutions
- Practice professional scripting standards

---

 📊 Grading Rubric

 Part A: File System Mastery (25 points)

 1. Directory Structure (5 points)
Create a complex, well-organized directory structure for a "DevOps Project":
```
devops-project/
├── ansible/
│   ├── playbooks/
│   ├── roles/
│   └── inventory/
├── docker/
│   ├── images/
│   └── compose/
├── scripts/
│   ├── deploy/
│   ├── backup/
│   └── monitor/
├── configs/
│   ├── nginx/
│   ├── apache/
│   └── database/
└── logs/
    ├── application/
    └── system/
```

- [ ] All directories created correctly (2 pts)
- [ ] Appropriate file placement (2 pts)
- [ ] Documentation of structure (1 pt)

 2. File Operations (10 points)
- [ ] Create 20+ files with realistic content (3 pts)
- [ ] Demonstrate copy, move, rename operations (3 pts)
- [ ] Create symbolic links where appropriate (2 pts)
- [ ] Implement file searching with find/grep (2 pts)

 3. Navigation Documentation (5 points)
- [ ] Document absolute vs relative path usage (2 pts)
- [ ] Include navigation command log (2 pts)
- [ ] Clear explanations of choices (1 pt)

 4. Permissions Setup (5 points)
- [ ] Set appropriate permissions for all files (2 pts)
- [ ] Document permission rationale (2 pts)
- [ ] Implement security best practices (1 pt)

---

 Part B: Scripting Challenge (35 points)

 1. Arguments & Validation (7 points)
Create `deploy.sh` that:
- Accepts environment, version, and config file arguments
- Validates all inputs
- Shows usage when arguments missing
- Returns appropriate exit codes

```bash
 Example usage:
./deploy.sh production v1.2.3 config/prod.yaml
```

Requirements:
- [ ] Proper argument handling (2 pts)
- [ ] Input validation (2 pts)
- [ ] Help/usage display (2 pts)
- [ ] Exit codes (1 pt)

 2. Functions & Modularity (7 points)
Create `backup.sh` with:
- Modular function design
- Reusable components
- Local variable scope
- Function documentation

Requirements:
- [ ] 5+ well-defined functions (2 pts)
- [ ] Proper variable scope (2 pts)
- [ ] Function documentation (2 pts)
- [ ] Error handling in functions (1 pt)

 3. Error Handling (7 points)
Create `monitor.sh` with robust error handling:
- `set -euo pipefail`
- Trap handlers
- Logging mechanism
- Cleanup functions

Requirements:
- [ ] Strict mode enabled (2 pts)
- [ ] Trap configuration (2 pts)
- [ ] Comprehensive logging (2 pts)
- [ ] Cleanup on exit (1 pt)

 4. I/O Redirection (7 points)
Create `log_analyzer.sh` demonstrating:
- Input from files
- Output to files
- Error redirection
- Pipeline construction

Requirements:
- [ ] File input handling (2 pts)
- [ ] Output redirection (2 pts)
- [ ] Error log creation (2 pts)
- [ ] Effective pipelines (1 pt)

 5. Documentation (7 points)
All scripts must include:
- [ ] Header comments with description (2 pts)
- [ ] Usage instructions (2 pts)
- [ ] Inline comments (2 pts)
- [ ] README with examples (1 pt)

---

 Part C: Text Processing (25 points)

 1. grep Mastery (5 points)
Create `find_errors.sh` that:
- Searches log files for errors
- Uses regex patterns
- Implements case-insensitive search
- Shows context lines
- Outputs to report

Requirements:
- [ ] Multiple grep patterns (2 pts)
- [ ] Regex usage (1 pt)
- [ ] Context display (1 pt)
- [ ] Report generation (1 pt)

 2. sed Transformations (5 points)
Create `sanitize_logs.sh` that:
- Removes sensitive data (IPs, emails)
- Standardizes timestamps
- Converts formats
- Creates backups

Requirements:
- [ ] Multiple sed commands (2 pts)
- [ ] Backup creation (1 pt)
- [ ] In-place editing (1 pt)
- [ ] Pattern matching (1 pt)

 3. awk Field Processing (5 points)
Create `report_generator.sh` that:
- Processes CSV/TSV data
- Calculates statistics
- Formats output
- Generates summary

Requirements:
- [ ] Field extraction (2 pts)
- [ ] Calculations (1 pt)
- [ ] Formatted output (1 pt)
- [ ] Summary statistics (1 pt)

 4. Pipeline Construction (5 points)
Create `system_report.sh` using pipelines to:
- Collect system info
- Process data
- Format output
- Generate HTML/Markdown report

Requirements:
- [ ] 5+ stage pipeline (2 pts)
- [ ] Data transformation (1 pt)
- [ ] Formatted report (2 pts)

 5. Complete Pipeline (5 points)
Create comprehensive solution:
- [ ] Input: Raw log files (1 pt)
- [ ] Processing: Extract, transform, analyze (2 pts)
- [ ] Output: Professional report (2 pts)

---

 Part D: System Administration (15 points)

 1. Process Monitoring (5 points)
Create `process_monitor.sh`:
- Shows CPU/memory usage
- Lists top processes
- Filters by user/name
- Alerts on high usage

Requirements:
- [ ] Resource monitoring (2 pts)
- [ ] Process filtering (1 pt)
- [ ] Alert mechanism (1 pt)
- [ ] Continuous monitoring option (1 pt)

 2. Resource Tracking (5 points)
Create `resource_tracker.sh`:
- Tracks disk usage
- Monitors memory
- Checks CPU load
- Generates trends

Requirements:
- [ ] Disk monitoring (1 pt)
- [ ] Memory tracking (1 pt)
- [ ] CPU monitoring (1 pt)
- [ ] Historical data (1 pt)
- [ ] Trend analysis (1 pt)

 3. Cron Implementation (5 points)
- Create crontab with 3+ jobs
- Document schedule
- Implement logging
- Test execution

Requirements:
- [ ] Working cron jobs (2 pts)
- [ ] Appropriate scheduling (1 pt)
- [ ] Logging enabled (1 pt)
- [ ] Documentation (1 pt)

---

 📁 Repository Structure

```
week2-sysadmin-toolkit/
├── README.md                      Assignment overview
├── docs/
│   ├── setup.md                  Setup instructions
│   ├── usage.md                  Usage guide
│   └── architecture.md           Design decisions
├── part-a-filesystem/
│   ├── devops-project/           Your directory structure
│   ├── navigation-log.txt        Command history
│   └── permissions-report.txt    Permission documentation
├── part-b-scripts/
│   ├── deploy.sh                 Deployment script
│   ├── backup.sh                 Backup script
│   ├── monitor.sh                Monitoring script
│   ├── log_analyzer.sh           Log analysis script
│   └── lib/
│       └── common.sh             Shared functions
├── part-c-textprocessing/
│   ├── find_errors.sh            grep exercise
│   ├── sanitize_logs.sh          sed exercise
│   ├── report_generator.sh       awk exercise
│   ├── system_report.sh          Pipeline exercise
│   ├── sample-logs/              Test data
│   └── output/                   Generated reports
├── part-d-systemadmin/
│   ├── process_monitor.sh        Process monitoring
│   ├── resource_tracker.sh       Resource tracking
│   └── crontab.txt               Cron schedule
└── tests/
    ├── test_deploy.sh            Test cases
    ├── test_backup.sh
    └── validation.sh             Validation suite
```

---

 🎓 Excellence Criteria

 Distinction (90-100 points)
- All scripts production-ready
- Comprehensive error handling
- Professional documentation
- Test suite included
- Creative extensions
- Clean code structure

 Merit (80-89 points)
- All requirements met
- Good error handling
- Clear documentation
- Working test cases
- Clean implementation

 Pass (70-79 points)
- Most requirements met
- Basic error handling
- Adequate documentation
- Scripts functional

 Needs Improvement (<70 points)
- Missing key requirements
- Poor error handling
- Incomplete documentation

---

 🚫 Prohibited Practices

1. No hardcoded paths - Use variables
2. No silent failures - Always check exit codes
3. No global variables in functions - Use local
4. No missing documentation - All scripts must have headers
5. No untested code - Verify all scripts work

---

 💡 Success Tips

1. Start Early - This is a substantial assignment
2. Test Incrementally - Run scripts as you write them
3. Use Version Control - Commit frequently
4. Document Continuously - Don't wait until the end
5. Validate Inputs - Never trust user input
6. Handle Errors - Plan for failure cases
7. Write Tests - Verify your scripts work
8. Follow Standards - Use consistent formatting

---

 📅 Submission Checklist

Before submitting, verify:

- [ ] All scripts are executable (`chmod +x`)
- [ ] All scripts run without errors
- [ ] Documentation is complete
- [ ] Test cases pass
- [ ] README is comprehensive
- [ ] Repository is public
- [ ] All files committed
- [ ] No sensitive data in repo
- [ ] Links work correctly

---

 🆘 Getting Help

- Technical Issues: Check script with `bash -x`
- Syntax Errors: Use ShellCheck
- Logic Issues: Add debug echo statements
- Concept Questions: Review lecture notes
- Stuck?: Break problem into smaller pieces

---

Good luck with your System Administration Toolkit! 🐧

Due Date: [Insert Date]  
Late Submissions: -10 points per day
