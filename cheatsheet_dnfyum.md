# DNF / YUM Cheat Sheet (RHEL)

## Purpose
This document is a quick reference for the most common **DNF/YUM** commands used on **Red Hat Enterprise Linux (RHEL)** for day-to-day admin and troubleshooting:

- Manage repositories (list/enable/disable/metadata cache)
- Search and inspect packages
- Install / remove software (and clean up deps)
- Apply updates (including security-only updates)
- Review transaction history (what changed)
- Automate patching (RHEL 7: yum-cron, RHEL 8+: dnf-automatic)

Notes:
- On **RHEL 8+**, **`yum` and `dnf` are interchangeable** (yum is effectively a compatibility layer). [1](https://www.man7.org/linux/man-pages/man8/yum.8.html)
- Microsoft also documents common YUM/DNF issues and RHUI validation workflows for Azure VMs. [2](https://learn.microsoft.com/en-us/troubleshoot/azure/virtual-machines/linux/yum-dnf-common-issues) | [3](https://learn.microsoft.com/en-us/troubleshoot/azure/virtual-machines/linux/yum-dnf-common-issues)

## How to use this cheat sheet

- Use the **Area** column to jump to the operation you need.
- Prefer **DNF** on RHEL 8/9/10 (but `yum` usually works as the same CLI).
- For scripts/automation, use `-y` (YUM) or non-interactive flags/options as needed.

---

## Single table of DNF/YUM commands

| Area | Command | What it does |
|---|---|---|
| Help / basics | dnf help | Show help and command list |
| Help / basics | dnf help <command> | Help for a specific DNF command |
| Help / basics | dnf shell | Open interactive DNF shell |
| RHEL 8+ note | yum (on RHEL 8+) | `yum` is effectively redirecting to / calling DNF (compat) |
| Repos | yum repolist all | List repositories (YUM) |
| Repos | dnf repolist | List enabled repositories (DNF) |
| Repos | dnf repolist --disabled | List disabled repositories |
| Repos | dnf repolist --all | List enabled + disabled repositories |
| Repos | dnf repoinfo | Show repository details |
| Cache / metadata | dnf makecache | Build/refresh metadata cache |
| Cache / metadata | dnf clean all | Clear cached metadata/packages |
| Updates (check) | yum check-update | Check for available updates without installing |
| Updates (check) | dnf check-update | Check for available updates without installing |
| Install | yum install <pkg> | Install a package (YUM) |
| Install | dnf install <pkg> | Install a package (DNF) |
| Install (local RPM) | yum localinstall <rpm-or-url> | Install a local RPM (or from URL) using YUM |
| Install (local RPM) | dnf install <path>.rpm | Install a local RPM; DNF resolves deps |
| Reinstall | yum reinstall <pkg> | Reinstall current version of a package |
| Reinstall | dnf reinstall <pkg> | Reinstall a package |
| Remove | yum remove <pkg> (or yum erase <pkg>) | Remove a package |
| Remove | dnf remove <pkg> | Remove a package |
| Autoremove | dnf autoremove | Remove unneeded dependency packages |
| Downgrade | yum downgrade <pkg> | Downgrade a package to an earlier version |
| Distro-sync | dnf distro-sync | Sync installed packages to repo versions (useful for drift) |
| Search | yum search <term> | Search packages by keyword (YUM) |
| Search | dnf search <term> | Search packages by keyword (DNF) |
| Package info | yum info <pkg> | Show package details (YUM) |
| Package info | dnf info <pkg> | Show package details (DNF) |
| List packages | dnf list | List installed + available packages |
| List installed | dnf list --installed | List installed packages (DNF) |
| List installed | yum list installed | List installed packages (YUM) |
| Provides (file owner) | dnf provides /path/to/file | Find package that provides a file |
| RPM (files in pkg) | rpm -ql <pkg> | List files installed by a package |
| RPM (owner of file) | rpm -qf /path/to/file | Find which package owns a file |
| Repo query | dnf repoquery <pkg> | Query repos for a package (versions, availability) |
| Group install | yum groupinstall "Group Name" | Install a YUM package group |
| Groups | dnf group list | List package groups (DNF) |
| Groups | dnf group info <group> | Show packages in a group |
| Modules (RHEL 8+) | dnf module list | List modules and streams |
| Modules (RHEL 8+) | dnf module info <module> | Show module details |
| Security updates | yum upgrade --security | Install security updates only (YUM) |
| Security updates | yum list-security --security (or yum list-sec) | List security updates (older flows) |
| Transaction history | yum history list all | List YUM transaction history |
| Transaction history | dnf history | List DNF transaction history |
| Transaction history | dnf history info <ID> | Show details of a transaction ID |
| Transaction history | dnf history undo <ID> | Undo a transaction (where supported) |
| RHUI troubleshooting (Azure) | Run the RHUI check script from Azure support-scripts repo (then execute with python) | Validate RHUI cert/rpm/config/connectivity (Azure PAYG RHEL) |
| RHUI troubleshooting (Azure) | sudo yumdownloader <pkgs...> | Download RPMs from a working VM (example workflow) |
| Automation (RHEL 7) | yum-cron | Service for automatic updates on older YUM-based systems |
| Automation (RHEL 8+) | dnf install dnf-automatic | Install DNF Automatic |
| Automation (RHEL 8+) | /etc/dnf/automatic.conf | Default config file for DNF Automatic |
| Automation (RHEL 8+) | systemctl enable --now dnf-automatic.timer | Enable automated runs via systemd timer |
| Non-interactive (YUM) | yum -y <command> | Auto-answer “yes” to prompts (non-interactive YUM usage) |
