# DPKG Cheat Sheet (Debian / Ubuntu)

## Purpose

This is a quick reference for `dpkg`, the Debian package manager (low/medium-level), used when you:
- need to install/remove a local .deb directly (offline, rescue, broken repos)
- need to query “what’s installed / what owns this file / what files belong to this package”
- need to recover from partial installs (half-configured / unpacked) and finish configuration
- need to inspect dpkg history/logs, or check where dpkg keeps its database

Notes:
- dpkg does NOT resolve dependencies automatically. When a .deb install fails due to deps, you usually fix deps with apt afterwards.
- dpkg is also used as a front-end to dpkg-query and dpkg-deb in some cases.

---

## Single table of DPKG commands (URLs removed from command column)

| Area | Command | What it does |
|---|---|---|
| Help / basics | dpkg --help | Show dpkg help / usage |
| Help / basics | dpkg --version | Show dpkg version |
| Install local .deb | sudo dpkg -i package.deb | Install a local .deb package |
| Unpack only | sudo dpkg --unpack package.deb | Unpack a .deb without configuring it |
| Configure one pkg | sudo dpkg --configure <pkg> | Configure an unpacked package |
| Configure all pending | sudo dpkg --configure -a | Configure all unpacked-but-unconfigured packages (common recovery step) |
| Process triggers | sudo dpkg --triggers-only -a | Process pending triggers only |
| Remove (keep config) | sudo dpkg -r <pkg> | Remove package but keep config files |
| Purge (remove config) | sudo dpkg -P <pkg> | Purge package including config files |
| List packages | dpkg -l | List packages (human-friendly table) |
| List packages (filter) | dpkg -l | grep <term> | Filter dpkg list output |
| Package status | dpkg -s <pkg> | Show package status info |
| List files in pkg | dpkg -L <pkg> | List files installed by a package |
| Who owns file | dpkg -S /path/to/file | Find which installed package owns a file |
| Query db (pattern) | dpkg-query -l '<pattern*>' | List packages matching a pattern (dpkg database query) |
| Query db (installed ver) | dpkg-query -W '<pattern*>' | Show package name + installed version (customizable) |
| Query db (format) | dpkg-query -W -f='<format>' <pkg> | Custom output format for scripting/reporting |
| Query status (db) | dpkg-query -s <pkg> | Package status (dpkg database) |
| Query files (db) | dpkg-query -L <pkg> | List files installed by a package (db query) |
| Query file owner (db) | dpkg-query -S /path/to/file | Find package owning a file (db query) |
| Audit broken state | dpkg --audit | Show packages in broken/partial states (quick sanity check) |
| Export selections | dpkg --get-selections | Export package selections (for rebuild/migration) |
| Import selections | dpkg --set-selections | Import package selections (reads from stdin) |
| dpkg database location | /var/lib/dpkg/ | Directory holding dpkg database files |
| dpkg status DB | /var/lib/dpkg/status | Main status database file used by dpkg-query/dpkg |
| dpkg activity log | /var/log/dpkg.log | dpkg action log (install/upgrade/remove/purge timestamps) |
| dpkg lock (common) | /var/lib/dpkg/lock | Common dpkg lock file location (lock-related issues) |
| dpkg lock (common) | /var/lib/dpkg/lock-frontend | Common frontend lock file location |
| apt archive lock | /var/cache/apt/archives/lock | Common apt archive lock file location |
| usrmerge check (casework) | dpkg-query -s usrmerge | grep Status | Check usrmerge package status (useful during /usr merge issues) |
| usrmerge fix (casework) | /usr/lib/usrmerge/usrmerge | Manually run usrmerge helper (when directed by scenario) |

---

## Optional: inspect a .deb without installing (helper tool)

| Area | Command | What it does |
|---|---|---|
| Inspect .deb | dpkg-deb -I package.deb | Show package metadata/control info |
| Inspect .deb | dpkg-deb -c package.deb | List contents of the .deb archive |
