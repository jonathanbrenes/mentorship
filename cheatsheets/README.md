# Linux Cheatsheets

This section contains quick-reference command guides for common Linux administration tools across different distributions.

These cheatsheets are designed for:

- Fast lookup during labs
- Interview preparation
- Production troubleshooting
- Cross-distribution comparison
- Day-to-day operational use

---

# Package Management – Debian / Ubuntu

## APT

> High-level package manager (repository-based)

🔗 [APT Cheatsheet](https://github.com/jonathanbrenes/mentorship/blob/main/cheatsheet_apt.md)

Covers:
- Repository update & refresh
- Install / remove packages
- Upgrade strategies
- Search & show package info
- Cache management
- Dependency troubleshooting

---

## DPKG

> Low-level Debian package management (.deb files)

🔗 [DPKG Cheatsheet](https://github.com/jonathanbrenes/mentorship/blob/main/cheatsheet_dpkg.md)

Covers:
- Install local `.deb`
- Remove / purge packages
- List installed packages
- Query package ownership
- Inspect package contents
- Repair broken installs

---

# Package Management – RHEL / CentOS / Rocky / Alma

## DNF / YUM

> Repository-based package manager (modern RHEL family)

🔗 [DNF / YUM Cheatsheet](https://github.com/jonathanbrenes/mentorship/blob/main/cheatsheet_dnfyum.md)

Covers:
- Install / remove packages
- Update & upgrade
- Repository management
- Package search
- Dependency inspection
- History rollback

---

## RPM

> Low-level package manager (.rpm files)

🔗 [RPM Cheatsheet](https://github.com/jonathanbrenes/mentorship/blob/main/cheatsheet_rpm.md)

Covers:
- Install local RPM
- Query installed packages
- Verify file integrity
- Check package ownership
- Inspect package metadata
- File ownership troubleshooting

---

# Package Management – SUSE / SLES / openSUSE

## Zypper

> Repository-based package manager for SUSE systems

🔗 [Zypper Cheatsheet](https://github.com/jonathanbrenes/mentorship/blob/main/cheatsheet_zypper.md)

Covers:
- Install / remove packages
- Refresh repositories
- Patch management
- Repository handling
- Locking packages
- Dependency resolution

---

# Text Editing

## VI / VIM

🔗 [VI Cheatsheet](https://github.com/jonathanbrenes/mentorship/blob/main/cheatsheet_vi.md)

Covers:
- Modes (normal, insert, visual, command)
- Navigation shortcuts
- Copy / paste / delete
- Search & replace
- File saving and exiting
- Advanced editing tips

---

# Cross-Distribution Comparison

| Feature | Debian/Ubuntu | RHEL Family | SUSE |
|----------|----------------|-------------|-------|
| High-level manager | `apt` | `dnf` / `yum` | `zypper` |
| Low-level manager | `dpkg` | `rpm` | `rpm` |
| Local package install | `dpkg -i` | `rpm -i` | `rpm -i` |
| Verify package files | `dpkg -V` | `rpm -V` | `rpm -V` |

---

# Recommended Usage Pattern

For day-to-day operations:

- Use **APT / DNF / Zypper** for repository-based installs
- Use **DPKG / RPM** only for:
  - Local package installs
  - Deep inspection
  - Integrity verification
  - Recovery troubleshooting

---

# Learning Strategy

If you are new:

1. Start with `apt` or `dnf`
2. Learn the low-level equivalent (`dpkg` or `rpm`)
3. Compare how SUSE uses `zypper`
4. Practice verifying file ownership when troubleshooting

---

# Mentorship Project

These cheatsheets are part of the broader Linux Mentorship project:

- Storage
- Networking
- Python
- SELinux
- Recovery
- Systemd
- Cross-distribution administration

Use them as reference material while completing hands-on exercises.
