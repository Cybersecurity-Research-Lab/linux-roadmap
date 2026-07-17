# 📖 Lesson 05 – Bash Automation

## Learning Outcomes
By the end of this lesson, you will be able to:
- Design a complete, robust automation script combining variables, loops, and functions
- Handle command-line arguments and configuration in a reusable way
- Implement logging, error handling, and safe failure modes in automation scripts
- Build practical administrative and security automation (backups, log monitoring, report generation)
- Recognize how automation scripts are targeted and abused in real attacks

## Estimated Study Time
55 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 02: Variables
- Lesson 03: Loops
- Lesson 04: Functions

## Key Terms
| Term | Definition |
|---|---|
| **Idempotency** | A property where running a script multiple times produces the same end result as running it once |
| **Logging** | Recording a script's actions and outcomes for later review |
| **Error handling** | Explicitly detecting and responding to failure conditions rather than ignoring them |
| **Lock file** | A file used to prevent multiple simultaneous instances of a script from running at once |
| **Dry run** | A mode where a script reports what it *would* do without actually doing it |

## Introduction
This lesson is where the pieces come together: variables for configuration and state, loops for processing multiple items, and functions for clean structure — combined into complete, production-quality automation scripts. The focus here shifts from individual syntax to *design*: how to build a script that fails safely, logs what it did, avoids running twice by accident, and can be trusted to run unattended.

## Why This Matters
Automation is where Bash scripting delivers its real value — backups, log rotation, monitoring, report generation, and incident-response triage scripts all depend on reliable, well-designed automation. But automation scripts often run with elevated privileges and without a human watching in real time, which means design mistakes (missing error handling, no logging, race conditions from concurrent runs) can cause silent failures or, in the worst cases, become a liability an attacker can exploit or abuse for persistence.

## Cybersecurity Insight
Automation scripts that run with elevated privileges are an attractive target: if an attacker can modify a script that root's cron job later executes (via a file permission weakness, or a writable directory earlier in a called-command's `PATH`, tying back to the Linux Fundamentals module's Environment Variables lesson), they gain that script's privilege level the next time it runs. This is why automation scripts intended to run privileged should be owned by root, writable only by root, called with absolute paths for every command, and stored outside any directory writable by lower-privileged users.

## Researcher's Note
When auditing automation scripts for security review, researchers specifically check: does the script use absolute paths for every external command it calls (rather than relying on `PATH`), does it validate or sanitize any input it processes, does it fail safely (exit rather than continue) on unexpected conditions, and are its file permissions and ownership appropriate for its privilege level. A script that "usually works" but has none of these properties is treated as a meaningful finding, not just a style issue, precisely because unattended scripts don't have a human available to catch problems in real time the way an interactive session would.

## Real-World Example
Multiple documented incidents involve attackers locating and modifying an existing, trusted automation script (a backup script, a cleanup job) rather than creating an obviously new one — because the script already runs on a schedule with established privileges and doesn't attract the same scrutiny a brand-new file might. This is precisely why file integrity monitoring (detecting unexpected changes to known scripts) and strict permission controls on automation scripts are standard recommendations in hardening guides, and why the Cybersecurity Insight above emphasizes ownership and absolute paths as concrete, checkable controls.

---

## Designing for Reliability — Logging and Error Handling

A well-designed automation script tells you what it did, even when nobody's watching in real time:
```bash
#!/bin/bash
set -euo pipefail

LOG_FILE="/var/log/myscript.log"

log() {
    local message="$1"
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $message" | tee -a "$LOG_FILE"
}

log "Script started"

if ! command -v rsync &>/dev/null; then
    log "ERROR: rsync is not installed, aborting"
    exit 1
fi

log "Prerequisite check passed"
# ... main logic here ...
log "Script completed successfully"
```
`tee -a` both prints to the terminal (useful for interactive runs) and appends to the log file (essential for unattended runs) — a small pattern that pays for itself the first time you need to debug something that ran overnight.

## Preventing Concurrent Runs — Lock Files

Automation scheduled to run frequently (especially via cron, covered in Lesson 06) risks overlapping executions if one run takes longer than expected — a lock file prevents this:
```bash
LOCK_FILE="/var/run/myscript.lock"

if [ -e "$LOCK_FILE" ]; then
    echo "Script already running (lock file exists), exiting."
    exit 1
fi

trap 'rm -f "$LOCK_FILE"' EXIT          # ensures cleanup even if the script errors or is interrupted
touch "$LOCK_FILE"

# ... main logic here ...
```
The `trap ... EXIT` line is important: it guarantees the lock file is removed when the script exits, whether that's normal completion, an error (with `set -e` active), or an interrupt signal — without it, a crashed run could leave a stale lock file behind and permanently block all future runs until someone notices and manually removes it.

## A Complete Example — Log Monitoring Automation

Bringing together variables, loops, functions, logging, and error handling into a realistic security-relevant script:
```bash
#!/bin/bash
set -euo pipefail

LOG_DIR="/var/log"
ALERT_THRESHOLD=10
REPORT_FILE="/tmp/failed_login_report_$(date +%Y%m%d).txt"

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1"
}

check_failed_logins() {
    local logfile="$1"
    local count
    count=$(grep -c "Failed password" "$logfile" 2>/dev/null || echo 0)
    echo "$count"
}

main() {
    log "Starting failed login scan"
    : > "$REPORT_FILE"          # truncate/create the report file

    for logfile in "$LOG_DIR"/auth.log*; do
        [ -f "$logfile" ] || continue          # skip if glob matched nothing

        local count
        count=$(check_failed_logins "$logfile")

        echo "$logfile: $count failed attempts" >> "$REPORT_FILE"

        if [ "$count" -ge "$ALERT_THRESHOLD" ]; then
            log "ALERT: $logfile has $count failed login attempts (threshold: $ALERT_THRESHOLD)"
        fi
    done

    log "Scan complete. Report written to $REPORT_FILE"
}

main "$@"
```
Note the `main "$@"` pattern at the bottom — defining a `main` function and calling it explicitly at the end (rather than letting script logic run loose at the top level) is a widely recommended structure, since it keeps all function definitions cleanly separated from execution and makes the script's entry point immediately obvious to anyone reading it.

---

## Tips
- Use `set -euo pipefail` as a default starting point for automation scripts, and consciously decide where you need to override that behavior for commands that are allowed to fail (e.g., `command || true`).
- Always use absolute paths for commands and files in scripts that will run unattended or with elevated privileges — don't rely on `PATH` or the current working directory being what you expect.
- Write a `--dry-run` mode into any automation that makes destructive changes (deleting files, modifying configuration) — it costs little upfront and saves enormous pain the first time the script has a bug.

## Common Mistakes
- Writing automation with no logging at all, making it impossible to diagnose what happened during an unattended run days or weeks later.
- Forgetting lock files for frequently-scheduled scripts, leading to overlapping runs that corrupt shared state or exhaust resources.
- Using relative paths or unqualified command names in scripts that might run with a different `PATH` or working directory than expected (e.g., under cron, covered in Lesson 06).
- Not handling the case where a glob matches nothing (`for file in *.log` when no `.log` files exist expands literally to the string `*.log`) — always check `[ -f "$file" ]` before processing.

## Common Misconceptions
- **"If a script worked when I tested it manually, it'll work fine automated."** Automated/unattended execution often has a different environment (`PATH`, working directory, environment variables) than an interactive session — always test in conditions as close as possible to how the script will actually run (see Lesson 06 for cron-specific environment differences).
- **"Error handling is optional for 'simple' scripts."** Simple scripts still fail in ways their author didn't anticipate — missing files, permission issues, network hiccups — and the cost of adding basic error handling upfront is far lower than the cost of debugging a mysterious unattended failure later.
- **"A lock file alone fully prevents concurrency problems."** Lock files prevent overlapping runs of the *same* script, but don't protect against race conditions with *other* processes modifying the same resources — broader coordination may be needed for genuinely concurrent-safe systems.

## Quick Practice
```bash
LOCK_FILE="/tmp/practice.lock"
if [ -e "$LOCK_FILE" ]; then
    echo "Already running"
    exit 1
fi
trap 'rm -f "$LOCK_FILE"' EXIT
touch "$LOCK_FILE"
echo "Running..."
sleep 2
echo "Done"
```
Run this script, and while it's sleeping, try running it a second time in another terminal — confirm the second instance correctly detects the lock and exits.

## Knowledge Check
1. Why is `tee -a` useful for logging in automation scripts, compared to just `echo`?
2. What problem does a lock file solve, and what does `trap ... EXIT` add to that pattern?
3. Why should automation scripts use absolute paths rather than relying on `PATH`?
4. What happens if a glob (`*.log`) matches no files, and why does that matter for loop logic?
5. Why might a script that works fine when run manually fail when run unattended (e.g., via cron)?

<details>
<summary>Answers</summary>

1. `tee -a` both displays output in real time (useful when run interactively) and appends it to a persistent log file (essential for reviewing what happened during an unattended run), whereas plain `echo` alone only does one or the other unless separately redirected.
2. A lock file prevents overlapping/concurrent runs of the same script; `trap ... EXIT` ensures the lock file is reliably removed when the script exits for any reason (success, error, or interruption), preventing a stale lock from permanently blocking future runs.
3. Because an automated/unattended execution environment may have a different `PATH` than expected, and relying on `PATH` resolution for privileged scripts creates a `PATH`-hijacking risk (as covered in the Environment Variables lesson).
4. If no files match, Bash expands the glob to the literal unmatched pattern string itself (e.g., `*.log`) rather than an empty list, which can cause a loop to process a single bogus "file" unless explicitly guarded against with a check like `[ -f "$file" ]`.
5. Automated execution contexts (like cron) often have a minimal, different `PATH`, no interactive shell profile loaded, and a different working directory than a manual terminal session, which can break scripts that implicitly depended on that interactive environment.
</details>

## Best Practices
- Always include logging in automation scripts, even simple ones — future you (or a teammate) will need it eventually.
- Use `set -euo pipefail` as a default, explicitly handling any commands that are allowed to fail.
- Use lock files with `trap ... EXIT` cleanup for any script that runs on a schedule and might overlap with itself.
- Use absolute paths for every command and file reference in unattended or privileged scripts.
- Build a `--dry-run` mode for any automation that makes destructive or hard-to-reverse changes.
- Guard glob-based loops against the "no matches" case explicitly.

## Summary
Bash automation is where variables, loops, and functions combine into complete, production-quality scripts — but the real skill is in *design*: logging so failures are diagnosable, error handling so problems are caught rather than silently ignored, lock files so scheduled runs don't overlap, and absolute paths so privileged scripts aren't vulnerable to environment manipulation. These same properties are exactly what security reviewers check for when auditing automation, and exactly what attackers exploit when those properties are missing.

## What's Next?
A script is only truly "automation" once it runs on its own schedule without a human triggering it each time. Lesson 06 covers cron — how to schedule the scripts built in this lesson reliably, and how cron itself is both essential infrastructure and a well-known persistence mechanism.

---


## Navigation
⬅️ [Previous: Lesson 04 – Functions](04-functions.md) | [ Lab 05 : Bash Automation ](../../labs/module-04/05-bash-automation.md) | ➡️ [Next: Lesson 06 – Cron Jobs](06-cron-jobs.md)
