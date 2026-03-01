## Title and Scenario

This lab focuses on diagnosing and resolving a webserver access issue caused by SELinux policy enforcement on an enterprise Linux host. You will work with a pre-deployed RHEL 9.4 VM running `httpd`, where the OS appears healthy and the service may be running, but access to the site is blocked due to SELinux constraints.

The goal is to practice evidence-driven troubleshooting using logs and SELinux tooling, and to restore a safe, supportable configuration without disabling SELinux globally.

## Deployment

Create a server running a webserver with SELinux enabled

Deploy this VM. This ARM template will automatically create a **RHEL 9.4 VM**, install **httpd**, configure the firewall, and deploy a sample HTML page. This environment is used to demonstrate **SELinux port labeling, file contexts, and httpd permissions**.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fselinux001.json)

## Skills Required

- Basic Linux service inspection and log reading
- Familiarity with SELinux concepts (enforcing vs permissive, contexts, policy decisions)
- Ability to interpret SELinux-related logging and diagnostics (including `setroubleshoot`)
- Understanding of why SELinux restrictions can block working services (ports, file contexts, permissions)

## Recommended Prerequisites

- Comfort connecting to a VM via SSH and running commands
- Familiarity with `systemctl` and basic networking checks (`ss`)
- Basic understanding of what SELinux is and why it exists (MAC vs DAC)
- Awareness that “disabling SELinux” is not an acceptable production fix

## Objectives

By the end of this exercise, you should be able to:

- Confirm whether `httpd` is running and listening as expected
- Confirm SELinux is enabled and determine the current mode
- Identify SELinux denials related to `httpd` in system logs
- Use evidence from `setroubleshoot` guidance to implement a safe, local, supportable fix path
- Validate the webserver responds correctly after policy-aligned changes

## Environment Overview

- Azure VM: RHEL 9.4
- Service: `httpd` installed and started by deployment
- Firewall: configured by deployment
- SELinux: enabled (enforcing expected)
- Access pattern: HTTP over the configured port used by `httpd`

## Your Mission

Use the deployed environment to:

- Validate service state and listener state
- Confirm SELinux status and the allowed `httpd` port types
- Collect SELinux denial evidence from logs
- Apply a safe, policy-aligned change using `setroubleshoot` guidance (without turning SELinux off)
- Validate that the webserver is reachable and renders the expected content

## Analytical Guidance

Work methodically:

- Start with service health and listener visibility, then validate SELinux mode.
- Use the SELinux port allow-list to determine whether the listening port aligns with allowed `httpd` ports.
- Use log evidence to identify SELinux denials, then rely on `setroubleshoot` output as your primary guidance for safe policy-aligned changes.
- After each change, re-test the webserver and re-check logs for any new SELinux denials.
- Treat this like production: favor minimal, targeted policy alignment over broad exemptions.

## Validation Criteria

You should be able to demonstrate (with evidence):

- `httpd` is running and has an active listener
- SELinux is enabled and you have identified the current mode
- You have collected SELinux denial evidence from logs
- SELinux policy is aligned such that:
  - the configured `httpd` port is permitted, and
  - the required file contexts/permissions allow access
- Final validation: the webserver responds successfully and displays “Hello World”

## Documentation Expectations

Document your work in a repeatable, support-style format:

- Initial symptoms and observed behavior (what worked, what failed, how you tested)
- Evidence collected (service output, SELinux status, relevant log excerpts)
- What you changed (high-level, policy-aligned intent; avoid step-by-step remediation narrative)
- Validation results (what you re-tested and the final observed state)
- Preventive notes (what guardrails would prevent recurrence)

## What Not To Do

- Do not disable SELinux globally as a “fix”
- Do not rely on unscoped, broad policy changes without evidence
- Do not assume the firewall is the only cause if `httpd` is running
- Do not skip validation after each change (verify impact and ensure no new denials appear)

## Real-World Context

SELinux-related failures commonly occur when:

- A service is moved to a non-standard port without updating SELinux port labeling
- Content is placed in a directory with an incorrect SELinux file context
- Permissions appear correct under DAC (Unix permissions), but SELinux MAC blocks access

In production, the correct approach is to use evidence from SELinux tooling and logs to apply the smallest, targeted, supportable change that restores functionality while keeping SELinux enforcing.

## Optional Advanced Exploration

- Compare SELinux denial patterns between port-related blocks vs file-context blocks.
- Review how `httpd` allowed ports and file contexts are represented in policy.
- Consider what “golden image” or configuration management checks could detect drift in:
  - `httpd` port configuration
  - content paths and SELinux file contexts
  - unexpected SELinux mode changes

---

# SELinux  
## Fix webserver blocked by SELinux
### Create a server running a webserver with SELinux enabled

Deploy this VM. This ARM template will automatically create a **RHEL 9.4 VM**, install **httpd**, configure the firewall, and deploy a sample HTML page. This environment is used to demonstrate **SELinux port labeling, file contexts, and httpd permissions**.

Deployment: See the Deployment section.

- Validate HTTPD is running
  After deployment, SSH into the VM and validate the service:

  ```bash
  systemctl status httpd
  ss -tulnp | grep httpd
  ```

- Check SELinux mode
  ```bash
  getenforce
  sestatus
  ```

- Show which ports httpd is allowed to listen on
  ```bash
  semanage port -l | grep http_port_t
  ```

- Expected output includes:
  ```
  80, 81, 443, 488, 8008, 8009, 8443, 9000
  ```


### Fix SELinux Blocking Unauthorized Ports

- Find httpd related messages on the logs
  ```bash
  grep httpd /var/log/messages
  ```

- Using the information provided by setroubleshoot create a new local policy to allow the tcp port

- Once fixed restart the httpd service



### Fix File Context Demonstration

Make sure you can connect the server using the configured port used by httpd

- Check again ```/var/log/messages```. Find more SELinux errors
  ```bash
  grep SELinux /var/log/messages
  ```

- Using the information provided by setroubleshoot, fix the problem

### Final check
Finally test again the webserver make sure you can see a "Hello World"
