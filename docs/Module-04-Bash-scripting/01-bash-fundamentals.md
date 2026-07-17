# 📖 Lesson 01 – Bash Fundamentals

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain what a shell is and how Bash fits into the Linux ecosystem
- Write, save, and execute a basic Bash script
- Use the shebang line, comments, and exit codes correctly
- Make a script executable and run it in multiple ways
- Debug a simple script using built-in Bash tooling

## Estimated Study Time
45 minutes (including lab)

## Difficulty
🟢 Beginner

## Prerequisites
- Comfortable running commands interactively in a Linux terminal
- No prior scripting experience required

## Key Terms
| Term | Definition |
|---|---|
| **Shell** | A program that interprets commands and provides a command-line interface to the OS |
| **Bash** | Bourne Again SHell — the most widely used Linux shell and scripting language |
| **Shebang** | The `#!` line at the top of a script specifying which interpreter should run it |
| **Exit code** | A numeric value (0–255) a script returns to indicate success (0) or a specific failure |
| **Executable bit** | The file permission that allows a script to be run directly |
| **stdin/stdout/stderr** | The standard input, output, and error streams a script reads from and writes to |

## Introduction
Bash is both an interactive shell and a full scripting language — the same syntax you use to run one command at a time can be saved into a file and executed as a repeatable program. This lesson covers the absolute foundation: what a script actually is, how Linux knows to run it correctly, and how to structure and execute even the simplest script correctly and safely.

## Why This Matters
Bash scripting is everywhere in Linux administration and security tooling — deployment scripts, log analysis pipelines, CI/CD glue code, and a huge fraction of both legitimate automation and attacker tooling are plain Bash. Being able to read an unfamiliar script quickly and confidently — understanding its shebang, structure, and exit behavior — is a prerequisite for nearly every other skill in this module and a genuinely common task in incident response (reading and understanding what a suspicious script actually does).

## Cybersecurity Insight
Malicious scripts dropped on a compromised host are very often plain Bash — a downloader, a persistence installer, a cleanup script. Being able to quickly read a script's shebang, structure, and behavior (without necessarily running it) is a core skill in incident response triage. A common first step is a static read-through (`cat script.sh` or `less script.sh`) followed by looking specifically for network calls (`curl`, `wget`, `nc`), suspicious redirection (`/dev/tcp/`), and encoded/obfuscated content (`base64 -d`, `eval`) before ever considering execution in an isolated sandbox.

## Researcher's Note
Malware analysts examining an unknown shell script typically avoid running it directly, instead using `shellcheck` (a static analysis tool, also useful for entirely legitimate script quality review) and manual read-throughs to understand behavior first. `bash -n script.sh` is a particularly useful trick — it performs a syntax check only, parsing the script without executing a single line, which is a safe first step even for scripts of unknown origin.

## Real-World Example
Numerous documented Linux malware families (including some cryptomining and botnet droppers) begin their infection chain with a short, unassuming Bash script — often distributed via a compromised web server or a `curl | bash` pattern (covered in the Linux Fundamentals module's Shell Redirection & Pipes lesson) — that downloads and installs the actual payload. Recognizing normal script structure well enough to spot when something deviates from it (unexplained obfuscation, unnecessary network calls, oddly broad permission changes) is a practical, everyday defensive skill built directly on the fundamentals in this lesson.

---

## Anatomy of a Bash Script

A minimal script:
```bash
#!/bin/bash
# This script prints a greeting

echo "Hello, world!"
exit 0
```

- **Shebang (`#!/bin/bash`)** — must be the very first line, telling the kernel which interpreter to use when the script is executed directly. Without it, the script falls back to being interpreted by whatever shell invokes it, which can cause subtle, hard-to-diagnose behavior differences.
- **Comments (`#`)** — anything after a `#` (other than the shebang itself) is ignored by Bash; use them generously to explain *why*, not just *what*.
- **Commands** — the actual instructions, identical in syntax to what you'd type interactively.
- **`exit` code** — `exit 0` signals success; any nonzero value (conventionally 1–255) signals a specific kind of failure. If omitted, a script exits with the status of its last executed command.

## Making a Script Executable and Running It

```bash
chmod +x myscript.sh        # add execute permission (see Linux Fundamentals, Lesson 02)
./myscript.sh                  # run directly (requires execute permission + correct shebang)
bash myscript.sh                  # run explicitly with bash, regardless of execute permission or shebang
sh myscript.sh                       # run with sh — NOT always the same as bash, see Common Mistakes
```
`./myscript.sh` relies on the shebang line to determine the interpreter and requires the execute bit to be set. `bash myscript.sh` bypasses both of those requirements by explicitly invoking Bash yourself — useful when testing a script you haven't made executable yet, or when you need to guarantee it runs under Bash specifically rather than whatever `sh` happens to be symlinked to on that system.

## Debugging Basics

```bash
bash -n script.sh          # syntax check ONLY — does not execute anything, safe for unknown scripts
bash -x script.sh              # execute with a trace of every command as it runs (very useful for debugging)
set -x                             # enable tracing from within a running script, for a specific section
set +x                                # disable tracing again
set -e                                   # exit immediately if any command fails (nonzero exit code)
set -u                                      # treat use of an unset variable as an error
```
Adding `set -euo pipefail` near the top of a script (the `pipefail` option additionally makes a pipeline fail if *any* stage fails, not just the last one) is one of the most commonly recommended defensive habits for production Bash scripts — it converts silent failures into loud, immediate ones, which is far easier to debug and far safer for scripts that matter.

---

## Tips
- Always start real scripts with `#!/bin/bash` (or `#!/usr/bin/env bash` for better portability across systems where Bash isn't at a fixed path) as the very first line, with nothing before it.
- Use `bash -n script.sh` as a zero-risk first step when looking at any script of unknown origin or complexity.
- Keep a habit of checking `echo $?` immediately after a command when troubleshooting — it shows the exit code of the last executed command.

## Common Mistakes
- Assuming `sh` and `bash` are always identical — on many distributions `/bin/sh` is actually a symlink to `dash` or another lighter shell with different (usually stricter, POSIX-only) syntax rules, and Bash-specific features (like arrays or `[[ ]]`) will fail under `sh`.
- Forgetting to `chmod +x` a script and being confused by a "permission denied" error when trying `./script.sh`.
- Putting anything (even a blank line or comment) before the shebang line, which breaks its recognition entirely.
- Not checking exit codes, silently continuing after a failed command that should have stopped the script.

## Common Misconceptions
- **"A script with no errors printed ran successfully."** A command can fail (return a nonzero exit code) without printing any visible error message at all — always check exit codes explicitly in scripts that matter, rather than assuming silence means success.
- **"`#!/bin/bash` and `#!/usr/bin/env bash` are functionally identical in every case."** They're usually equivalent, but `env bash` searches `PATH` for whichever `bash` comes first, which is more portable across systems where Bash isn't installed at the standard `/bin/bash` location (common on some BSD/macOS setups) but is technically dependent on `PATH` being trustworthy, which matters in hardened/restricted environments.
- **"Running `bash -x` is the same as reading the script's source."** Tracing shows what actually executes at runtime, including the *results* of variable expansion and command substitution — this can reveal important behavior that isn't obvious from the static source, especially with dynamically constructed commands.

## Quick Practice
```bash
cat << 'EOF' > practice.sh
#!/bin/bash
echo "Current user: $(whoami)"
echo "Current directory: $(pwd)"
exit 0
EOF
chmod +x practice.sh
./practice.sh
echo "Exit code was: $?"
```
Confirm the script runs and prints an exit code of `0`.

## Knowledge Check
1. What does the shebang line do, and why must it be the very first line of a script?
2. What's the difference between running `./script.sh` and `bash script.sh`?
3. What does `bash -n script.sh` do, and why is it a safe first step for an unfamiliar script?
4. What does `set -euo pipefail` accomplish, and why is it recommended for production scripts?
5. Why can't you assume `sh` and `bash` will run a script identically?

<details>
<summary>Answers</summary>

1. It tells the kernel which interpreter to use when the script is executed directly; it must be the first line because the kernel only checks the very beginning of the file for this directive.
2. `./script.sh` requires the execute permission bit and relies on the shebang to pick the interpreter; `bash script.sh` explicitly invokes Bash regardless of execute permission or shebang.
3. It performs a syntax check only, without executing any command in the script — a safe way to inspect an unfamiliar or untrusted script's basic validity without running potentially malicious code.
4. `-e` exits immediately on any command failure, `-u` treats unset variable use as an error, and `pipefail` makes a pipeline fail if any stage fails (not just the last) — together they convert silent, hard-to-diagnose failures into immediate, visible ones.
5. On many systems `/bin/sh` is a different, more limited shell (often `dash`) than `/bin/bash`, and Bash-specific syntax will fail or behave differently under `sh`.
</details>

## Best Practices
- Always include a shebang line as the very first line of every script.
- Use `set -euo pipefail` in scripts that matter, understanding the tradeoffs (it can make some legitimate "expected failure" patterns need explicit handling).
- Comment your scripts, especially anything non-obvious or security-relevant.
- Use `bash -n` before running any script you didn't write yourself, as a minimum safety check.
- Check exit codes (`$?`) explicitly at points in a script where failure would matter.

## Summary
A Bash script is just a saved sequence of shell commands, made runnable through a shebang line and the execute permission bit, with exit codes communicating success or failure back to whatever invoked it. Understanding this foundation — and knowing how to safely inspect a script before running it — underlies both effective scripting and basic incident-response script triage.

## What's Next?
With the basic structure of a script established, Lesson 02 covers variables — how scripts store, reference, and safely handle data.

---


## Navigation
⬅️ [Back to Module README](README.md) | [ Lab 01 : # 📖 Lesson 01 – Bash Fundamentals

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain what a shell is and how Bash fits into the Linux ecosystem
- Write, save, and execute a basic Bash script
- Use the shebang line, comments, and exit codes correctly
- Make a script executable and run it in multiple ways
- Debug a simple script using built-in Bash tooling

## Estimated Study Time
45 minutes (including lab)

## Difficulty
🟢 Beginner

## Prerequisites
- Comfortable running commands interactively in a Linux terminal
- No prior scripting experience required

## Key Terms
| Term | Definition |
|---|---|
| **Shell** | A program that interprets commands and provides a command-line interface to the OS |
| **Bash** | Bourne Again SHell — the most widely used Linux shell and scripting language |
| **Shebang** | The `#!` line at the top of a script specifying which interpreter should run it |
| **Exit code** | A numeric value (0–255) a script returns to indicate success (0) or a specific failure |
| **Executable bit** | The file permission that allows a script to be run directly |
| **stdin/stdout/stderr** | The standard input, output, and error streams a script reads from and writes to |

## Introduction
Bash is both an interactive shell and a full scripting language — the same syntax you use to run one command at a time can be saved into a file and executed as a repeatable program. This lesson covers the absolute foundation: what a script actually is, how Linux knows to run it correctly, and how to structure and execute even the simplest script correctly and safely.

## Why This Matters
Bash scripting is everywhere in Linux administration and security tooling — deployment scripts, log analysis pipelines, CI/CD glue code, and a huge fraction of both legitimate automation and attacker tooling are plain Bash. Being able to read an unfamiliar script quickly and confidently — understanding its shebang, structure, and exit behavior — is a prerequisite for nearly every other skill in this module and a genuinely common task in incident response (reading and understanding what a suspicious script actually does).

## Cybersecurity Insight
Malicious scripts dropped on a compromised host are very often plain Bash — a downloader, a persistence installer, a cleanup script. Being able to quickly read a script's shebang, structure, and behavior (without necessarily running it) is a core skill in incident response triage. A common first step is a static read-through (`cat script.sh` or `less script.sh`) followed by looking specifically for network calls (`curl`, `wget`, `nc`), suspicious redirection (`/dev/tcp/`), and encoded/obfuscated content (`base64 -d`, `eval`) before ever considering execution in an isolated sandbox.

## Researcher's Note
Malware analysts examining an unknown shell script typically avoid running it directly, instead using `shellcheck` (a static analysis tool, also useful for entirely legitimate script quality review) and manual read-throughs to understand behavior first. `bash -n script.sh` is a particularly useful trick — it performs a syntax check only, parsing the script without executing a single line, which is a safe first step even for scripts of unknown origin.

## Real-World Example
Numerous documented Linux malware families (including some cryptomining and botnet droppers) begin their infection chain with a short, unassuming Bash script — often distributed via a compromised web server or a `curl | bash` pattern (covered in the Linux Fundamentals module's Shell Redirection & Pipes lesson) — that downloads and installs the actual payload. Recognizing normal script structure well enough to spot when something deviates from it (unexplained obfuscation, unnecessary network calls, oddly broad permission changes) is a practical, everyday defensive skill built directly on the fundamentals in this lesson.

---

## Main Topic 1: Anatomy of a Bash Script

A minimal script:
```bash
#!/bin/bash
# This script prints a greeting

echo "Hello, world!"
exit 0
```

- **Shebang (`#!/bin/bash`)** — must be the very first line, telling the kernel which interpreter to use when the script is executed directly. Without it, the script falls back to being interpreted by whatever shell invokes it, which can cause subtle, hard-to-diagnose behavior differences.
- **Comments (`#`)** — anything after a `#` (other than the shebang itself) is ignored by Bash; use them generously to explain *why*, not just *what*.
- **Commands** — the actual instructions, identical in syntax to what you'd type interactively.
- **`exit` code** — `exit 0` signals success; any nonzero value (conventionally 1–255) signals a specific kind of failure. If omitted, a script exits with the status of its last executed command.

## Main Topic 2: Making a Script Executable and Running It

```bash
chmod +x myscript.sh        # add execute permission (see Linux Fundamentals, Lesson 02)
./myscript.sh                  # run directly (requires execute permission + correct shebang)
bash myscript.sh                  # run explicitly with bash, regardless of execute permission or shebang
sh myscript.sh                       # run with sh — NOT always the same as bash, see Common Mistakes
```
`./myscript.sh` relies on the shebang line to determine the interpreter and requires the execute bit to be set. `bash myscript.sh` bypasses both of those requirements by explicitly invoking Bash yourself — useful when testing a script you haven't made executable yet, or when you need to guarantee it runs under Bash specifically rather than whatever `sh` happens to be symlinked to on that system.

## Main Topic 3: Debugging Basics

```bash
bash -n script.sh          # syntax check ONLY — does not execute anything, safe for unknown scripts
bash -x script.sh              # execute with a trace of every command as it runs (very useful for debugging)
set -x                             # enable tracing from within a running script, for a specific section
set +x                                # disable tracing again
set -e                                   # exit immediately if any command fails (nonzero exit code)
set -u                                      # treat use of an unset variable as an error
```
Adding `set -euo pipefail` near the top of a script (the `pipefail` option additionally makes a pipeline fail if *any* stage fails, not just the last one) is one of the most commonly recommended defensive habits for production Bash scripts — it converts silent failures into loud, immediate ones, which is far easier to debug and far safer for scripts that matter.

---

## Tips
- Always start real scripts with `#!/bin/bash` (or `#!/usr/bin/env bash` for better portability across systems where Bash isn't at a fixed path) as the very first line, with nothing before it.
- Use `bash -n script.sh` as a zero-risk first step when looking at any script of unknown origin or complexity.
- Keep a habit of checking `echo $?` immediately after a command when troubleshooting — it shows the exit code of the last executed command.

## Common Mistakes
- Assuming `sh` and `bash` are always identical — on many distributions `/bin/sh` is actually a symlink to `dash` or another lighter shell with different (usually stricter, POSIX-only) syntax rules, and Bash-specific features (like arrays or `[[ ]]`) will fail under `sh`.
- Forgetting to `chmod +x` a script and being confused by a "permission denied" error when trying `./script.sh`.
- Putting anything (even a blank line or comment) before the shebang line, which breaks its recognition entirely.
- Not checking exit codes, silently continuing after a failed command that should have stopped the script.

## Common Misconceptions
- **"A script with no errors printed ran successfully."** A command can fail (return a nonzero exit code) without printing any visible error message at all — always check exit codes explicitly in scripts that matter, rather than assuming silence means success.
- **"`#!/bin/bash` and `#!/usr/bin/env bash` are functionally identical in every case."** They're usually equivalent, but `env bash` searches `PATH` for whichever `bash` comes first, which is more portable across systems where Bash isn't installed at the standard `/bin/bash` location (common on some BSD/macOS setups) but is technically dependent on `PATH` being trustworthy, which matters in hardened/restricted environments.
- **"Running `bash -x` is the same as reading the script's source."** Tracing shows what actually executes at runtime, including the *results* of variable expansion and command substitution — this can reveal important behavior that isn't obvious from the static source, especially with dynamically constructed commands.

## Quick Practice
```bash
cat << 'EOF' > practice.sh
#!/bin/bash
echo "Current user: $(whoami)"
echo "Current directory: $(pwd)"
exit 0
EOF
chmod +x practice.sh
./practice.sh
echo "Exit code was: $?"
```
Confirm the script runs and prints an exit code of `0`.

## Knowledge Check
1. What does the shebang line do, and why must it be the very first line of a script?
2. What's the difference between running `./script.sh` and `bash script.sh`?
3. What does `bash -n script.sh` do, and why is it a safe first step for an unfamiliar script?
4. What does `set -euo pipefail` accomplish, and why is it recommended for production scripts?
5. Why can't you assume `sh` and `bash` will run a script identically?

<details>
<summary>Answers</summary>

1. It tells the kernel which interpreter to use when the script is executed directly; it must be the first line because the kernel only checks the very beginning of the file for this directive.
2. `./script.sh` requires the execute permission bit and relies on the shebang to pick the interpreter; `bash script.sh` explicitly invokes Bash regardless of execute permission or shebang.
3. It performs a syntax check only, without executing any command in the script — a safe way to inspect an unfamiliar or untrusted script's basic validity without running potentially malicious code.
4. `-e` exits immediately on any command failure, `-u` treats unset variable use as an error, and `pipefail` makes a pipeline fail if any stage fails (not just the last) — together they convert silent, hard-to-diagnose failures into immediate, visible ones.
5. On many systems `/bin/sh` is a different, more limited shell (often `dash`) than `/bin/bash`, and Bash-specific syntax will fail or behave differently under `sh`.
</details>

## Best Practices
- Always include a shebang line as the very first line of every script.
- Use `set -euo pipefail` in scripts that matter, understanding the tradeoffs (it can make some legitimate "expected failure" patterns need explicit handling).
- Comment your scripts, especially anything non-obvious or security-relevant.
- Use `bash -n` before running any script you didn't write yourself, as a minimum safety check.
- Check exit codes (`$?`) explicitly at points in a script where failure would matter.

## Summary
A Bash script is just a saved sequence of shell commands, made runnable through a shebang line and the execute permission bit, with exit codes communicating success or failure back to whatever invoked it. Understanding this foundation — and knowing how to safely inspect a script before running it — underlies both effective scripting and basic incident-response script triage.

## What's Next?
With the basic structure of a script established, Lesson 02 covers variables — how scripts store, reference, and safely handle data.

---

## 🧪 Hands-On Lab: Bash Fundamentals

**Objective:** Write, execute, debug, and safely inspect basic Bash scripts.

**Tasks:**
1. Write a script called `system_info.sh` that prints the current user, hostname, current date, and current working directory, each on its own line, using command substitution (`$(...)`).
2. Make it executable and run it using `./system_info.sh`.
3. Run the same script a second way, using `bash system_info.sh` without relying on the execute bit, and confirm the output is identical.
4. Intentionally introduce a syntax error (e.g., an unclosed quote) and run `bash -n system_info.sh` to catch it without executing the script — record the error message.
5. Fix the error, then add `set -euo pipefail` near the top of the script, followed by a deliberately failing command (e.g., `ls /nonexistent_directory`) after your existing lines — run it and confirm the script stops immediately at that point rather than continuing.
6. Remove the deliberately failing command, run the script one final time, and check `echo $?` immediately afterward to confirm a `0` exit code.
7. Write a second, tiny script that only contains `curl http://example.com/install.sh | bash` (do not actually connect anywhere real — this is for static analysis practice only, feel free to use a placeholder domain). Practice the "safe first look" workflow: read the file with `cat`, run `bash -n` on it, and write 2–3 sentences describing what red flags this pattern would represent if you found it on a system you were investigating.

**Deliverable:** The final `system_info.sh` script content, the syntax error message from Task 4, and your 2–3 sentence write-up from Task 7.

---

## Navigation
⬅️ [Back to Module README](README.md) | [Lab 01 : Bash Fundamentals ](../../labs/module-04/01-bash-fundamentals.md) | ➡️ [Next: Lesson 02 – Variables](02-variables.md)
