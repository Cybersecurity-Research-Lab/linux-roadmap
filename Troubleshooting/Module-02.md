# 🛠️ Module Troubleshooting Guide

A single reference for diagnosing common problems across every topic in this module. Organized by lesson so you can jump straight to the relevant section.

---

## Users and Groups

**"Permission denied" when running `useradd`/`usermod`/`groupadd`**
- Cause: not running as root/sudo.
- Fix: prefix with `sudo`. If `sudo` itself fails, confirm your account is in the `sudo` or `wheel` group (`groups $(whoami)`).

**New group membership doesn't seem to apply**
- Cause: group membership is loaded at login; a user already logged in won't see a newly added group until they log out and back in (or start a new shell with `newgrp groupname` as a temporary workaround).
- Fix: log out/in, or use `newgrp` for the current session.

**`usermod -G` wiped out a user's other group memberships**
- Cause: `-G` without `-a` *replaces* all supplementary groups instead of adding to them.
- Fix: re-add the missing groups manually with `usermod -aG group1,group2,group3 username`; going forward, always use `-aG` together.

**Locked out after editing `/etc/passwd` or `/etc/group` by hand**
- Cause: manual edits can introduce syntax errors that break every login.
- Fix: always use `vipw` / `vigr` instead of a plain text editor — they validate syntax before saving and lock the file to prevent concurrent corruption. If already locked out, boot into single-user/rescue mode to repair the file.

---

## File Permissions

**"Permission denied" even though `ls -l` shows you have access**
- Cause: you likely lack *execute* permission on a parent directory, which blocks traversal even if the file itself is readable.
- Fix: check every directory in the path with `namei -l /full/path/to/file` — it shows permissions for each path segment at once.

**`chmod`/`chown` changes don't seem to take effect**
- Cause: often a caching illusion — a program (e.g., a text editor, a mounted network share) may be showing stale cached metadata.
- Fix: re-check with `stat filename` directly; for network filesystems, remounting or waiting for cache expiry may be needed.

**SUID bit disappears after copying a file**
- Cause: some copy tools and package managers strip SUID/SGID bits by default as a safety measure, and some filesystems (e.g., certain network mounts, or `noexec`/`nosuid` mount options) block SUID entirely.
- Fix: check mount options with `mount | grep <mountpoint>` for `nosuid`; re-apply the bit explicitly with `chmod u+s` after copying if appropriate and intentional.

**"Operation not permitted" even as root**
- Cause: an immutable file attribute (`chattr +i`) or a Mandatory Access Control system (SELinux/AppArmor) is blocking the action, independent of standard permission bits.
- Fix: check with `lsattr filename`; check `getenforce` (SELinux) or `aa-status` (AppArmor), and review the relevant policy/audit log (`audit.log`, `dmesg`).

---

## Processes

**`kill` doesn't seem to terminate a process**
- Cause: the process may be in uninterruptible sleep (`D` state, usually waiting on disk I/O) and cannot respond to any signal, including `SIGKILL`, until that I/O completes.
- Fix: identify what it's blocked on with `cat /proc/<PID>/status` and `cat /proc/<PID>/wchan`; resolve the underlying I/O issue rather than repeatedly sending signals.

**"No such process" error when you can see it in `ps`**
- Cause: a race condition — the process exited between when you looked it up and when you tried to signal it (common with short-lived processes).
- Fix: re-check with `ps aux | grep <name>` immediately before acting, or use `pkill`/`killall` by name to avoid the PID race entirely.

**Zombie processes accumulating**
- Cause: a parent process isn't calling `wait()` to collect the exit status of its finished children.
- Fix: zombies themselves can't be killed (they're already dead); identify and fix/restart the parent process, or if the parent exits, `init`/`systemd` will reap the zombies automatically.

**Killed a process but it immediately reappears**
- Cause: it's likely managed by systemd (or another supervisor) with a `Restart=` policy — see the Services section below.
- Fix: stop and disable the managing service instead of killing the process directly.

---

## Services

**`systemctl start` succeeds but the service shows as failed shortly after**
- Cause: the service is likely crashing immediately after startup due to a configuration or dependency problem.
- Fix: `journalctl -u servicename -n 50 --no-pager` to see the actual error from the last run.

**Edited a unit file but changes don't apply**
- Cause: forgot `systemctl daemon-reload`.
- Fix: run `sudo systemctl daemon-reload`, then restart the service.

**Service works when started manually but not on boot**
- Cause: it was started but never enabled, or its `After=`/`Requires=` dependencies aren't correctly ordered relative to what it actually needs (e.g., network availability).
- Fix: confirm with `systemctl is-enabled servicename`; review and correct `[Unit]` ordering directives.

**"Failed to start" with no obvious reason in `systemctl status`**
- Cause: the default status output is often truncated.
- Fix: always follow up with `journalctl -u servicename -n 100 --no-pager` for the full picture, and check the `ExecStart=` path is correct and the binary is executable.

---

## Package Managers

**"Unable to locate package" despite it clearly existing**
- Cause: stale local package index.
- Fix: `sudo apt update` (Debian/Ubuntu) or `sudo dnf check-update` (RHEL/Fedora) before installing.

**Dependency conflicts blocking an install**
- Cause: two packages require incompatible versions of a shared dependency.
- Fix: review the specific conflict message carefully; consider `apt install -f` (Debian/Ubuntu) to attempt automatic dependency resolution, or investigate whether a third-party repo is providing a conflicting version.

**GPG/signature verification errors**
- Cause: an expired or missing repository signing key, or (less commonly but more seriously) a tampered package/mirror.
- Fix: verify the repository is legitimate before doing anything else; if legitimate, re-import the correct GPG key through the distribution's documented process. Never bypass verification with `--allow-unauthenticated`/`--nogpgcheck` as a shortcut.

**Installed a package but the binary isn't found**
- Cause: the binary's directory may not be in `PATH`, or the package installed a differently-named binary than expected.
- Fix: `dpkg -L packagename` or `rpm -ql packagename` to see exactly what files were installed and where.

---

## Environment Variables

**A variable set in one terminal isn't visible in another**
- Cause: environment variables are scoped per-shell-session unless persisted in a profile file loaded by both sessions.
- Fix: add the variable to `~/.bashrc` (or the relevant profile file) for persistence across sessions.

**A script can't see a variable you clearly set**
- Cause: the variable wasn't `export`ed, so it's shell-local only and not inherited by the script's child process.
- Fix: use `export VARNAME=value` instead of a plain assignment.

**Changed `PATH` but the shell still runs the old version of a command**
- Cause: the shell caches resolved command locations in a hash table for the current session.
- Fix: run `hash -r` to clear the command hash cache, or open a new shell session.

**Profile file edits don't seem to apply**
- Cause: edited the wrong file for the session type (login vs. interactive non-login shell use different files), or forgot to `source` it.
- Fix: `source ~/.bashrc` (or the appropriate file) to apply immediately; confirm which file your terminal emulator actually loads if unsure.

---

## Shell Redirection & Pipes

**Redirected output file is empty or missing expected content**
- Cause: `2>&1` was placed before the stdout redirection (wrong order), sending stderr to the terminal instead of the file, or stdout redirection wasn't specified at all.
- Fix: use the pattern `command > file 2>&1` (stdout redirect first, then merge stderr into it).

**A pipeline produces no output at all**
- Cause: one stage in the pipeline is silently failing or filtering everything out.
- Fix: test each stage individually by running progressively longer prefixes of the pipeline and checking output at each step.

**`uniq -c` counts look wrong**
- Cause: input wasn't sorted first, so non-adjacent duplicates weren't collapsed.
- Fix: always `sort` before piping into `uniq`.

**Accidentally overwrote an important file with `>`**
- Cause: `>` truncates the destination file immediately, before the command even finishes running.
- Fix: for future prevention, enable `set -o noclobber` in your shell to block accidental overwrites; recovery of the lost content depends on backups/snapshots, since `>` provides no undo.

---

## General Diagnostic Workflow

When something doesn't behave as expected anywhere in this module, work through these questions in order:

1. **Who am I, and what can I do?** — `whoami`, `id`, `groups`
2. **What actually happened?** — re-run the failing command and read the *exact* error text; don't guess.
3. **What does the log say?** — `journalctl`, `/var/log/`, or command-specific logs
4. **Is this a permissions problem?** — check ownership and mode with `ls -l` / `stat`, and directory traversal permissions with `namei -l`
5. **Is this a caching/propagation problem?** — new shell session, `daemon-reload`, `hash -r`, or simply waiting
6. **Can I reproduce it minimally?** — strip the command/script down to the smallest version that still fails
