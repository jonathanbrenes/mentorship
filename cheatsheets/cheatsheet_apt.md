# APT / APT-GET Cheat Sheet (Debian / Ubuntu)

## Purpose

This document is a quick reference for the most common package-management commands on Debian/Ubuntu systems.
It covers the “daily driver” workflows (update/upgrade/install/remove), plus the supporting tools you end up using
when troubleshooting: dpkg/dpkg-query for local package database queries, apt-cache for metadata queries, apt-file
for “which package provides this file?”, repo/source list locations, and unattended-upgrades for automation.

Notes:
- `apt` is the user-friendly front-end (interactive, nicer output). `apt-get` is the older/automation-stable CLI.
- Many repair / recovery tasks end up using dpkg/apt-file when the package name isn’t obvious or you’re fixing boot tooling.

---

## Single table of APT / Debian tools commands (URLs removed from command column)

| Area | Command | What it does |
|---|---|---|
| Help / basics | apt -h | Show apt help / commands |
| Help / basics | apt --version | Show apt version |
| Update (repo metadata) | apt update | Refresh package lists (does NOT upgrade packages) |
| Upgrade (safe) | apt upgrade | Upgrade installed packages (no removals) |
| Upgrade (may remove) | apt full-upgrade | Upgrade with removals if needed (aka “smart” upgrade) |
| Upgrade (legacy) | apt-get update | Refresh package lists (legacy) |
| Upgrade (legacy) | apt-get upgrade | Upgrade installed packages (legacy) |
| Upgrade (legacy) | apt-get dist-upgrade | “Smart” upgrade (legacy; may add/remove packages) |
| Install | apt install <pkg> | Install a package |
| Install (legacy) | apt-get install <pkg> -y | Install a package (non-interactive example) |
| Install local .deb | apt install ./package.deb | Install a local .deb file |
| Remove | apt remove <pkg> | Remove package, keep config files |
| Purge | apt purge <pkg> | Remove package + config files |
| Remove (legacy) | apt-get remove <pkg> -y | Remove package (legacy) |
| Purge (legacy) | apt-get purge <pkg> -y | Purge package (legacy) |
| Cleanup deps | apt autoremove | Remove packages no longer needed (unused deps) |
| Cleanup deps (legacy) | apt-get autoremove | Remove packages no longer needed (legacy) |
| Cache cleanup | apt clean | Remove downloaded package files (all) |
| Cache cleanup | apt autoclean | Remove old/obsolete downloaded package files |
| Search | apt search <keyword> | Search packages by keyword |
| Search | apt-file search <filename-or-path> | Which package provides a file |
| Show package | apt show <pkg> | Show package details |
| List installed | apt list --installed | List installed packages |
| List upgradable | apt list --upgradable | List packages with upgrades available |
| List versions | apt list --all-versions <pkg> | Show all available versions of a package |
| Pin / hold | apt-mark hold <pkg> | Hold a package (prevent upgrades) |
| Pin / unhold | apt-mark unhold <pkg> | Remove hold |
| Pin / show holds | apt-mark showhold | List held packages |
| Repo sources | /etc/apt/sources.list | Main APT sources file |
| Repo sources | /etc/apt/sources.list.d/ | Additional repo files directory |
| Repo edit | apt edit-sources | Edit sources (apt helper) |
