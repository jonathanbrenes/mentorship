## Title and Scenario

This lab simulates a production-style Linux recovery incident in a high-availability Azure environment. Two virtual machines (`node001` and `node002`) are deployed using an ARM template. While `node001` behaves normally, `node002` exhibits unstable boot behavior due to intentional post-deployment system-level changes.

This exercise focuses on disciplined incident investigation, boot-chain analysis, recovery workflows, and validation under repeated reboot scenarios.

Your objective is to recover `node002` to a stable, supportable state without redeploying the environment.

## Deployment

Linux Recovery Exercise – Recover `node002`

Create the lab environment (2 VMs: `node001` and `node002`).  
This deployment intentionally introduces instability on `node002`.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Ffinal001.json)

## Skills Required

- Advanced Linux troubleshooting
- Understanding of systemd service behavior and boot targets
- Familiarity with RHEL-based systems
- Experience analyzing unstable boot conditions
- Ability to compare healthy vs broken node state
- Familiarity with Azure serial console and boot diagnostics
- Experience using Azure VM Repair workflow
- Familiarity with `sos report` analysis and comparison

## Recommended Prerequisites

- Experience with RHEL 8 systems
- Comfort using Azure CLI
- Understanding of initramfs and boot processes
- Familiarity with system limits and service constraints
- Basic HA cluster awareness concepts

## Objectives

By completing this exercise, you should be able to:

- Identify unstable boot behavior patterns
- Use Azure diagnostic tools effectively
- Compare healthy and broken node states
- Use Azure VM Repair safely
- Generate and compare `sos report` artifacts
- Restore stable boot and login behavior
- Validate repeatable recovery across multiple reboots
- Document root causes and prevention strategy

## Environment Overview

- OS image: **RHEL-HA 8.8**
- VM size: **Standard_E2bds_v5**
- Two nodes: `node001` (healthy baseline) and `node002` (unstable)
- Shared VNet/subnet
- NSG rules allow SSH and lab traffic
- Instability introduced via system-level modifications on `node002`

Failure symptoms may vary between reboots:
- Unexpected reboots
- Boot delays
- Service startup failures
- SSH login instability
- Inconsistent system behavior

## Your Mission

1. Confirm `node001` is healthy and use it as a baseline.
2. Investigate `node002` boot behavior using:
   - Azure Serial Console
   - Boot Diagnostics
3. Use Azure VM Repair if standard access fails.
4. Collect diagnostic artifacts from both nodes.
5. Compare healthy vs unstable configuration.
6. Recover `node002` to a stable configuration.
7. Reboot repeatedly to confirm stability.
8. Validate SSH reliability.
9. Produce incident-style documentation.

## Analytical Guidance

Treat this like a real production incident:

- Do not assume a single root cause.
- Failures may present differently per reboot.
- Compare system behavior across both nodes.
- Validate each change before proceeding.
- Confirm persistence across reboot cycles.

Focus investigation on:

- system limits
- `sshd` configuration and constraints
- systemd defaults and override behavior
- boot artifacts
- initramfs configuration
- kernel and service interactions

Use `node001` as the authoritative configuration baseline.

## Validation Criteria

You must demonstrate:

- Stable, repeatable boot on `node002`
- Consistent SSH access across multiple reboots
- No unexpected service failures during startup
- Recovery actions persist after reboot
- System state aligns with baseline expectations

Recovery quality is measured by repeatability — not a single successful boot.

## Documentation Expectations

Document your findings in structured format:

- Initial symptoms observed
- Evidence collected
- Diagnostic methods used
- Configuration differences discovered
- Root cause category (high-level)
- Remediation actions (high-level)
- Post-fix validation evidence
- Prevention recommendations

## What Not To Do

- Do not redeploy as first action
- Do not disable security controls permanently
- Do not apply unverified changes blindly
- Do not skip reboot validation
- Do not assume problem is singular

## Real-World Context

This mirrors real-world HA deployment failures where:

- Boot configuration drift occurs post-deployment
- Service limits cause login instability
- System-level changes create non-deterministic failures
- initramfs or boot-chain inconsistencies impact stability

Incidents of this nature require structured comparison, artifact collection, and controlled recovery — not guesswork.

## Optional Advanced Exploration

- Automate healthy vs broken configuration diff workflows
- Analyze `sos report` contents for systemic drift patterns
- Create a post-recovery hardening validation checklist
- Evaluate how configuration management tools could prevent recurrence

---

## Linux Recovery Exercise – Recover `node002`

Create the lab environment (2 VMs: `node001` and `node002`).
This deployment intentionally introduces instability on `node002`.

Deployment: See the Deployment section.

---

## Scenario
A new HA-style lab was deployed in Azure using ARM.

At first glance:
- `node001` is reachable and looks healthy
- `node002` may reboot unexpectedly or fail during boot
- Across retries, failure symptoms may differ (boot delay, service failures, login issues, unstable startup)

Your task is **not** to redeploy.

Your task is to **recover `node002`**, bring it to a stable boot state, and validate cluster/lab readiness.

---

## What You Know
- OS image: **RHEL-HA 8.8**
- VM size: **Standard_E2bds_v5**
- Both nodes are in the same VNet/subnet with NSG rules for SSH and lab traffic
- The issue was introduced by post-deployment system-level changes on `node002`

Do not assume a single root cause.

---

## Student Tasks
1. Confirm `node001` is healthy and use it as your control baseline
2. Investigate `node002` boot behavior from Azure (serial console + boot diagnostics)
3. Use **Azure VM Repair** workflow if normal boot access is not possible
4. Collect diagnostics from both nodes using `sos report` and compare healthy vs broken state
5. Identify what system changes on `node002` are causing unstable boot/reboot behavior
6. Recover `node002` to a stable, supportable configuration
7. Reboot `node002` multiple times and prove boot consistency
8. Validate SSH access reliability after recovery
9. Document:
   - root cause(s)
   - remediation steps
   - prevention recommendations

---

## Hints (Read Carefully)
- Failures can appear differently between reboots
- Focus on:
  - system limits
  - `sshd` service constraints
  - systemd defaults
  - boot artifacts / initramfs / kernel state
- Use `node001` to compare expected configuration and service behavior
- Treat this as a production incident: verify, change, re-test, and confirm persistence

---

## Recovery Tools You Should Use

### 1) Azure VM Repair (when `node002` cannot boot cleanly)
Use Azure VM Repair to mount/repair the OS disk safely from a repair workflow.

```bash
# Install (if needed)
az extension add --name vm-repair

# Create repair VM workflow
az vm repair create   --resource-group <rg>   --name node002

# After repair actions, restore the original VM
az vm repair restore   --resource-group <rg>   --name node002
```

### 2) `sos report` comparison: healthy (`node001`) vs broken (`node002`)
Generate a report from both nodes and compare service/system differences.

```bash
# On node001 (healthy baseline)
sudo sos report --batch --tmp-dir /var/tmp

# On node002 (if booted)
sudo sos report --batch --tmp-dir /var/tmp
```

### 3) `sos report` from chroot (important)
If `node002` is not bootable, mount its root disk in repair mode, `chroot` into it,
and run `sos report` from that environment.

> Note: You can create a `sos report` from a chroot environment and still use it
> for healthy-vs-broken comparison.

---

## What NOT to Do
- Do **not** redeploy the environment as first action
- Do **not** disable security controls permanently just to “make it work”
- Do **not** apply unverified fixes from random sources
- Do **not** skip post-fix reboot validation

---

## Expected Outcome
By the end of this exercise, you should be able to explain:
- Why `node002` boot behavior was unstable
- Which exact configuration(s) caused the problem
- How you restored stable boot/login behavior
- How to prevent recurrence in real environments

> Real-world note:
> This mirrors incident patterns where startup limits and boot-chain changes combine into non-deterministic failures.
> Recovery quality is measured by repeatable, stable reboots — not by a single successful login.
