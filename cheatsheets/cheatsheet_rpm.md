# RPM Cheat Sheet (RHEL / other EL / SLES)

## Purpose

This document is a quick reference for using `rpm` directly across RPM-based distros (RHEL/EL and SLES/openSUSE).
It focuses on what you typically need when:
- you only have an RPM file (no repo access / broken repo / rescue VM)
- you need to query what’s installed, what files belong to a package, or what package owns a file
- you need to verify integrity/signatures, inspect scripts/changelogs, or rebuild the rpmdb
- you’re troubleshooting break-fix scenarios where higher-level tools (dnf/yum/zypper) are unavailable or not working

Notes:
- `rpm` is the low-level tool. It does NOT resolve dependencies for you the way dnf/yum/zypper does.
- Use `rpm` when you must, otherwise prefer the distro package manager for normal installs/upgrades.

---

## Single table of RPM commands (URLs removed from command column)

| Area | Command | What it does |
|---|---|---|
| Help / basics | rpm --version | Show rpm version |
| Help / basics | rpm -? | Help / usage |
| List installed | rpm -qa | List all installed packages |
| List installed | rpm -qa --last | List installed packages sorted by install time |
| Query installed | rpm -q <pkg> | Check if a package is installed |
| Query installed | rpm -qi <pkg> | Show installed package info (name/version/summary/etc.) |
| Query installed | rpm -ql <pkg> | List files installed by a package |
| Query installed | rpm -qc <pkg> | List config files for a package |
| Query installed | rpm -qd <pkg> | List documentation files for a package |
| Query installed | rpm -qR <pkg> | Show “requires” (dependencies) of an installed package |
| Query installed | rpm -q --provides <pkg> | Show “provides” capabilities for an installed package |
| Query installed | rpm -q --scripts <pkg> | Show install/uninstall/verify scriptlets for a package |
| Query installed | rpm -q --changelog <pkg> | Show changelog for an installed package |
| Query by file | rpm -qf /path/to/file | Show which installed package owns a file |
| Query by file | rpm -qf "$(which <cmd>)" | Package that owns a command (installed) |
| Query capabilities | rpm -q --whatprovides <capability> | Find installed packages providing a capability |
| Query capabilities | rpm -q --whatrequires <capability> | Find installed packages requiring a capability |
| Query RPM file | rpm -qp <file.rpm> | Query an RPM file (not installed) |
| Query RPM file | rpm -qpi <file.rpm> | Show RPM file package info (not installed) |
| Query RPM file | rpm -qpl <file.rpm> | List files inside an RPM file (not installed) |
| Query RPM file | rpm -qpR <file.rpm> | Show dependencies required by an RPM file |
| Install | rpm -ivh <file.rpm> | Install an RPM (no upgrade logic) |
| Upgrade/install | rpm -Uvh <file.rpm> | Upgrade or install an RPM (common way to “install”) |
| Upgrade only | rpm -Fvh <file.rpm> | Freshen: upgrade only if already installed |
| Reinstall | rpm --reinstall <file.rpm> | Reinstall package from RPM file |
| Remove | rpm -e <pkg> | Erase/uninstall a package |
| Remove (danger) | rpm -e --nodeps <pkg> | Erase without dependency checks (use carefully) |
| Remove (scripts off) | rpm -e --noscripts <pkg> | Erase without running scripts (edge recovery use) |
| Verify package | rpm -V <pkg> | Verify installed package files against rpmdb |
| Verify all | rpm -Va | Verify ALL installed packages (noisy but useful) |
| Verify file owner | rpm -Vf /path/to/file | Verify the package that owns that file |
| Signature check | rpm -K <file.rpm> | Check RPM digests/signature (package file) |
| Signature check | rpm --checksig <file.rpm> | Check RPM signature (same intent as -K) |
| Import GPG key | rpm --import /path/to/keyfile | Import a public key for signature verification |
| List imported keys | rpm -qa gpg-pubkey* | List imported RPM GPG keys |
| Key details | rpm -qi gpg-pubkey-<id> | Show details about one imported key |
| Remove key | rpm -e gpg-pubkey-<id> | Remove an imported key |
| rpmdb init | rpm --initdb | Initialize rpm database |
| rpmdb rebuild | rpm --rebuilddb | Rebuild rpm database (common after corruption) |
| Restore metadata | rpm --restore <pkg> | Restore file metadata (owner/group/perms/etc.) from rpmdb |
| Reset perms | rpm --setperms <pkg> | Reset default permissions for package files |
| Reset ownership | rpm --setugids <pkg> | Reset default owner/group for package files |
| Custom output | rpm -q --qf "<format>" <pkg> | Query with a custom output format |

