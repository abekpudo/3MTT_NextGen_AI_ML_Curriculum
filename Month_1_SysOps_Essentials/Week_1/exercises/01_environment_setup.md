 Exercise 1: Environment Verification

 🎯 Objective
Verify your Python development environment is properly configured and create a verification report.

---

 📋 Tasks

 Task 1: Python Version Check
Create a script that prints Python version information.

```python
 verify_python.py
import sys
import platform

print("="  50)
print("PYTHON ENVIRONMENT VERIFICATION")
print("="  50)
print(f"Python Version: {platform.python_version()}")
print(f"Python Executable: {sys.executable}")
print(f"Platform: {platform.platform()}")
print(f"Architecture: {platform.machine()}")
print(f"Processor: {platform.processor()}")
print("="  50)
```

Expected Output:
- Python 3.9.x or higher
- Clear path to Python executable
- Operating system information

---

 Task 2: Package Installation Check
Verify essential packages are installed.

```python
 check_packages.py
import importlib

packages = [
    'numpy',
    'pandas',
    'matplotlib',
    'jupyter',
    'requests'
]

print("\nINSTALLED PACKAGES:")
print("-"  30)

for package in packages:
    try:
        module = importlib.import_module(package)
        version = getattr(module, '__version__', 'unknown')
        print(f"✓ {package:15} {version}")
    except ImportError:
        print(f"✗ {package:15} NOT INSTALLED")

print("-"  30)
```

Expected: All packages should show ✓ with versions

---

 Task 3: Virtual Environment Test
Demonstrate virtual environment activation.

```python
 venv_test.py
import sys
import os

print("\nVIRTUAL ENVIRONMENT CHECK:")
print("-"  30)

 Check if in virtual environment
if hasattr(sys, 'real_prefix') or (hasattr(sys, 'base_prefix') and sys.base_prefix != sys.prefix):
    print("✓ Virtual environment is ACTIVE")
    print(f"  Path: {sys.prefix}")
else:
    print("✗ Virtual environment is NOT active")
    print("  Activate with: source venv/bin/activate")

print(f"\nCurrent working directory: {os.getcwd()}")
print(f"Python path: {sys.executable}")
```

---

 Task 4: Jupyter Notebook Test
Create a working Jupyter notebook that:
1. Displays system information
2. Creates a simple matplotlib plot
3. Uses pandas to create a DataFrame
4. Saves results to a file

Notebook cells to include:
```python
 Cell 1: Imports
import sys
import pandas as pd
import matplotlib.pyplot as plt

 Cell 2: System info
print(f"Python: {sys.version}")
print(f"Pandas: {pd.__version__}")

 Cell 3: Create DataFrame
df = pd.DataFrame({
    'x': range(10),
    'y': [i2 for i in range(10)]
})
print(df)

 Cell 4: Create plot
plt.figure(figsize=(8, 5))
plt.plot(df['x'], df['y'], marker='o')
plt.title('Sample Plot from Jupyter')
plt.xlabel('X values')
plt.ylabel('Y values (squared)')
plt.grid(True)
plt.savefig('exercise_plot.png')
plt.show()
```

---

 Task 5: Generate Verification Report
Create a comprehensive report file.

```python
 generate_report.py
import sys
import platform
import subprocess
from datetime import datetime

def run_command(cmd):
    """Run shell command and return output"""
    try:
        result = subprocess.run(cmd, shell=True, capture_output=True, text=True)
        return result.stdout.strip()
    except Exception as e:
        return f"Error: {e}"

def main():
    report = []
    report.append("="  60)
    report.append("3MTT PROGRAM - ENVIRONMENT VERIFICATION REPORT")
    report.append("="  60)
    report.append(f"Generated: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
    report.append("")
    
     Python info
    report.append("PYTHON INFORMATION:")
    report.append("-"  40)
    report.append(f"Version: {platform.python_version()}")
    report.append(f"Executable: {sys.executable}")
    report.append(f"Platform: {platform.platform()}")
    report.append("")
    
     Git info
    report.append("GIT INFORMATION:")
    report.append("-"  40)
    git_version = run_command("git --version")
    report.append(f"Git Version: {git_version}")
    
    git_config = run_command("git config --list")
    report.append("\nGit Configuration:")
    for line in git_config.split('\n')[:10]:   First 10 lines
        if line.strip():
            report.append(f"  {line}")
    report.append("")
    
     Installed packages
    report.append("INSTALLED PACKAGES:")
    report.append("-"  40)
    pip_list = run_command("pip list")
    report.append(pip_list)
    report.append("")
    
     Save report
    with open('verification_report.txt', 'w') as f:
        f.write('\n'.join(report))
    
    print("Report saved to: verification_report.txt")

if __name__ == "__main__":
    main()
```

---

 ✅ Submission Requirements

Submit the following files in your repository:

1. `verify_python.py` - Python version checker
2. `check_packages.py` - Package verification
3. `venv_test.py` - Environment activation test
4. `exercise_notebook.ipynb` - Working Jupyter notebook
5. `generate_report.py` - Report generator
6. `verification_report.txt` - Generated report

---

 🎓 Success Criteria

- [ ] All Python scripts run without errors
- [ ] Jupyter notebook displays correctly
- [ ] Plot is generated and saved
- [ ] Verification report is comprehensive
- [ ] All files committed to GitHub

---

 💡 Hints

1. If packages are missing: `pip install package_name`
2. If Jupyter won't start: Check it's installed in venv
3. For report generation: Use `subprocess` carefully
4. Commit each script as you complete it

---

Next: [Exercise 2: Git Workflow Practice →](./02_git_workflow.md)
