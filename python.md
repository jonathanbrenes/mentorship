## Title and Scenario

This lab set focuses on practical Python operations and failure modes that Linux engineers commonly run into in production environments: performance differences across Python versions, isolated dependency management, and the risks of modifying “system Python” on enterprise distributions.

You will work across SLES 15 and RHEL 9 scenarios. Some parts are benchmark-style (performance and virtual environments), while other parts are troubleshooting-style (Python-based tooling unexpectedly failing after changes to system Python integration).

The goals are to build confidence with safe, supportable patterns (virtual environments) and to practice evidence-driven recovery when Python integration is broken on a running system.

## Deployment

Enhanced Performance in Python 3.6 vs 3.11 (SLES 15)

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpython001.json)

Python Troubleshooting Exercise – Broken Python on RHEL 9

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpython002.json)

Python Troubleshooting Exercise – Broken Python on SLES 15

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpython003.json)

## Skills Required

- Basic Linux command line fluency (navigation, editing files, reading outputs)
- Python version awareness (major/minor versions, how tools pick an interpreter)
- Understanding of “system Python” vs application Python
- Safe dependency isolation concepts (virtual environments)
- Package-management thinking (what the OS owns vs what applications own)
- Evidence-driven troubleshooting (verify assumptions, trace what is being executed, confirm impacts)

## Recommended Prerequisites

- Comfortable connecting to a VM via SSH and running commands
- Familiarity with Python basics (running scripts, installing packages)
- Conceptual understanding of:
  - How the OS provides runtime dependencies (RPM/APT packages)
  - Why distributions pin specific versions of core components

## Objectives

By the end of this lab set, you should be able to:

- Demonstrate performance differences between Python runtimes using the same workload
- Create and use isolated Python environments for different interpreter versions
- Explain why enterprise distributions pin a specific “system Python” version
- Diagnose why `python3` behavior becomes inconsistent on an enterprise Linux system
- Restore a safe, supported Python integration approach (without reinstalling the OS)
- Articulate preventive practices that avoid these failures in production

## Environment Overview

- Azure virtual machines deployed via provided templates
- OS coverage:
  - SLES 15 (Python 3.6 as system Python, with newer Python present for comparison)
  - RHEL 9 (Python 3.9 as system Python, with additional versions installed)
- Constraints:
  - Treat these as production-like systems: avoid destructive “quick fixes”
  - Do not reinstall the OS
  - Use investigative work to understand what changed and why it matters

## Your Mission

Complete the exercises in order:

1. Measure and observe Python 3.6 vs 3.11 runtime behavior on the same workload (SLES 15).
2. Build isolated Python environments for multiple interpreter versions and confirm dependency separation.
3. For each troubleshooting scenario (RHEL 9 and SLES 15), determine what is unsafe about the current Python integration and recover the system to a supported state without reinstalling the OS.

Success means:
- You can explain what you observed
- You can reproduce your validation steps
- The system behaves consistently and predictably after recovery

## Analytical Guidance

Use a disciplined workflow:

- Identify what the OS expects to be true (system Python version and ownership)
- Verify what is actually happening when you run `python3`
- Trace the selection mechanism (PATH, symlinks, system tooling that selects the interpreter)
- Confirm impact on Python-based tools and workflows
- Restore supportability by aligning runtime selection with OS expectations and using isolation for non-system Python needs
- Validate across reboots and repeated runs (consistency matters more than a single success)

Favor approaches that:
- Preserve package manager ownership
- Avoid overwriting core binaries
- Keep application Python separate from system Python

## Validation Criteria

You should be able to demonstrate the following outcomes, with evidence:

- Performance exercise:
  - The same benchmark script runs under both Python versions and produces output without errors
  - The Python 3.11 run completes faster than Python 3.6 in a noticeable way (timing evidence)

- Virtual environment exercise:
  - Each virtual environment activates successfully
  - `python3 --version` matches the expected interpreter for each environment
  - `pip list` differs across environments until you intentionally install packages in each

- Troubleshooting exercises (RHEL 9 and SLES 15):
  - `python3` resolves consistently to the expected system Python for the OS
  - Python-based workflows behave consistently (no “confusing” version behavior)
  - Package integrity/ownership assumptions are restored (system binaries are managed by the OS)
  - Recovery is stable across a reboot and re-validation

## Documentation Expectations

Document your work like a small incident report:

- Summary
  - What was broken, and what “broken” looked like (symptoms)
- Evidence collected
  - What you checked and what it showed (version output, selection path, ownership indicators)
- Hypotheses tested
  - Competing explanations and why you ruled them in/out
- Recovery approach (high-level)
  - What category of change restored correctness (no step-by-step fix instructions)
- Validation proof
  - Repeated checks, reboot confirmation, expected behavior restored
- Preventive considerations
  - How to avoid this class of failure in production

## What Not To Do

- Do not reinstall the OS
- Do not overwrite system Python binaries (or their versioned variants)
- Do not replace system-managed files under `/usr/bin` with custom builds
- Do not treat “newer Python” as automatically safe for OS tooling
- Do not apply changes without understanding package ownership and OS expectations

## Real-World Context

These scenarios map to common production incidents:

- Performance pressure leading teams to upgrade runtimes without isolating dependencies
- “Quick fixes” that replace `/usr/bin/python3` (or versioned binaries) and silently break OS tooling
- Package-management integrity failures that only surface later (updates, configuration tools, automation)
- Mixed Python versions on the same host causing non-deterministic behavior across scripts and utilities

The correct production pattern is typically:
- Keep system Python aligned with distribution support
- Use virtual environments (or equivalent isolation) for application runtimes
- Treat `/usr/bin` as OS-owned and protect package integrity

## Optional Advanced Exploration

- Compare different benchmark workloads (CPU-heavy vs IO-heavy) to see whether runtime gains vary
- Investigate how different Linux tools on each distribution depend on Python (inventory the blast radius)
- Evaluate operational guardrails:
  - Configuration management checks preventing `/usr/bin` overwrites
  - Monitoring to detect unexpected interpreter path changes
  - Baseline integrity verification in CI/CD images
- Consider how you would safely provide newer Python versions at scale:
  - Per-application venvs
  - Dedicated runtime directories under `/opt`
  - Image-based deployments with clear ownership boundaries

---

# Python
## Enhanced Performance in Python 3.6 vs 3.11
- Create a SLES 15 server which it will have python 3.6 and 3.11 
  Deploy this VM. This will automatically create a VM.

  Deployment: See the Deployment section.

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

  Deployment: See the Deployment section.

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

Create a SLES 15 server.
  This will automatically create a VM.

  Deployment: See the Deployment section.

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
- The issue is **not** Python 3.15** — it is **where and how it was installed**


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
