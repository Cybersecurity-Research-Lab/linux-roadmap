# 📑 Module Cheat Sheet

One-page quick reference for every command and syntax pattern covered in this module. Keep this open in a second window while you work.

---

## 🐚 Bash Fundamentals

```bash
#!/bin/bash              # shebang — MUST be the first line
#!/usr/bin/env bash          # more portable alternative

chmod +x script.sh          # make executable
./script.sh                    # run directly (needs execute bit + shebang)
bash script.sh                    # run explicitly, bypasses execute bit/shebang requirement

exit 0        # success
exit 1        # generic failure
echo $?          # check exit code of last command

# Debugging
bash -n script.sh        # syntax check only, doesn't execute
bash -x script.sh           # execute with trace output
set -x                          # enable tracing mid-script
set +x                             # disable tracing
set -e                                # exit immediately on any command failure
set -u                                   # error on use of unset variable
set -euo pipefail                           # recommended default for production scripts

shellcheck script.sh          # static analysis — catches quoting/logic issues
```

---

## 🧮 Variables

```bash
name="value"              # NO spaces around =
echo "$name"                  # ALWAYS quote expansions
echo "${name}"                    # braces for disambiguation, e.g. "${name}s"
readonly PI=3.14              # cannot be reassigned
unset name                       # remove a variable

count=$((5 + 3))               # arithmetic expansion
result=$(command)                 # command substitution (preferred syntax)
result=`command`                     # older backtick syntax

# Quoting
"$var"      # GOOD: preserved as single word, expansion allowed
$var         # RISKY: word-splitting + glob-expansion
'$var'          # literal: NO expansion at all

# Positional parameters / special variables
$0    # script name
$1 $2 # first, second argument
$@    # all args, each preserved separately (use quoted: "$@")
$*    # all args as one string (use quoted: "$*")
$#    # number of arguments
$?    # exit code of last command
$$    # PID of current script

# Arrays
arr=("a" "b" "c")
echo "${arr[0]}"          # first element
echo "${arr[@]}"             # all elements
echo "${#arr[@]}"                # count
arr+=("d")                          # append

# Safe defaults
echo "${var:-default}"          # use default if var unset/empty
```

---

## 🔁 Loops

```bash
# for — fixed list
for color in red green blue; do echo "$color"; done

# for — array
for item in "${arr[@]}"; do echo "$item"; done

# for — glob (SAFE for filenames)
for file in *.log; do echo "$file"; done

# for — C-style numeric
for ((i=1; i<=5; i++)); do echo "$i"; done

# while
count=1
while [ "$count" -le 5 ]; do
    echo "$count"
    count=$((count + 1))
done

# while — read file line by line (SAFE pattern)
while IFS= read -r line; do
    echo "$line"
done < "file.txt"

# until
until [ condition ]; do ...; done

# SAFE filename iteration (handles spaces/newlines in filenames)
find /path -name "*.log" -print0 | while IFS= read -r -d '' file; do
    echo "$file"
done

# Loop control
break        # exit loop entirely
continue        # skip to next iteration
break 2          # break out 2 levels of nested loops
```
⚠️ Avoid `for file in $(ls)` — use a glob or `find -print0` instead.

---

## 🧩 Functions

```bash
# Definition (two equivalent syntaxes)
myfunc() {
    local var="$1"          # ALWAYS use local inside functions
    echo "$var"
}
function myfunc2 {
    echo "$1"
}

myfunc "argument"          # call it

# Return patterns
# Pattern 1: exit status (0=success, 1-255=failure)
check_thing() {
    [ condition ] && return 0 || return 1
}
if check_thing; then echo "ok"; fi

# Pattern 2: data via echo + command substitution
get_value() {
    echo "computed value"
}
result=$(get_value)

# main function pattern
main() {
    # ... script logic ...
}
main "$@"
```

---

## ⚙️ Bash Automation

```bash
# Recommended script header
#!/bin/bash
set -euo pipefail

# Logging pattern
LOG_FILE="/var/log/myscript.log"
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Lock file pattern (prevents overlapping runs)
LOCK_FILE="/var/run/myscript.lock"
if [ -e "$LOCK_FILE" ]; then
    echo "Already running"; exit 1
fi
trap 'rm -f "$LOCK_FILE"' EXIT
touch "$LOCK_FILE"

# Prerequisite check
command -v rsync &>/dev/null || { echo "rsync not found"; exit 1; }

# Guard against unmatched glob
for file in *.conf; do
    [ -f "$file" ] || continue
    # process "$file"
done

# Dry-run flag pattern
DRY_RUN=false
[ "${1:-}" = "--dry-run" ] && DRY_RUN=true
if [ "$DRY_RUN" = true ]; then
    log "DRY RUN: would perform action"
else
    # perform actual action
fi
```

---

## ⏰ Cron Jobs

```bash
crontab -l                   # list current user's cron jobs
crontab -e                      # edit current user's crontab
crontab -r                         # remove ALL jobs — NO confirmation, use with care
crontab -u username -l                # view another user's crontab

# Cron expression fields (in order)
# minute(0-59) hour(0-23) day-of-month(1-31) month(1-12) day-of-week(0-6, 0=Sun)

0 2 * * *        command       # daily at 2:00 AM
*/15 * * * *     command          # every 15 minutes
0 9 * * 1-5      command             # 9 AM, weekdays only
0 0 1 * *        command                # midnight on the 1st of every month

# System-wide locations (require an extra "user" field before the command)
/etc/crontab
/etc/cron.d/*
/etc/cron.hourly/     # run-parts, no schedule field
/etc/cron.daily/
/etc/cron.weekly/
/etc/cron.monthly/

# Defensive cron entry pattern
PATH=/usr/local/bin:/usr/bin:/bin
0 2 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1

# Approximate cron's minimal environment for testing
env -i /bin/bash -c '/path/to/script.sh'
```

---

## 🚨 Quick Security Audit Checklist

```bash
shellcheck script.sh                                          # static analysis on any script of interest
bash -n script.sh                                                 # safe syntax-only check on unknown scripts
grep -n "eval\|curl.*|.*bash\|/dev/tcp" script.sh                   # spot risky patterns
grep -rn "\$[A-Za-z_]" script.sh | grep -v '"'                          # rough unquoted-expansion check (manual review still needed)

# Cron persistence audit
crontab -l
cat /etc/crontab 2>/dev/null
ls -la /etc/cron.d/
ls -la /etc/cron.hourly/ /etc/cron.daily/ /etc/cron.weekly/ /etc/cron.monthly/
for user in $(cut -f1 -d: /etc/passwd); do
    echo "=== $user ==="; crontab -u "$user" -l 2>/dev/null
done
```

---

## ⚠️ Universal Safety Reminders

- **Always quote variable expansions**: `"$var"`, not `$var`.
- **Never `eval` untrusted or externally-influenced input.**
- **Always use `local` inside functions** unless you deliberately want global scope.
- **Always use absolute paths** in automation and cron — never rely on `PATH` or working directory.
- **Always test `bash -n`** before running an unfamiliar script.
- **Never run `crontab -r`** without being certain — it has no undo.
