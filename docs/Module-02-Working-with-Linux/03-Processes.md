# 📖 Lesson 03 – Processes

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain what a process is and how Linux tracks process state
- Inspect running processes using `ps`, `top`, and `htop`
- Control process execution: backgrounding, foregrounding, killing, and signaling
- Interpret process trees and parent/child relationships
- Recognize suspicious process behavior relevant to incident response

## Estimated Study Time
50 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 01: Users and Groups (processes run *as* a user)
- Basic comfort with the terminal

## Key Terms
| Term | Definition |
|---|---|
| **Process** | An instance of a running program, tracked by the kernel |
| **PID** | Process ID — a unique numeric identifier for a running process |
| **PPID** | Parent Process ID — the PID of the process that spawned this one |
| **Signal** | A message sent to a process to request an action (e.g., terminate, reload) |
| **Zombie process** | A terminated process whose exit status hasn't been collected by its parent |
| **Daemon** | A background process, typically detached from a terminal, providing a service |
| **Foreground/background** | Whether a process is attached to the interactive shell or running detached |

## Introduction
Every running program on a Linux system — from your shell to a web server to a cryptominer planted by an attacker — is a process. The kernel tracks each one with a unique PID, an owner, a parent, resource usage, and a state. Learning to inspect and control processes is essential not just for day-to-day administration but for detecting when something on a system is behaving abnormally.

## Why This Matters
Malware, backdoors, and unauthorized cryptomining all manifest as running processes. Being able to quickly answer "what's running, who owns it, what's it connected to, and is that normal?" is a foundational incident-response skill. Process management is also central to everyday administration: restarting a hung service, freeing resources from a runaway program, or understanding what a script actually launched.

## Cybersecurity Insight
Attackers frequently try to disguise malicious processes by naming them to resemble legitimate system processes (e.g., `[kworker/0:1]` mimicking a real kernel worker thread, or a binary literally named `systemd` running from `/tmp`). A key defensive technique is checking a suspicious process's actual executable path with `ls -l /proc/<PID>/exe` — legitimate kernel threads have no backing executable file at all, and any process claiming to be a system process but running from `/tmp`, `/dev/shm`, or a user's home directory deserves immediate scrutiny.

## Researcher's Note
Incident responders often use `ps auxf` (the `f` flag shows a visual process tree) as a first-pass triage tool, looking specifically for children of unexpected parents — for example, a web server process (`apache2`, `nginx`) spawning a shell (`/bin/bash` or `/bin/sh`) is a classic sign of a successful remote code execution exploit, since a web server has no legitimate reason to spawn an interactive shell on its own.

## Real-World Example
In numerous documented web application compromises, investigators traced the initial breach by noticing that the web server's worker process had an unexpected child process — a reverse shell connecting back to an attacker-controlled IP. The process tree view (`ps auxf` or `pstree`) made the anomaly immediately visible: `nginx → sh → nc` is not a pattern that appears in normal, non-compromised web server operation.

---

## Main Topic 1: Inspecting Processes

**`ps`** — snapshot of current processes:
```bash
ps aux                 # all processes, all users, detailed
ps -ef                 # alternate format, shows PPID clearly
ps aux --sort=-%cpu    # sorted by CPU usage, descending
ps auxf                # visual tree showing parent/child relationships
```
Key columns in `ps aux`: `USER`, `PID`, `%CPU`, `%MEM`, `STAT` (process state), `START`, `TIME`, `COMMAND`.

**`top`** / **`htop`** — live, continuously updating views:
```bash
top       # built-in on virtually every distro
htop      # friendlier, color-coded, usually needs installing
```
Inside `top`, press `M` to sort by memory, `P` to sort by CPU, `k` to kill a process by PID.

**`pstree`** — a compact visual hierarchy of parent/child processes, useful for spotting the "web server spawned a shell" pattern described above.

**`/proc/<PID>/`** — the kernel exposes live process detail as a virtual filesystem. `/proc/<PID>/cmdline`, `/proc/<PID>/exe`, `/proc/<PID>/cwd`, and `/proc/<PID>/environ` are goldmines for investigating a specific process in depth.

## Main Topic 2: Controlling Processes — Signals and Job Control

Processes are controlled by sending **signals**. The two you'll use constantly:
```bash
kill -15 1234     # SIGTERM: ask the process to terminate gracefully (default)
kill -9 1234      # SIGKILL: force-terminate immediately, no cleanup
kill -1 1234      # SIGHUP: often used to tell a daemon to reload its config
killall nginx     # send a signal to all processes matching a name
pkill -f "python3 script.py"   # kill by matching the full command line
```
⚠️ Always try `SIGTERM` (the default, no flag needed) before `SIGKILL` — it gives the process a chance to clean up open files, save state, and exit gracefully. Reach for `SIGKILL` only when a process is unresponsive.

**Job control** lets you manage foreground/background execution within a single shell session:
```bash
long_command &      # start in the background
jobs                # list background jobs in this shell
fg %1               # bring job 1 to the foreground
bg %1               # resume a stopped job in the background
Ctrl+Z               # suspend the current foreground job
Ctrl+C               # send SIGINT to the current foreground job
```

## Main Topic 3: Process States and Relationships

Every process has a state, shown in `ps`'s `STAT` column:
- `R` — running or runnable
- `S` — sleeping (waiting on an event, e.g., I/O)
- `D` — uninterruptible sleep (usually waiting on disk I/O — can't even be killed with SIGKILL until it returns)
- `Z` — zombie (terminated, but parent hasn't collected its exit status yet)
- `T` — stopped (e.g., via Ctrl+Z or `SIGSTOP`)

Every process (except the very first) has a **parent**. When a parent dies before its child, the child is "re-parented" — on modern Linux, usually to `systemd` (PID 1) or an init-like process, which then becomes responsible for eventually reaping it. A **zombie process** is a process that has finished executing, but whose exit status the parent hasn't yet collected via `wait()` — it consumes a process table entry but no real resources, and a large number of zombies usually points to a buggy parent process, not a security issue by itself. A large number of `D`-state processes, by contrast, often points to disk or storage problems.

---

## Tips
- `ps aux | grep processname` is the fastest way to check whether something specific is running — but remember the `grep` command itself will show up in the results (a classic gotcha).
- Use `nice` and `renice` to adjust a process's CPU scheduling priority without killing it — useful for long-running background jobs you don't want hogging resources.
- `lsof -p <PID>` lists every file (including network sockets) a process has open — invaluable for understanding what a suspicious process is actually doing.

## Common Mistakes
- Jumping straight to `kill -9` out of habit, which can corrupt data for processes mid-write and skips any graceful cleanup.
- Forgetting the trailing `grep` process shows up in `ps aux | grep foo` results (use `pgrep foo` or `ps aux | grep [f]oo` to avoid the false positive).
- Assuming a killed parent process automatically kills its children — it often doesn't; orphaned children can keep running silently.
- Confusing PID with PPID when reading `ps -ef` output, leading to killing the wrong process.

## Common Misconceptions
- **"A zombie process is dangerous and should be killed immediately."** Zombies can't be killed — they're already dead; the fix is addressing the *parent* process that isn't reaping them, or letting `init`/`systemd` clean it up on parent exit.
- **"High CPU usage always means malware."** Legitimate processes (compilers, video encoders, backups) can also spike CPU; context (what the process is, who owns it, whether it's expected) matters more than the raw number.
- **"`kill` always terminates a process."** `kill` sends a *signal*; the process (or the kernel, for uncatchable signals) decides how to respond. A well-written program can intercept and handle most signals other than `SIGKILL` and `SIGSTOP`.

## Quick Practice
```bash
sleep 300 &
jobs
ps aux | grep sleep
kill %1
jobs
```
Confirm the background job disappears from both `jobs` and `ps aux` after the `kill`.

## Knowledge Check
1. What's the difference between `SIGTERM` and `SIGKILL`?
2. What does the `D` process state typically indicate, and why can't you `kill -9` it immediately?
3. Why would a web server process spawning a shell process be considered suspicious?
4. What's the difference between a PID and a PPID?
5. What command shows a visual tree of process parent/child relationships?

<details>
<summary>Answers</summary>

1. `SIGTERM` (15) politely asks a process to terminate, allowing graceful cleanup; `SIGKILL` (9) forces immediate termination with no chance for cleanup.
2. `D` means uninterruptible sleep, usually waiting on disk/kernel I/O; the process can't respond to any signal, including `SIGKILL`, until that I/O operation completes.
3. A web server has no legitimate operational reason to spawn an interactive shell; this pattern is a strong indicator of a successful remote code execution exploit.
4. PID uniquely identifies the process itself; PPID identifies its parent process (the process that spawned it).
5. `pstree`, or `ps auxf` for a tree-style view within `ps`.
</details>

## Best Practices
- Prefer `SIGTERM` before escalating to `SIGKILL`.
- Use `ps auxf` or `pstree` regularly enough that you develop a sense of what "normal" looks like on systems you manage — anomaly detection depends on a baseline.
- Investigate any process whose executable path (`/proc/<PID>/exe`) points somewhere unusual, like `/tmp` or `/dev/shm`.
- Use `nice`/`renice` for resource management instead of killing and restarting long jobs.
- Automate baseline process auditing (e.g., a known-good process list) on critical systems rather than relying purely on manual review.

## Summary
Processes are the living, running state of a Linux system, and being fluent with `ps`, `top`, `kill`, and process trees is essential for both routine administration and security investigation. Understanding process states, signals, and parent/child relationships lets you distinguish "normal, if unfamiliar" from "actively suspicious."

## What's Next?
Many of the most important long-running processes on a system are actually managed by a higher-level layer: services. Lesson 04 covers how Linux starts, stops, and supervises services — and how that overlaps with process management.

---


## Navigation
⬅️ [Previous: Lesson 02 – File Permissions](02-file-permissions.md) | 🏠 [Module README](README.md) | ➡️ [Next: Lesson 04 – Services](04-services.md)
