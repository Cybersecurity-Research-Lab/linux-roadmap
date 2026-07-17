# 🛠️ Module Troubleshooting Guide

A single reference for diagnosing common problems across every topic in this module. Organized by lesson so you can jump straight to the relevant section.

---

## Bash Fundamentals

**"Permission denied" when running `./script.sh`**
- Cause: the execute bit isn't set on the script.
- Fix: `chmod +x script.sh`, or run it explicitly with `bash script.sh` instead.

**"bad interpreter" or the script runs with unexpected behavior**
- Cause: the shebang line is missing, malformed, has something before it, or points to the wrong interpreter (e.g., `#!/bin/sh` when the script uses Bash-only syntax).
- Fix: confirm `#!/bin/bash` (or `#!/usr/bin/env bash`) is the very first line with nothing before it; confirm no Bash-specific syntax is being run under `sh`.

**Script runs but produces no output and no error**
- Cause: could be an early, silent failure with no error handling, or output is being redirected somewhere unexpected.
- Fix: add `set -euo pipefail` near the top to surface failures immediately, and check for any redirection (`>`, `2>`) that might be hiding output.

---

## Variables

**A command using a variable fails on files/values with spaces**
- Cause: the variable was expanded unquoted (`$var` instead of `"$var"`), causing word-splitting.
- Fix: quote the expansion: `"$var"`.

**"command not found" immediately after a variable assignment line**
- Cause: spaces around the `=` sign in the assignment (`name = "value"`), which Bash parses as a command invocation, not an assignment.
- Fix: remove all spaces around `=`: `name="value"`.

**A variable appears empty when you're sure it was set**
- Cause: it may have been set inside a subshell (e.g., inside a pipeline or a `$(...)` command substitution) and didn't persist to the parent shell; or it was never `export`ed and you're checking from a different process.
- Fix: avoid setting important variables inside a pipeline stage; use process substitution or redirection instead of piping into a `while` loop if the variable needs to persist (see the Loops section below).

**`$1`, `$2` etc. seem to reference the wrong values inside a function**
- Cause: function positional parameters are independent of the script's own positional parameters — this is expected behavior, not a bug.
- Fix: pass the needed values explicitly as arguments to the function call.

---

## Loops

**A `for` loop mishandles filenames with spaces**
- Cause: looping over unquoted command substitution output (e.g., `for f in $(ls)`) instead of a glob or safe `find` pattern.
- Fix: use `for f in *` (glob) or `find ... -print0 | while IFS= read -r -d '' f; do ... done`.

**Variables set inside a `while read` loop don't persist after the loop ends**
- Cause: the loop was fed via a pipe (`cat file | while read line`), which runs the loop in a subshell.
- Fix: use input redirection instead: `while read -r line; do ... done < file`.

**A loop over `*.log` (or similar glob) seems to process a literal, nonexistent file**
- Cause: the glob matched nothing, and Bash's default behavior expands it to the literal, unmatched pattern string.
- Fix: add a guard like `[ -f "$file" ] || continue` inside the loop, or enable `shopt -s nullglob` at the top of the script.

**`break`/`continue` inside a nested loop doesn't affect the outer loop as expected**
- Cause: by default they only affect the innermost loop.
- Fix: use `break 2` or `continue 2` (adjusting the number for the desired nesting level).

---

## Functions

**A function call seems to overwrite a variable used elsewhere in the script**
- Cause: the function assigned to a variable without declaring it `local`, making it global by default.
- Fix: add `local` before every variable declaration inside the function that isn't intended to affect global state.

**Capturing a function's output includes extra, unwanted text**
- Cause: the function printed additional informational/debug messages via `echo` alongside its intended "return value," and all of it was captured by the command substitution.
- Fix: separate informational logging (send to stderr with `>&2`, or use a dedicated log function) from the actual data the function is meant to return via stdout.

**`return "some text"` doesn't work as expected**
- Cause: `return` only accepts a numeric exit status (0–255); it cannot return arbitrary string data.
- Fix: use `echo` and capture the result with command substitution instead, reserving `return` for success/failure signaling.

**A function's exit status seems wrong even though no explicit `return` was given**
- Cause: without an explicit `return`, a function's exit status is that of the last command it executed, which may not reflect the function's overall intended success/failure.
- Fix: add an explicit `return 0`/`return 1` at the appropriate point(s) in the function.

---

## Bash Automation

**A script works when run manually but fails or behaves differently when automated**
- Cause: automated execution (including cron, covered below) often has a different environment — `PATH`, working directory, environment variables — than an interactive session.
- Fix: use absolute paths throughout, explicitly set any required environment variables, and avoid relying on the current working directory unless the script explicitly sets it.

**A scheduled/repeated script run corrupts data or behaves unpredictably when run close together**
- Cause: overlapping concurrent executions with no lock file protection.
- Fix: add a lock file check with `trap ... EXIT` cleanup, as covered in Lesson 05.

**A stale lock file permanently blocks a script from running**
- Cause: a previous run crashed or was killed in a way that didn't trigger the `trap` cleanup (e.g., `SIGKILL`, which cannot be trapped).
- Fix: manually remove the stale lock file to unblock; consider adding a lock file age check (e.g., ignore/remove locks older than an expected maximum runtime) for extra resilience.

**A destructive automation script did something unintended during testing**
- Cause: no `--dry-run` mode was available/used before running for real.
- Fix: for future prevention, always build and test a dry-run mode for any destructive automation before relying on it in production; recovery depends on backups/snapshots for the current incident.

---

## Cron Jobs

**A cron job never seems to run at all**
- Cause: incorrect field order/syntax in the schedule expression, the crontab wasn't saved correctly, or the cron daemon itself isn't running.
- Fix: double-check field order against `minute hour day month weekday`; confirm with `crontab -l` that the entry is actually present; confirm the cron service is active (`systemctl status cron` or `crond`, depending on distribution).

**A cron job runs but fails, while the same script works fine manually**
- Cause: almost always cron's minimal environment — missing `PATH` entries, no sourced shell profile, unexpected working directory.
- Fix: use absolute paths for the script and every command inside it; explicitly set any needed environment variables in the crontab or at the top of the script; redirect output (`>> logfile 2>&1`) to capture the actual error for diagnosis.

**No error is visible even though the job appears to be failing**
- Cause: cron output wasn't redirected anywhere reviewable (it may be silently emailed, or mail delivery isn't configured/monitored on the host).
- Fix: add explicit output redirection (`>> logfile 2>&1`) to every cron entry so failures are captured and reviewable.

**An `/etc/cron.d/` entry doesn't work while a personal crontab entry with similar syntax does**
- Cause: `/etc/cron.d/` (and `/etc/crontab`) requires an additional user field before the command, which personal crontabs (`crontab -e`) don't use.
- Fix: add the correct user field: `minute hour day month weekday username command`.

**Accidentally ran `crontab -r` and lost all scheduled jobs**
- Cause: `crontab -r` removes the entire crontab immediately with no confirmation.
- Fix: no built-in undo; recovery depends on a backup of the crontab contents (a strong argument for keeping crontab definitions under version control or in configuration management rather than only live in `crontab -e`).

---

## General Diagnostic Workflow

When something doesn't behave as expected anywhere in this module, work through these questions in order:

1. **Did I quote my variable expansions?** — the single most common source of subtle Bash bugs
2. **What does `bash -n` or `shellcheck` say?** — catch syntax and common-pattern issues before runtime
3. **What's the actual exit code?** — check `$?` (or a function's `return`) immediately after the suspect command
4. **Is this an environment difference?** — especially relevant for anything run via cron or other automation; compare `PATH`, working directory, and environment variables between the working and failing contexts
5. **Am I looking at global vs. local scope correctly?** — confirm `local` is used where intended, especially inside functions
6. **Is there a log I can check?** — if the script has logging (it should), start there before re-running and guessing
