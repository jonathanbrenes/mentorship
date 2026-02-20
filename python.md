# Python
## Enhanced Performance in Python 3.6 vs 3.11
- Create a SLES 15 server which it will have python 3.6 and 3.11 
  Deploy this VM. This will automatically create a VM.
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpython001.json)
  
- Demonstrating Enhanced Performance in Python 3.11 vs 3.6 Using an N-body Simulation Script
  
  This will showcase the improved performance of Python 3.11 over Python 3.6 by running an extended N-body simulation. The script calculates the gravitational interactions between celestial bodies, providing a computationally intensive benchmark to highlight the efficiency gains in Python 3.11.
- Execute the python script using different versions
  Use python 3.6 and 3.11 available on SLES 15

  ```bash
  python3.6 /usr/local/bin/nbody.py
  python3.11 /usr/local/bin/nbody.py
  ```
 > **Note:** Result of the script will be faster with 3.11. That's one of reasons everybody is moving to newest python version

---

## Python Virtual Environment
- Set a [Python Virtual Environment](https://docs.python.org/3/library/venv.html)
  Create an environment for python 3.6 and another one for python 3.11
  ```bash
  python3.6 -m venv p36env # Create python virtual environment for version 3.6 inside p36env directory
  python3.11 -m venv p311env # Create python virtual environment for version 3.11 inside p311env directory
  source p36env/bin/activate # Activate virtual environment for 3.6
  python3 --version # This will return version 3.6
  source p311env/bin/activate # Activate virtual environment for 3.11
  python3 --version # This will return version 3.11
  ```
- Using pip inside each python virtual environment
  The management of each python package through pip is individual for each environment. So it is possible to install different packages and version over different environments
  ```bash
  source p36env/bin/activate # Activate the virtualment environment for 3.6
  pip list # List installed packages
  pip install azure-identity # Install azure-identity package and its required modules
  source p311env/bin/activate # Activate the virtualment environment for 3.11
  pip list # List installed packages. Notice the packages from azure-identity are not installed
  pip install azure-identity # Install azure-identity package and its required modules
  pip list # List installed packages. Compare the list of packages and the versions
  ```

> Using python virtual environments is the correct way to implement different versions of python without messing with the default OS python installed

---

## Python Troubleshooting Exercise – Broken Python on RHEL 9

Create a RHEL 9 server.
  This will automatically create a VM.
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpython002.json)
  
### Scenario

A new RHEL 9.7 virtual machine has been deployed in Azure using an ARM template.  
The system boots correctly, but **Python-based tools start behaving unexpectedly**.

At first glance:
- `python3` exists
- Multiple Python versions are installed
- The system *appears* healthy

However, common workflows begin to fail:
- Python scripts do not behave as expected
- Some system utilities relying on Python may stop working
- Version checks give confusing results

Your task is **not** to reinstall the OS.

Your task is to **understand what changed**, **why it is dangerous**, and **how to recover the system properly**.

### Student Tasks

1. Identify which Python version RHEL 9 expects by default
2. Determine which Python version is currently being executed when running:
   ```bash
   python3
   ```
3. Inspect how the Python version selection is being controlled
4. Identify **what was altered incorrectly**
5. Restore the system to a **supported and safe configuration**
6. Explain **why this configuration is dangerous in production**

### Hints (Read Carefully)

- RHEL 9 **uses Python 3.9 as the system Python**
- Some OS tools **depend on the exact Python minor version**
- Changing `/usr/bin/python3` is **not the same** as installing another Python
- Replacing binaries in `/usr/bin` can silently break the OS
- The issue is **not** Python itself — it is **how it was integrated**

### About `alternatives` on RHEL

RHEL provides the `alternatives` system to manage **multiple implementations of the same command**.

Key points:
- `alternatives` controls **which binary is selected**
- It **does not validate** OS compatibility
- It **will not protect system Python**
- It is safe for user tools, **not for core OS dependencies**

Common commands you will need:
```bash
alternatives --display python3
alternatives --config python3
```

If `alternatives` points `python3` to a binary that the OS does not expect,  
**you can break system tools without any warnings**.

### What NOT to Do

- Do NOT overwrite `/usr/bin/python3.9`
- Do NOT replace system-managed binaries with custom builds
- Do NOT assume newer Python versions are safe for the OS

### Expected Outcome

By the end of this exercise, you should be able to explain:

- Why RHEL pins a specific Python version
- Why virtual environments are the correct solution
- Why modifying system Python is dangerous
- How to detect and recover from this situation

> **Real-world note:**  
> - This scenario closely mirrors production outages caused by “quick fixes” applied directly to `/usr/bin`.
> - You may need a rescue VM to properly fix this server.

---

## Python Troubleshooting Exercise – Broken Python on SLES 15

Create a RHEL 9 server.
  This will automatically create a VM.
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpython003.json)
  
### Scenario

A new **SLES 15 SP7** virtual machine has been deployed in Azure.
The system boots correctly and appears functional, but **Python-related behavior is inconsistent and unreliable**.

At first glance:
- `python3` exists
- Python scripts execute
- A newer Python version is present on the system

Despite this, multiple issues begin to surface:
- Python applications behave unexpectedly
- Some system tools may fail silently
- Version checks no longer make sense

Your task is **not** to reinstall the OS.

Your task is to **understand what was changed**, **why it is unsafe**, and **how to recover the system correctly**.

### What You Know

- This system is **SLES 15**
- Python is a **core OS dependency**
- System Python binaries were manually replaced

Nothing else should be assumed.

### Student Tasks

1. Identify which Python version **SLES 15 expects by default**
2. Determine which Python binary is executed when running:
   ```bash
   python3
   ```
3. Inspect Python binaries under `/usr/bin`
4. Identify **which files were overwritten**
5. Explain why this breaks OS assumptions
6. Restore Python to a **supported configuration**
7. Propose a **safe method** to use newer Python versions on SLES

### Hints (Read Carefully)

- SLES 15 **uses Python 3.6 as the system Python**
- Many SUSE tools rely on Python 3.6 specifically
- Replacing `/usr/bin/python3.6` bypasses package management
- Installing custom builds into `/usr` overrides distribution ownership
- The issue is **not Python 3.15** — it is **where and how it was installed**


### Why This Is Dangerous

The system Python:
- Is owned by the RPM database
- Is tested and supported only at specific versions
- Is required by YaST, zypper, and other system tooling

Manually copying binaries into `/usr/bin`:
- Breaks RPM integrity guarantees
- Prevents safe updates
- Creates undefined system behavior

This failure mode is **silent but catastrophic**.

### Investigation Guide – Using `zypper` and RPM on SLES

When core system files are modified, **package management is your primary forensic tool**.
This section intentionally avoids step-by-step recovery instructions.

Focus on **understanding**, not memorizing commands.

### Package Ownership Awareness

Key questions to investigate:
- Does this file belong to a package?
- Which package owns it?
- Does the file still match what SUSE shipped?

Key idea:
> If RPM owns the file, manual changes are **never safe**.

### Integrity and Verification Concepts

RPM is able to detect when files:
- Were replaced
- Were modified
- No longer match the package database

Important takeaway:
> A file can exist and still be **broken** from the OS perspective.


### Reinstallation as a Concept

`zypper` is capable of:
- Reinstalling packages
- Overwriting modified files
- Restoring ownership and checksums

Even if warnings or errors appear, **package reinstallation remains the authoritative recovery path**.

### What NOT to Do

- Do NOT overwrite `/usr/bin/python3.6`
- Do NOT replace `/usr/bin/python3.6m`
- Do NOT install custom Python builds into `/usr`
- Do NOT assume the OS will protect itself

### Expected Outcome

By the end of this exercise, you should be able to explain:

- Why SLES pins Python 3.6
- Why replacing system binaries is unsafe
- How RPM tracks file ownership
- How a broken system Python can be detected
- How this situation should be prevented in production

> **Real-world note:**
> This exact mistake has caused production outages by breaking package managers and system configuration tools.

Investigate carefully. Fix deliberately.


Take your time. Investigate. Fix it properly.

