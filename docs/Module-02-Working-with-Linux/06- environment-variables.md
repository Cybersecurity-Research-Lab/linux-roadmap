# 📖 Lesson 06 – Environment Variables

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain what environment variables are and how processes inherit them
- View, set, export, and unset environment variables
- Understand the role of `PATH`, `HOME`, and other key system variables
- Configure variables persistently via shell profile files
- Recognize the security risks of storing secrets in environment variables

## Estimated Study Time
40 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 03: Processes (environment variables are inherited by child processes)
- Basic shell comfort

## Key Terms
| Term | Definition |
|---|---|
| **Environment variable** | A named value available to a process and its children, used for configuration |
| **Shell variable** | A variable local to the current shell only, not passed to child processes unless exported |
| **`PATH`** | The list of directories the shell searches for executable commands |
| **Export** | The act of marking a shell variable so it's inherited by child processes |
| **Shell profile** | A startup file (e.g., `.bashrc`, `.bash_profile`, `/etc/environment`) that sets variables persistently |
| **Scope** | Whether a variable is available only in the current shell, or inherited by every child process |

## Introduction
Programs need configuration, and rather than hardcoding it, Linux (and Unix generally) provides environment variables — named key/value pairs that live in a process's memory and get inherited by any child process it spawns. Your shell prompt, which editor opens when you type `git commit`, where the system looks for commands, and countless application-specific settings all flow through environment variables.

## Why This Matters
Environment variables are everywhere in both legitimate configuration and attack surface. A misconfigured or maliciously modified `PATH` can trick a privileged process into running an attacker's binary instead of the real one. Secrets (API keys, database passwords) stored carelessly in environment variables can leak through process listings, crash logs, or child process inheritance. Understanding how variables are scoped and inherited is essential to both configuring systems correctly and recognizing when that mechanism is being abused.

## Cybersecurity Insight
`PATH` manipulation is a classic privilege escalation and persistence technique: if a privileged script or SUID binary calls another program *without specifying its full path* (e.g., `ls` instead of `/bin/ls`), and an attacker can influence the `PATH` variable seen by that process (or write to an early directory in it), they can plant a malicious binary with the same name that executes instead — inheriting whatever privilege the calling process had. This is exactly why well-written privileged scripts hardcode full paths rather than relying on `PATH`.

## Researcher's Note
Security researchers auditing SUID binaries or privileged cron jobs routinely check what environment they inherit and whether any called commands are unqualified (no full path). Tools like `strace -f` (tracing system calls, including `execve`) are commonly used to observe exactly which binaries a privileged process actually resolves and executes at runtime — surfacing `PATH`-dependent behavior that isn't obvious just from reading the script.

## Real-World Example
A well-documented class of privilege escalation vulnerability involves a root-owned cron job or SUID script calling a common command like `python` or `curl` by name only. If the script's execution environment includes a writable directory early in `PATH` (or an attacker can influence `PATH` before the script runs), placing a malicious executable with that same name in that directory causes it to run with the script's elevated privileges — a technique documented across many local privilege escalation write-ups and one of the reasons `PATH` hygiene is a standard hardening checklist item.

---

## Viewing and Setting Variables

```bash
printenv                  # list all environment variables for this shell
printenv PATH              # show a single variable
echo $HOME                 # reference a variable's value with $
env                         # similar to printenv, also used to run a command with a modified environment
set                         # list ALL variables, including shell-local ones not exported
```

Setting a variable for the **current shell only**:
```bash
MY_VAR="hello"
echo $MY_VAR       # works here
bash -c 'echo $MY_VAR'   # empty — not inherited by the child shell, because it wasn't exported
```

**Exporting** makes it available to child processes:
```bash
export MY_VAR="hello"
bash -c 'echo $MY_VAR'   # now prints "hello"
```

Unsetting a variable:
```bash
unset MY_VAR
```

Setting a variable for a single command only, without affecting the current shell at all:
```bash
MY_VAR="temp" some_command
```

## Key System Variables

| Variable | Purpose |
|---|---|
| `PATH` | Colon-separated list of directories searched (in order) for executable commands |
| `HOME` | The current user's home directory |
| `USER` | The current logged-in username |
| `SHELL` | The user's default login shell |
| `PWD` | The current working directory |
| `LANG` | System locale/language setting |
| `EDITOR` | The default text editor invoked by other programs (e.g., `crontab -e`, `git commit`) |

Inspecting `PATH` closely:
```bash
echo $PATH
# /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```
Directories are searched **left to right**, and the first match wins. This ordering matters enormously for security: if a writable, low-trust directory appears *before* the trusted system directories in `PATH`, any binary placed there with the name of a common command will shadow the real one.

## Persistence — Profile Files and Scope

Variables set interactively in a shell disappear when that shell closes. To persist configuration, variables are set in shell startup/profile files:

| File | Scope |
|---|---|
| `/etc/environment` | System-wide, applies to all users, parsed before shell-specific files |
| `/etc/profile` | System-wide, sourced by login shells |
| `~/.bash_profile` or `~/.profile` | Per-user, sourced at login |
| `~/.bashrc` | Per-user, sourced for interactive non-login shells (most terminal sessions) |

Adding a directory to `PATH` persistently for a single user:
```bash
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc     # reload without logging out
```
Note the pattern `$HOME/bin:$PATH` — this *prepends* the new directory, meaning it's searched first. Prepending an untrusted or writable directory is exactly the mistake that enables the `PATH`-hijacking attack described above; system-critical additions should generally be appended, not prepended, unless you specifically intend to override a system command.

---

## Tips
- `env -i command` runs a command with a *completely empty* environment — useful for testing what a script actually depends on from its environment.
- Use `printenv VARNAME` instead of `echo $VARNAME` when scripting, since it fails cleanly (non-zero exit code) if the variable doesn't exist, which `echo` won't tell you.
- `.bashrc` vs `.bash_profile` confusion is nearly universal — as a simple rule of thumb, put variable exports meant for all shells (interactive and login) in `.bashrc`, and have `.bash_profile` source `.bashrc` if it doesn't already.

## Common Mistakes
- Setting a variable without `export` and being confused why a child process (like a script or subshell) doesn't see it.
- Prepending an untrusted or world-writable directory to `PATH`.
- Storing secrets (API keys, passwords) directly in shell history or `.bashrc` in plaintext, where they're readable by anyone with file access and often visible in process listings if passed as command-line arguments.
- Forgetting to `source` a profile file after editing it, then wondering why changes "didn't take" (they will on the next new shell, just not the current one).

## Common Misconceptions
- **"Environment variables set in one terminal are available everywhere."** They're scoped to that shell process and its children only — a variable set in one terminal window has no effect on a separate terminal window unless it's persisted in a profile file both sessions load.
- **"Exporting a variable makes it secure."** `export` only controls *inheritance* by child processes, not confidentiality — exported variables are visible to any process running as the same user, and sometimes to other users via `/proc/<PID>/environ` if permissions allow.
- **"`PATH` order doesn't really matter as long as the directory is there."** Order determines which binary actually runs when multiple directories in `PATH` contain a file with the same name — this is precisely the mechanism behind `PATH`-hijacking attacks.

## Quick Practice
```bash
echo $PATH
mkdir -p ~/testbin
echo '#!/bin/bash' > ~/testbin/ls
echo 'echo "gotcha"' >> ~/testbin/ls
chmod +x ~/testbin/ls
export PATH="$HOME/testbin:$PATH"
ls
```
Observe what happens, then explain in your own words why this occurred — and then clean up (`unset PATH` won't fully restore it; open a new shell instead, or manually reset `PATH`).

## Knowledge Check
1. What's the difference between a shell variable and an exported environment variable?
2. Why does `PATH` order matter for security?
3. What file would you edit to persist a `PATH` change for a single user across all their future login sessions?
4. Why is storing a secret directly in an environment variable not automatically "safe"?
5. What does `env -i command` do, and why might that be useful for testing?

<details>
<summary>Answers</summary>

1. A shell variable exists only in the current shell; an exported variable is also inherited by any child processes that shell spawns.
2. `PATH` is searched left to right, and the first matching executable wins — a malicious binary in an earlier, writable directory can shadow a legitimate command with the same name.
3. `~/.bash_profile` (or `~/.profile`), possibly with `~/.bashrc` also updated depending on shell configuration, since both may be sourced depending on session type.
4. Exported variables are visible to any process running as the same user and can leak via `/proc/<PID>/environ`, crash dumps, or logs — export controls inheritance, not confidentiality.
5. It runs a command with a completely empty environment, useful for discovering exactly which environment variables a script or program actually depends on to function correctly.
</details>

## Best Practices
- Never prepend untrusted or user-writable directories to `PATH`; append them, or better, avoid it entirely.
- Use full, absolute paths for commands inside privileged scripts and SUID-related code rather than relying on `PATH` resolution.
- Avoid storing secrets directly in environment variables where possible; use dedicated secrets management tools, or at minimum restrict which processes/users can read them.
- Keep `.bashrc`/`.bash_profile` changes documented and reviewed, especially on shared or production systems.
- Use `env -i` or a clean container/VM when testing scripts to verify they don't silently depend on your personal shell configuration.

## Summary
Environment variables carry configuration from a shell into every process it spawns, governed by scope (local vs. exported) and persisted through profile files. `PATH` is the most security-relevant variable on the system, since its ordering determines which executable actually runs — making `PATH` hygiene a recurring theme in both hardening guides and real-world privilege escalation techniques.

## What's Next?
With configuration and process context covered, the final lesson ties commands together: shell redirection and pipes, which let you chain simple tools into powerful workflows — a skill central to both administration and security tooling.

---

## Navigation
⬅️ [Previous: Lesson 05 – Package Managers](05-package-managers.md) | [Lab 06 : Environment Variables](../../labs/module-02/06-environment-variables.md) | ➡️ [Next: Lesson 07 – Shell Redirection & Pipes](07-shell-redirection-pipes.md)
