# systemd
---

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
  ```bash
  systemctl status
  systemctl list-units --failed
  ```

- Inspect boot and runtime logs
  ```bash
  journalctl -b
  journalctl -xe
  ```

- Identify socket-activated services
  ```bash
  systemctl list-sockets
  ```

- Inspect unit files and overrides
  ```bash
  systemctl cat <unit>
  systemctl show <unit>
  ```

- Validate systemd unit syntax
  ```bash
  systemd-analyze verify <unit file>
  ```

- Check recent file changes
  ```bash
  stat /lib/systemd/system/*
  ls -l /etc/systemd/system/
  ```


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

---


## Create a custom systemd service for a Python web server

### Exercise overview

This lab focuses on **creating and managing a custom systemd service unit**. Use previous fixed VM

A Python-based web server will serve a simple HTML page on **port 9999**, but **no systemd unit exists initially**.  
Students must create a **new systemd service**, configure it correctly, and ensure it runs securely as a **dedicated non-root user**.

This exercise reinforces:
- Writing custom systemd service units
- Running services as non-root users
- Understanding `ExecStart`, `User`, `WorkingDirectory`
- Service lifecycle management
- Basic security best practices with systemd

### Initial state (what exists)

After deployment, the system contains:

- Python installed
- No web service running
- No systemd unit related to the web server



### Objective

Students must:
- Create the HTML file
- Create a **new systemd service unit**
- Use this Python web server which it is listening on **port 9999**
  ```bash
  python3 -m http.server 9999
  ```
- Use **one single-line Python command**
- Run the service as a **new user called `webserver`**
- Serve files from `/var/www/html/`
- Ensure the service:
  - Starts successfully
  - Can be restarted
  - Persists across reboots



### Constraints

- Do NOT use Apache, NGINX, or httpd
- Do NOT run the service as root
- Do NOT use shell scripts as wrappers
- The Python command must be placed **directly in `ExecStart=`**
- The service must be managed only through systemd



### Student tasks

#### 1. Create the service user

Students should verify or create a dedicated user for the service.

Key points:
- No login shell required
- No home directory required
- Service isolation is the goal


#### 2. Verify web content location

Confirm that `/var/www/html` exists and contains `index.html`.
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Python systemd lab</title>
  </head>
  <body>
    <h1>Hello World</h1>
    <p>Python webserver running on port 9999 using systemd.</p>
  </body>
</html>
```

Students should verify:
- File ownership
- Read permissions
- That the service user can access the files


#### 3. Create a new systemd service unit

Students must create a **new unit file** under:

/etc/systemd/system/

The unit must:
- Define a meaningful service name
- Use a **single Python command** to start the web server
- Bind to **port 9999**
- Serve content from `/var/www/html`
- Run as user `webserver`

No service template is provided.


#### 4. Reload and start the service

After creating the unit file, students must:
- Reload systemd configuration
- Start the service
- Check service status

They should confirm:
- The service is active
- No permission or execution errors exist
- The correct user is being used

#### 5. Validate functionality

Students should validate the service by:
- Checking listening ports
- Accessing the web server locally
- Confirming the content is served correctly

The expected output in a browser or via curl is:

Hello World


### Troubleshooting hints (do not give the solution)

If the service fails to start, students should investigate:
- `journalctl` for service-specific logs
- File permissions under `/var/www/html`
- The working directory used by the service
- Whether the selected port requires elevated privileges
- systemd unit syntax errors
- Check firewall and SELinux
- Check Red Hat documentation https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html/using_systemd_unit_files_to_customize_and_optimize_your_system/working-with-systemd-unit-files


### Expected learning outcomes

By completing this exercise, students should be able to:

- Write a functional systemd service unit from scratch
- Understand how systemd executes commands
- Run services securely using non-root users
- Debug systemd startup failures using logs
- Explain why service isolation matters


### Final validation

The exercise is complete when:

- The systemd service is enabled and running
- Port 9999 is listening
- The service runs as user `webserver`
- `/var/www/html/index.html` is served correctly
- The service survives a reboot

This lab simulates a common real-world scenario where a simple service must be productionized using systemd without relying on full web server stacks.
