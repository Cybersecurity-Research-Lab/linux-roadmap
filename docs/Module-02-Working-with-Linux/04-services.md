# 📖 Lesson 04 – Services

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain the role of an init system (`systemd`) in managing services
- Start, stop, restart, enable, and disable services with `systemctl`
- Inspect service status and logs with `systemctl` and `journalctl`
- Write a basic custom `systemd` unit file
- Recognize how services are abused for persistence by attackers

## Estimated Study Time
50 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 03: Processes (services are ultimately managed processes)
- Lesson 01: Users and Groups (services run as specific accounts)

## Key Terms
| Term | Definition |
|---|---|
| **Init system** | The first process (PID 1) started by the kernel, responsible for bringing up and supervising the rest of the system |
| **systemd** | The dominant modern Linux init system, used by most major distributions |
| **Unit file** | A configuration file describing a service, timer, socket, or other systemd-managed resource |
| **Daemon** | A background service process, typically with no attached terminal |
| **Enable vs. start** | "Enable" configures a service to launch at boot; "start" launches it immediately — they are independent actions |
| **Target** | systemd's equivalent of a traditional runlevel, grouping units for a particular boot state |

## Introduction
A "service" is just a process that's meant to run continuously (or on a schedule) in the background, supervised by the system rather than a person. Almost everything meaningful on a server — web servers, databases, SSH daemons, cron, logging — runs as a service. Modern Linux distributions manage services with **systemd**, which replaced older init systems (SysV init, Upstart) on the vast majority of current distributions.

## Why This Matters
Services are both critical infrastructure and a favorite persistence mechanism for attackers — a malicious systemd unit configured to `Restart=always` and enabled at boot will survive reboots and keep re-launching even if a defender kills the process. Understanding how services are defined, started, and supervised is essential both for legitimate administration and for recognizing when that mechanism has been hijacked.

## Cybersecurity Insight
`systemctl list-units --type=service --state=running` combined with `systemctl list-unit-files --state=enabled` gives a fast picture of what's running now and what's configured to persist across reboots. Attackers who achieve root access sometimes create a new systemd unit file (or modify an existing one) pointing to a malicious binary, then `enable` it — a technique documented in numerous incident response reports as a straightforward, hard-to-notice persistence method, since a new `.service` file in `/etc/systemd/system/` doesn't stand out the way a modified system binary might.

## Researcher's Note
Threat hunters commonly diff the contents of `/etc/systemd/system/` and `/lib/systemd/system/` (or `/usr/lib/systemd/system/`) against a known-good baseline, since unexpected unit files — especially ones with generic or system-sounding names — are a well-documented persistence indicator. Checking the `ExecStart=` line of every enabled unit for unfamiliar paths (especially anything in `/tmp`, `/var/tmp`, or a user's home directory) is a fast, high-value manual check.

## Real-World Example
Multiple public incident response write-ups describe attackers creating a systemd service unit (sometimes named to resemble a legitimate system component, like `network-healthcheck.service`) that repeatedly launched a reverse shell or cryptominer, with `Restart=always` ensuring it relaunched within seconds even when defenders killed the underlying process — until the responders identified and removed the unit file itself, not just the running process.

---

## Main Topic 1: systemd and systemctl Fundamentals

systemd manages **units** — services, sockets, timers, mounts, and more — described by unit files usually ending in `.service`. The `systemctl` command is your primary interface:

```bash
systemctl status nginx           # detailed current status
systemctl start nginx            # start now (doesn't persist across reboot)
systemctl stop nginx             # stop now
systemctl restart nginx          # stop then start
systemctl reload nginx           # reload config without a full restart (if supported)
systemctl enable nginx           # configure to start at boot (doesn't start it now)
systemctl disable nginx          # remove from boot startup
systemctl enable --now nginx     # enable AND start in one command
```

**Enable and start are independent** — a service can be running but not enabled (won't survive a reboot), or enabled but not currently running (will start on next boot but isn't active now). This distinction trips up nearly everyone at first.

```bash
systemctl list-units --type=service --state=running    # what's running now
systemctl list-unit-files --state=enabled               # what's set to persist at boot
systemctl is-active nginx                                # quick active/inactive check
systemctl is-enabled nginx                                # quick enabled/disabled check
```

## Main Topic 2: Logs and Diagnostics with journalctl

systemd centralizes logging through the **journal**, queried with `journalctl`:
```bash
journalctl -u nginx                # all logs for a specific unit
journalctl -u nginx -f              # follow live, like tail -f
journalctl -u nginx --since today   # time-filtered
journalctl -p err                   # only error-priority and above, across all units
journalctl -b                       # logs since the current boot
journalctl --disk-usage             # how much space the journal is consuming
```
When a service fails to start, `systemctl status <service>` will show the last few log lines directly, but `journalctl -u <service> -n 50 --no-pager` gives you a deeper, more readable slice for real troubleshooting.

## Main Topic 3: Writing and Managing Unit Files

A minimal `.service` unit file:
```ini
[Unit]
Description=My Custom Application
After=network.target

[Service]
Type=simple
User=appuser
ExecStart=/usr/local/bin/myapp --config /etc/myapp/config.yml
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Key directives:
- `[Unit]` — metadata and ordering (`After=`, `Requires=`, `Wants=`)
- `[Service]` — how to run it: `Type=`, `ExecStart=`, `ExecStop=`, `User=`, `Restart=`
- `[Install]` — how it hooks into boot targets when enabled

Custom unit files typically live in `/etc/systemd/system/`. After creating or editing one:
```bash
sudo systemctl daemon-reload      # tell systemd to re-read unit files
sudo systemctl enable --now myapp.service
```
Forgetting `daemon-reload` after editing a unit file is one of the most common sources of "I changed the config but nothing happened" confusion.

Always run services as a dedicated, unprivileged user (`User=appuser`) rather than root unless the service genuinely requires root — this limits the blast radius if the service is ever compromised.

---

## Tips
- `systemctl cat <service>` prints the actual resolved unit file content, including any drop-in overrides — faster than hunting through the filesystem.
- `systemctl edit <service>` creates a safe "drop-in" override file instead of editing the vendor-supplied unit directly, which survives package updates.
- Use `Type=simple` for the common case (the process itself is the main process); use `Type=forking` only for older-style daemons that fork into the background themselves.

## Common Mistakes
- Editing a unit file and forgetting `systemctl daemon-reload`.
- Confusing `enable` with `start` and being surprised a service didn't come up after a reboot (it was started, not enabled) or didn't come up right now (it was enabled, not started).
- Running a custom service as root "to avoid permission issues" instead of fixing the actual underlying permission problem.
- Not setting `Restart=` policy, so a crashed service just stays down silently instead of self-healing.

## Common Misconceptions
- **"Stopping a service is the same as disabling it."** `stop` only affects the current session; without `disable`, the service will start again on the next boot.
- **"All Linux services use systemd."** Most modern major distributions do, but some minimal/embedded distributions and containers use alternatives like OpenRC, runit, or no init system at all — always confirm with `ps -p 1 -o comm=` if you're unsure.
- **"A failed service shows up as an error you can't miss."** A failed unit can silently sit in a `failed` state without obvious symptoms unless someone checks `systemctl --failed` — a routine health check administrators sometimes skip.

## Quick Practice
```bash
systemctl list-units --type=service --state=running | head -10
systemctl --failed
```
Note how many services are currently running, and confirm whether any units are in a `failed` state on your lab system.

## Knowledge Check
1. What is the practical difference between `systemctl start` and `systemctl enable`?
2. Why is `systemctl daemon-reload` necessary after editing a unit file?
3. Why should a custom service typically run under a dedicated non-root user rather than root?
4. What does `Restart=always` in a unit file mean, and why is it relevant to both legitimate services and attacker persistence?
5. What command shows only failed systemd units?

<details>
<summary>Answers</summary>

1. `start` launches the service immediately but doesn't affect boot behavior; `enable` configures the service to launch automatically at boot but doesn't start it right now — they're independent.
2. systemd caches parsed unit file state in memory; `daemon-reload` tells it to re-read unit files from disk so changes take effect.
3. Running as a dedicated unprivileged user limits what an attacker can do if that specific service is ever compromised (principle of least privilege).
4. It tells systemd to automatically relaunch the process if it exits, for any reason; legitimately this provides resilience, but attackers exploit the same mechanism to make malicious processes reappear even after being killed.
5. `systemctl --failed`
</details>

## Best Practices
- Always run `systemctl daemon-reload` immediately after creating or editing a unit file.
- Use `systemctl edit` for overrides rather than modifying vendor unit files directly.
- Run services under the least-privileged user account that can do the job.
- Set an appropriate `Restart=` policy for resilience, but pair it with monitoring/alerting so repeated crash-restarts don't go unnoticed.
- Periodically audit `/etc/systemd/system/` and enabled units for anything unexpected, especially after a suspected compromise.

## Summary
Services are long-running, system-supervised processes, and on modern Linux they're almost universally managed by systemd. `systemctl` handles the full lifecycle — start, stop, enable, disable, status — while `journalctl` provides centralized logging for diagnostics. Because services are designed to persist and restart automatically, they're also a favorite target for attacker persistence, making unit file auditing a standard part of any hardening or incident response process.

## What's Next?
Services depend on software actually being installed on the system in the first place. Lesson 05 covers package managers — how software gets onto (and off of) a Linux system, and how to audit what's installed.

---

## Navigation
⬅️ [Previous: Lesson 03 – Processes](03-processes.md) | 🏠 [Module README](README.md) | ➡️ [Next: Lesson 05 – Package Managers](05-package-managers.md)
