# systemd
## Troubleshoot a broken systemd configuration

### Exercise overview

This lab deploys a **RHEL 9 virtual machine** in Azure that contains a **deliberately broken systemd configuration**.  
The goal is for students to use **systemd and general Linux troubleshooting tools** to identify what is broken, understand why the system is misbehaving, and reason about the impact of the change.

Nothing is documented inside the VM. Students must rely on observation, logs, and system behavior.

This exercise focuses on:
- systemd unit inspection
- journal analysis
- boot-time troubleshooting
- understanding how a single misconfiguration can cascade into system-wide issues

### Deploy the VM

Use the ARM template below to deploy the lab environment:

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fsystemd001.json)

The deployment creates:
- A RHEL 9 VM
- Public IP for SSH access
- Boot diagnostics enabled


### Student tasks

Students should **not** be told what is broken.

They are expected to:

- SSH into the VM
- Observe abnormal system behavior
- Identify failing or degraded services
- Trace the problem back to systemd configuration


### Suggested troubleshooting path (not a solution)

Students may naturally discover the issue by using tools such as:

- Check overall system state
  systemctl status
  systemctl list-units --failed

- Inspect boot and runtime logs
  journalctl -b
  journalctl -xe

- Identify socket-activated services
  systemctl list-sockets

- Inspect unit files and overrides
  systemctl cat <unit>
  systemctl show <unit>

- Validate systemd unit syntax
  systemd-analyze verify <unit file>

- Check recent file changes
  stat /lib/systemd/system/*
  ls -l /etc/systemd/system/


### Expected learning outcomes

By completing this exercise, students should be able to:

- Understand the role of systemd socket units
- Recognize symptoms of broken unit definitions
- Use journald effectively for root cause analysis
- Correlate boot-time behavior with systemd configuration
- Develop a structured troubleshooting approach instead of guessing


### Final validation

Once the issue is identified and fixed, students should be able to:

- Reload systemd configuration successfully
- Start the affected units without errors
- Reboot the VM without triggering failures
- Explain clearly what was broken, why it failed, and how they fixed it
