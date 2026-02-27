# Final
## Linux Recovery Exercise – Recover `node002`

Create the lab environment (2 VMs: `node001` and `node002`).
This deployment intentionally introduces instability on `node002`.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Ffinal001.json)

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
az vm repair create \
  --resource-group <rg> \
  --name node002

# After repair actions, restore the original VM
az vm repair restore \
  --resource-group <rg> \
  --name node002
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
