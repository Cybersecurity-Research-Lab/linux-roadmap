# 📖 Lesson 06 – Cron Jobs

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain how cron schedules and executes recurring jobs on Linux
- Read and write correct cron schedule expressions
- Configure user and system crontabs safely
- Diagnose common cron failures, especially environment-related ones
- Recognize how cron is abused for attacker persistence and audit it accordingly

## Estimated Study Time
45 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 05: Bash Automation (cron is how you schedule the scripts built there)
- Lesson 01 from the Linux Fundamentals module (Users and Groups) helpful, since crontabs are per-user

## Key Terms
| Term | Definition |
|---|---|
| **Cron** | The Linux daemon that executes scheduled commands at specified times |
| **Crontab** | The configuration file listing a user's (or the system's) scheduled cron jobs |
| **Cron expression** | The five-field time specification (minute, hour, day, month, weekday) defining a schedule |
| **`cron.d`** | A directory for system-wide cron job configuration files, each with an explicit user field |
| **Anacron** | A cron variant designed for systems that aren't always powered on, ensuring missed jobs eventually run |
| **At** | A related utility for scheduling a one-time (not recurring) job |

## Introduction
A script is only true automation once it runs on its own schedule, without a human triggering it. Cron is the standard Linux mechanism for that — a long-running daemon that wakes up every minute, checks configured schedules, and executes anything that's due. This lesson covers how to configure cron correctly (including its notoriously easy-to-get-wrong environment differences from an interactive shell) and how to audit it, since cron is also one of the most well-documented and commonly used persistence mechanisms in real-world attacks.

## Why This Matters
Cron runs constantly, often with elevated privileges, and largely unattended — exactly the properties that make it valuable for legitimate automation and attractive for attacker persistence. A cron job that survives a reboot and silently re-executes on a schedule is a durable foothold; conversely, a well-audited crontab is one of the very first things any experienced incident responder checks on a potentially compromised host.

## Cybersecurity Insight
Attackers with any level of code execution on a Linux host frequently add a cron entry as a low-effort, durable persistence mechanism — a single line in a crontab that re-downloads and executes a payload every few minutes is quiet, survives reboots, and is easy to overlook amid legitimate scheduled jobs. Auditing `crontab -l` for every user (`for user in $(cut -f1 -d: /etc/passwd); do crontab -u "$user" -l 2>/dev/null; done`) plus reviewing `/etc/cron.d/`, `/etc/crontab`, and `/etc/cron.{hourly,daily,weekly,monthly}/` is a standard, high-value step in host compromise investigations.

## Researcher's Note
Incident responders reviewing cron for signs of compromise specifically look for entries referencing unusual paths (`/tmp`, `/dev/shm`, hidden directories), base64-encoded or otherwise obfuscated commands, unusually frequent schedules (every minute) for jobs with no obvious legitimate purpose, and any cron file with a modification timestamp that doesn't align with known, expected administrative changes. Comparing current crontab contents against a known-good baseline (from configuration management, version control, or a prior clean snapshot) is far more reliable than trying to judge "suspicious-looking" entries from intuition alone.

## Real-World Example
Numerous public incident write-ups describe attackers establishing persistence via a cron entry disguised to resemble legitimate system maintenance (e.g., a job named suggestively like a log rotation or update check) that actually re-downloaded and executed malware on a recurring schedule — surviving both process termination and, in several documented cases, surviving a first-pass "cleanup" that only removed the malicious binary without checking cron for the job that would simply re-fetch it minutes later. This is precisely why thorough incident response explicitly includes a cron/scheduled-task review as a standard step, not an optional one.

---

## Cron Expressions and Basic Syntax

A cron schedule has five time fields, followed by the command to run:
```
# minute (0-59) hour (0-23) day-of-month (1-31) month (1-12) day-of-week (0-6, 0=Sunday)
# |          |         |             |               |
# *          *         *             *               *    command-to-execute
```
Examples:
```
0 2 * * *        /usr/local/bin/backup.sh              # every day at 2:00 AM
*/15 * * * *     /usr/local/bin/check_disk.sh              # every 15 minutes
0 9 * * 1-5      /usr/local/bin/weekday_report.sh              # 9 AM, Monday through Friday
0 0 1 * *        /usr/local/bin/monthly_cleanup.sh                # midnight on the 1st of every month
```
`*` means "every value"; `*/15` means "every 15 units"; `1-5` means a range; comma-separated lists (`1,3,5`) are also valid. Getting these fields in the wrong order (a very common mistake) silently produces a schedule that's simply wrong rather than an error, so always double-check against the field-order comment above or a cron expression testing tool.

## Managing Crontabs

Each user has their own crontab:
```bash
crontab -l              # list current user's cron jobs
crontab -e                 # edit current user's crontab (opens configured $EDITOR)
crontab -r                    # remove ALL of current user's cron jobs (no confirmation — be careful)
crontab -u username -l           # view another user's crontab (requires appropriate privileges)
```
System-wide scheduling has a few additional mechanisms:
```
/etc/crontab                     # system crontab; note the extra "user" field before the command
/etc/cron.d/*                       # drop-in system cron files, same format as /etc/crontab
/etc/cron.hourly/                      # scripts here run once per hour (via run-parts, no schedule field needed)
/etc/cron.daily/                          # scripts here run once per day
/etc/cron.weekly/                            # once per week
/etc/cron.monthly/                              # once per month
```
The key difference between a user crontab and `/etc/crontab`/`cron.d`: the latter require an explicit user field (specifying which account the job runs as), since these files aren't implicitly tied to a single user's identity the way `crontab -e` is.

## Environment Differences and Debugging

Cron jobs run in a minimal environment, dramatically different from an interactive shell — this is the single most common source of "it works when I run it manually, but not from cron" confusion:
```bash
# A typical cron job's environment often has:
# - A minimal PATH (sometimes just /usr/bin:/bin)
# - No inherited shell profile (.bashrc, .bash_profile are NOT sourced)
# - A different (or no) working directory assumption
# - Possibly a different HOME
```
Defensive practices that directly address this:
```bash
# In the crontab itself, redirect output so failures are visible instead of silently discarded:
0 2 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1

# Inside the script, use absolute paths for everything, and don't rely on PATH:
/usr/bin/rsync -a /home/ /backup/home/

# Explicitly set any environment variables the script needs, either in the crontab itself:
PATH=/usr/local/bin:/usr/bin:/bin
MAILTO=admin@example.com
0 2 * * * /usr/local/bin/backup.sh
```
By default, cron emails any output a job produces to the owning user (unless redirected, or `MAILTO` is set differently) — which is why explicit output redirection to a log file (as shown above) is both a debugging aid and a way to avoid unexpected mail buildup on systems where local mail delivery isn't configured or monitored.

---

## Tips
- Always test a script manually first, but then also test it in as close to cron's minimal environment as possible before trusting it on a schedule — e.g., `env -i /bin/bash -c '/path/to/script.sh'` approximates cron's stripped-down environment for a quick sanity check.
- Use full absolute paths for the script itself and every command inside it — never rely on a relative path or `PATH` resolution in a cron context.
- Redirect cron job output explicitly (`>> logfile 2>&1`) so failures are visible and reviewable rather than silently emailed somewhere nobody checks.

## Common Mistakes
- Writing a script that works interactively but fails under cron due to `PATH` or working-directory assumptions that don't hold in cron's minimal environment.
- Getting cron field order wrong (e.g., swapping hour and minute) and not noticing because cron doesn't reject a syntactically valid but logically wrong schedule.
- Forgetting the extra "user" field required in `/etc/crontab` and `/etc/cron.d/` entries (not needed in a per-user `crontab -e` file).
- Running `crontab -r` by accident, wiping an entire user's scheduled jobs with no confirmation prompt and no built-in undo.

## Common Misconceptions
- **"If a cron job doesn't show an error, it worked correctly."** Cron typically only surfaces problems via email (if configured) or by the job's own logging — a script that fails silently (no output, no explicit error handling) can fail repeatedly for a long time completely unnoticed.
- **"Cron jobs run with the same environment as an interactive login shell."** This is one of the most common and consequential misconceptions in this entire lesson — cron's environment is deliberately minimal and does not source shell profile files, which breaks many scripts that implicitly depend on interactive shell setup.
- **"Checking `crontab -l` for the current user is sufficient to audit cron on a system."** A thorough audit must also check every other user's crontab, `/etc/crontab`, `/etc/cron.d/`, and the `cron.hourly`/`daily`/`weekly`/`monthly` directories — persistence can be placed in any of these locations, not just the current user's personal crontab.

## Quick Practice
```bash
crontab -l
cat /etc/crontab 2>/dev/null
ls /etc/cron.d/ 2>/dev/null
```
Review your lab system's current cron configuration across all these locations and note anything present.

## Knowledge Check
1. What are the five fields in a cron expression, in order?
2. Why does a script that works fine when run manually sometimes fail when run via cron?
3. What's the difference between a per-user crontab (`crontab -e`) and an `/etc/cron.d/` file in terms of required syntax?
4. Why is `crontab -r` considered dangerous to run casually?
5. Beyond the current user's crontab, what other locations should be checked during a thorough cron audit?

<details>
<summary>Answers</summary>

1. Minute, hour, day-of-month, month, day-of-week, followed by the command to execute.
2. Cron runs jobs in a minimal environment that doesn't source shell profile files and often has a much more limited `PATH` and different working directory than an interactive session, breaking scripts that implicitly depend on that interactive setup.
3. `/etc/cron.d/` (and `/etc/crontab`) entries require an explicit user field specifying which account the job runs as; a per-user crontab (`crontab -e`) doesn't need this since it's already tied to that user's identity.
4. It removes all of the current user's cron jobs immediately, with no confirmation prompt and no built-in undo, making accidental data loss (scheduled job configuration) easy.
5. Every other user's crontab, `/etc/crontab`, `/etc/cron.d/*`, and the `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/`, `/etc/cron.monthly/` directories.
</details>

## Best Practices
- Always use absolute paths for the script and every command it calls, since cron's `PATH` cannot be assumed to match an interactive shell's.
- Explicitly redirect cron job output to a log file (`>> logfile 2>&1`) for visibility and debugging.
- Test scripts under conditions approximating cron's minimal environment before relying on them being scheduled.
- Regularly audit cron across all users and all system-wide locations, not just your own crontab, especially after any suspected compromise.
- Treat unexpected or unexplained cron entries as a priority investigation item, particularly anything referencing `/tmp`, `/dev/shm`, or obfuscated commands.

## Summary
Cron is the standard mechanism for turning a script into true recurring automation, but its minimal, non-interactive execution environment is the single most common source of "works manually, fails on schedule" bugs — addressed by absolute paths, explicit environment variables, and output redirection. Because cron is quiet, persistent across reboots, and often runs with elevated privileges, it's also a well-documented attacker persistence mechanism, making a thorough, multi-location cron audit a standard part of any serious incident response process.

## What's Next?
This wraps up the core lesson content for the module. From here, use the module-wide **[Troubleshooting Guide](../../Troubleshooting/Module-04.md)** whenever a script or scheduled job doesn't behave as expected, and keep the **[Cheat Sheet](../../Cheatsheet/Module-04.md)** open as a quick reference while you continue building automation.

---


## Navigation
⬅️ [Previous: Lesson 05 – Bash Automation](05-bash-automation.md) | [ Lab 06 : Cron Jobs](../../labs/module-04/06-cron-jobs.md) | ➡️ [Troubleshooting Guide](../../Troubleshooting/Module-04.md)
