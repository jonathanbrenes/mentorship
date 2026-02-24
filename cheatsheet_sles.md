# Zypper Cheat Sheet (SLES / openSUSE)

## Purpose

This document is a quick reference for the most common `zypper` commands used on SUSE Linux Enterprise Server (SLES) and openSUSE. It’s aimed at day-to-day admin and troubleshooting workflows:

- Manage repositories (list/add/remove/enable/disable/priority/refresh)
- Search and inspect packages
- Install / remove software
- Apply updates and patches (including security patches)
- Use package locks to prevent unexpected upgrades
- Audit “what changed” using zypper/libzypp history logs

In many real-world incidents, issues start with repositories (missing, stale, wrong priority, not refreshed, etc.), so repo commands are front-and-center. This also includes history logging because “what changed?” is often the key question during regressions.

## How to use this cheat sheet

- Use the **Area** column to jump straight to what you need (Repos, Search, Install, Patch, etc.).
- Commands shown with aliases (e.g., `lr` vs `repos`) are interchangeable; both are listed where commonly used.
- For automation, prefer non-interactive flags (e.g., `--non-interactive`) and be careful with any option that disables GPG checks.

---

## Single table of zypper commands

| Area | Command | What it does |
|---|---|---|
| Help / basics | zypper | Show global options + command list |
| Help / basics | zypper help <command> | Help for a specific command |
| Help / basics | zypper shell (or zypper sh) | Open interactive zypper shell session |
| Repos | zypper lr (or zypper repos) | List configured repositories |
| Repos | zypper lr -u | List repos including URI |
| Repos | zypper lr -P | List repos with priority (and sort by it) |
| Repos | zypper ref (or zypper refresh) | Refresh repository metadata |
| Repos | zypper ref -f <alias> | Force refresh a repo alias |
| Repos | zypper ar <URL> <alias> (or zypper addrepo) | Add a repository |
| Repos | zypper rr <alias> (or zypper removerepo) | Remove a repository |
| Repos | zypper mr -d <#|alias> | Disable a repository |
| Repos | zypper mr -e <#|alias> | Enable a repository |
| Repos | zypper mr -p <prio> <alias> | Set repository priority |
| Repos / caches | zypper clean -a | Clean all caches |
| Repos / services | zypper refresh --services | Refresh services metadata (when services are used) |
| Registration (SLES) | SUSEConnect -s | Show SUSE registration status |
| Search / query | zypper se <term> (or zypper search <term>) | Search packages |
| Search / query | zypper se -i <term> | Search only installed packages |
| Search / query | zypper se -r <alias> <term> | Search packages limited to a repo |
| Inspect | zypper if <pkg> (or zypper info <pkg>) | Show detailed package information |
| Inspect | zypper wp <capability> (or zypper what-provides) | Show which package provides a capability |
| Install/remove | zypper in <pkg> (or zypper install <pkg>) | Install a package |
| Install/remove | zypper rm <pkg> (or zypper remove <pkg>) | Remove a package |
| Install/remove | zypper in <repo>:<pkg> | Install a package from a specific repo |
| Install/remove | zypper in <path-or-uri-to.rpm> | Install from an RPM file path/URI |
| Version/arch select | zypper in 'pkg<version' | Install using a version constraint (capability-style) |
| Version/arch select | zypper in pkg.<arch>=<version> | Install exact version + architecture |
| Version/arch select | zypper in -n <exact-name> | Select by exact package name (--name) |
| Patterns | zypper in -t pattern <pattern> | Install a pattern (package group) |
| Patterns | zypper pt (or zypper patterns) | List patterns |
