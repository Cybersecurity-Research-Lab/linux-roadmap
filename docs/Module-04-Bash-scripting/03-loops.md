# 📖 Lesson 03 – Loops

## Learning Outcomes
By the end of this lesson, you will be able to:
- Write `for`, `while`, and `until` loops correctly in Bash
- Iterate safely over lists, arrays, command output, and file contents
- Use loop control statements (`break`, `continue`) appropriately
- Avoid common pitfalls when looping over filenames and command output
- Apply loops to practical administrative and log-analysis tasks

## Estimated Study Time
50 minutes (including lab)

## Difficulty
🟡 Beginner–Intermediate

## Prerequisites
- Lesson 01: Bash Fundamentals
- Lesson 02: Variables (quoting discipline is essential for safe looping)

## Key Terms
| Term | Definition |
|---|---|
| **`for` loop** | Iterates over a fixed list of items (words, array elements, glob results) |
| **`while` loop** | Repeats as long as a condition remains true |
| **`until` loop** | Repeats until a condition becomes true (inverse of `while`) |
| **`break`** | Immediately exits the current loop |
| **`continue`** | Skips the rest of the current iteration and moves to the next |
| **IFS** | Internal Field Separator — controls how Bash splits words during iteration |

## Introduction
Loops let a script repeat an action across a set of items — every file in a directory, every line in a log, every entry in an array, or simply "keep going until a condition changes." Bash offers three loop constructs (`for`, `while`, `until`), each suited to different situations, and getting the iteration and quoting right is what separates a script that works reliably from one that silently breaks on the first filename with a space in it.

## Why This Matters
Loops are the backbone of nearly every practical automation and log-analysis script — checking every host in a list, processing every log line, iterating over every file matching a pattern. They're also where a huge fraction of real-world script bugs originate, because looping over unquoted command output or improperly delimited data is a classic, well-documented source of both reliability failures and injection-adjacent security bugs.

## Cybersecurity Insight
A very common and risky anti-pattern is looping over the output of `ls` (`for file in $(ls)`) instead of using a glob directly (`for file in *`) or `find` with `-print0`/`read -d ''` — filenames can legitimately contain spaces, newlines, or even characters resembling command syntax, and naive looping over unquoted `ls` output can misinterpret a maliciously or accidentally crafted filename as multiple arguments, or worse. This is a frequently cited example in secure shell scripting guidance precisely because it looks correct and works fine in casual testing, right up until it encounters a filename that breaks the assumption.

## Researcher's Note
Security researchers writing bulk-processing tools (e.g., scanning many files or log entries for indicators of compromise) consistently favor `find ... -print0 | while IFS= read -r -d '' file` over naive `for` loops on `ls`/`find` output specifically because it correctly handles filenames containing spaces, newlines, or unusual characters without misinterpreting them — a detail that matters enormously when processing files from an untrusted or adversary-controlled source, such as an attacker's dropped tooling during an investigation.

## Real-World Example
Publicly documented shell scripting guidance (including OWASP-adjacent secure coding resources and numerous `shellcheck` rule explanations) repeatedly highlights the `for file in $(ls)` pattern as a textbook mistake — real incidents and CTF-style demonstrations have shown that a file named something like `` `rm -rf ~` `` (using backticks) or containing embedded newlines can, under certain naive scripting patterns, cause behavior far beyond what the script author intended, reinforcing why safe iteration technique is treated as a core scripting skill rather than a minor detail.

---

## The for Loop

```bash
# Iterate over a fixed list
for color in red green blue; do
    echo "Color: $color"
done

# Iterate over an array (always quote with "${array[@]}")
fruits=("apple" "banana" "cherry")
for fruit in "${fruits[@]}"; do
    echo "Fruit: $fruit"
done

# Iterate over files using a glob (safe — handles spaces correctly)
for file in /var/log/*.log; do
    echo "Found log file: $file"
done

# C-style for loop, for numeric iteration
for ((i=1; i<=5; i++)); do
    echo "Iteration: $i"
done
```
The glob-based `for file in *.log` form is safe because Bash expands the glob into an array-like list of properly delimited filenames internally — this is different from, and safer than, capturing `ls` output as a plain string and then word-splitting it.

## The while and until Loops

```bash
# Basic while loop
count=1
while [ "$count" -le 5 ]; do
    echo "Count: $count"
    count=$((count + 1))
done

# Reading a file line by line — the SAFE, standard pattern
while IFS= read -r line; do
    echo "Line: $line"
done < "/etc/hosts"

# until loop — repeats until the condition becomes true
attempts=0
until [ "$attempts" -ge 3 ]; do
    echo "Attempt $((attempts + 1))"
    attempts=$((attempts + 1))
done
```
The `while IFS= read -r line; do ... done < file` pattern is the standard, safe way to process a file line by line in Bash: `IFS=` prevents leading/trailing whitespace from being stripped, and `-r` prevents backslash sequences in the input from being interpreted/escaped unexpectedly — both details that matter when processing untrusted or unusual input, such as log files or files recovered during an investigation.

## Loop Control and Practical Patterns

```bash
# break — exit the loop immediately
for i in 1 2 3 4 5; do
    if [ "$i" -eq 3 ]; then
        break
    fi
    echo "$i"
done   # prints 1, 2

# continue — skip to the next iteration
for i in 1 2 3 4 5; do
    if [ "$i" -eq 3 ]; then
        continue
    fi
    echo "$i"
done   # prints 1, 2, 4, 5
```

**Safe iteration over `find` results** (handles any filename, including ones with spaces or newlines):
```bash
find /var/log -name "*.log" -print0 | while IFS= read -r -d '' file; do
    echo "Processing: $file"
done
```
`-print0` and `read -d ''` use the null byte as a delimiter instead of newline/whitespace — since a null byte can never legally appear in a Unix filename, this is the only fully robust way to handle arbitrary filenames in a loop.

**A realistic security-relevant example** — scanning multiple log files for a pattern and reporting counts:
```bash
for logfile in /var/log/auth.log*; do
    count=$(grep -c "Failed password" "$logfile" 2>/dev/null)
    echo "$logfile: $count failed login attempts"
done
```

---

## Tips
- Default to `for file in *` (glob) or the `find -print0 | while read -r -d ''` pattern for filenames; avoid looping over raw `ls` output.
- Always use `read -r` (not just `read`) unless you specifically want backslash escape interpretation, which is rarely what you actually want.
- Use `((i++))` or `i=$((i + 1))` for arithmetic inside loops rather than external tools like `expr`, which is slower and considered outdated style.

## Common Mistakes
- Looping over unquoted `$(ls)` output instead of a glob or `find -print0`, breaking on filenames with spaces or special characters.
- Forgetting `IFS=` before `read` when processing a file line by line, causing leading/trailing whitespace to be silently stripped.
- Off-by-one errors in C-style `for` loops (`<=` vs `<`), especially when translating logic from another language.
- Modifying the array or list being iterated over from inside the loop, causing unpredictable behavior.

## Common Misconceptions
- **"`for file in $(ls)` and `for file in *` are basically the same thing."** They behave identically for simple filenames with no spaces or special characters, but diverge significantly — and dangerously — the moment a filename doesn't fit that assumption; the glob form is unconditionally safer.
- **"A `while` loop reading a file is basically the same as `cat file | while read line`."** Piping into a `while` loop runs the loop in a subshell, meaning any variables set inside the loop won't persist after it ends — using `< file` redirection instead (as shown above) avoids this subshell scoping trap.
- **"`break` and `continue` work the same in nested loops without extra care."** By default, `break`/`continue` only affect the innermost loop; breaking out of an outer loop from within a nested one requires `break 2` (or the appropriate level number), which is easy to forget.

## Quick Practice
```bash
for n in {1..5}; do
    if [ "$n" -eq 3 ]; then
        continue
    fi
    echo "Number: $n"
done
```
Predict the output before running it, paying attention to which number is skipped.

## Knowledge Check
1. Why is `for file in $(ls)` considered an unsafe pattern compared to `for file in *`?
2. What does `IFS=` accomplish when placed before a `read` command?
3. What's the difference between `break` and `continue`?
4. Why does piping into a `while read` loop (`cat file | while read line`) cause variable scoping issues that redirection (`< file`) avoids?
5. What delimiter does `find -print0` use, and why is it the only fully robust choice for arbitrary filenames?

<details>
<summary>Answers</summary>

1. `$(ls)` output is subject to word-splitting on whitespace, so filenames containing spaces (or other unusual characters) get incorrectly split into multiple items; a glob expansion handles filenames as properly delimited units regardless of their content.
2. It prevents `read` from stripping leading and trailing whitespace from each line, preserving the line's content exactly as it appears in the input.
3. `break` exits the loop entirely; `continue` skips the remainder of the current iteration and moves on to the next one, without exiting the loop.
4. A pipeline runs each stage (including the `while` loop) in its own subshell, so variables modified inside the loop don't persist in the parent shell once the pipeline finishes; `< file` redirection runs the loop in the current shell, avoiding this issue.
5. The null byte (`\0`); it's the only character that can never legally appear inside a Unix filename, making it the only delimiter that can unambiguously separate any possible filename.
</details>

## Best Practices
- Prefer glob expansion (`for file in *.log`) or `find -print0 | while read -r -d ''` over looping on raw `ls`/`find` output for anything involving filenames.
- Always use `read -r` and `IFS=` together when reading input line by line.
- Use redirection (`< file`) rather than piping into a `while read` loop when you need variables set inside the loop to persist afterward.
- Keep loop bodies focused and readable; extract complex per-iteration logic into a function (previewed in Lesson 04) once a loop body grows beyond a few lines.

## Summary
Bash's `for`, `while`, and `until` loops cover the large majority of iteration needs in real scripts, but safe iteration — especially over filenames and untrusted input — depends on the quoting and delimiter discipline introduced in this lesson. The `find -print0 | while read -r -d ''` pattern in particular is worth internalizing as the default, robust choice whenever filenames are involved.

## What's Next?
As loop bodies and scripts grow more complex, repeating the same logic in multiple places becomes unwieldy. Lesson 04 introduces functions — how to package reusable logic cleanly, including logic you'll likely want to call from inside loops like the ones in this lesson.

---

## Navigation
⬅️ [Previous: Lesson 02 – Variables](02-variables.md) | [ Lab 03 : Loops](../../labs/module-04/03-loops.md) | ➡️ [Next: Lesson 04 – Functions](04-functions.md)
