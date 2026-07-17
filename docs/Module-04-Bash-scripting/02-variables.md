# 📖 Lesson 02 – Variables

## Learning Outcomes
By the end of this lesson, you will be able to:
- Declare, assign, and reference Bash variables correctly
- Explain the critical difference between quoted and unquoted variable expansion
- Use positional parameters and special variables to handle script arguments
- Work with arrays and basic parameter expansion
- Avoid common variable-handling mistakes that lead to security or reliability bugs

## Estimated Study Time
45 minutes (including lab)

## Difficulty
🟢 Beginner

## Prerequisites
- Lesson 01: Bash Fundamentals

## Key Terms
| Term | Definition |
|---|---|
| **Variable** | A named storage location for a value within a script or shell session |
| **Expansion** | The process of substituting a variable reference (`$var`) with its actual value |
| **Word splitting** | Bash's default behavior of splitting unquoted variable values on whitespace |
| **Positional parameter** | A script argument, referenced as `$1`, `$2`, etc. |
| **Special variable** | A built-in variable with reserved meaning, e.g., `$?`, `$#`, `$@`, `$0` |
| **Array** | A variable that holds multiple values, indexed numerically (or associatively) |

## Introduction
Variables are how Bash scripts store and manipulate data — command output, user input, file paths, configuration values. The mechanics are simple on the surface, but Bash's quoting and expansion rules have real, sharp edges: unquoted variables can silently split into multiple words or expand unexpected glob patterns, and getting this wrong is one of the most common sources of both ordinary script bugs and genuine security vulnerabilities (command injection-style issues) in shell scripts.

## Why This Matters
Improper variable handling — especially unquoted expansion of untrusted input — is directly responsible for a well-documented class of shell script vulnerabilities, ranging from broken behavior with filenames containing spaces to full command injection when a variable's value is attacker-controlled and gets word-split or re-interpreted in a dangerous context. Getting quoting right isn't a stylistic preference; it's a security control.

## Cybersecurity Insight
Passing untrusted input (user input, downloaded data, filenames from an unknown source) into a variable and then using it unquoted or inside `eval` is a direct path to command injection in Bash scripts — for example, a filename crafted as `; rm -rf ~ ;` interpolated unsafely into a constructed command string can execute arbitrary commands. Always quoting variable expansions (`"$var"` rather than `$var`) and avoiding `eval` on any data that could be influenced by an untrusted source are two of the most impactful defensive habits in shell scripting.

## Researcher's Note
Static analysis tools like `shellcheck` (introduced in Lesson 01) flag unquoted variable expansions by default, specifically because they're such a common and consequential class of bug — both a reliability issue (breaking on filenames with spaces) and a security issue (enabling word-splitting or glob-expansion-based injection in certain contexts). Security researchers reviewing shell scripts as part of a code audit treat unquoted expansion of any externally influenced variable as a finding worth investigating, not just a style nitpick.

## Real-World Example
A well-known and frequently cited category of shell scripting vulnerability involves scripts that build a command string by concatenating user-supplied input and then execute it via `eval` or an unquoted expansion — attacker-supplied input containing shell metacharacters (`;`, `|`, `` ` ``, `$()`) can break out of the intended context and execute arbitrary commands with the script's privileges. This exact pattern appears repeatedly in vulnerability disclosures for web application backend scripts, CI/CD pipeline scripts, and administrative tooling written in Bash.

---

## Declaring and Using Variables

```bash
name="Alice"              # NO spaces around the = sign — this is mandatory syntax
echo "$name"                  # reference with $, always quote in practice
echo ${name}                     # braces are optional here, but required for disambiguation, e.g. "${name}s"

readonly PI=3.14159            # cannot be reassigned after this point
unset name                        # remove a variable entirely

count=$((5 + 3))                     # arithmetic expansion
echo "$count"                            # prints 8
```
Bash variables are untyped by default — everything is stored as a string internally, even numbers, until used in an arithmetic context like `$(( ))`.

**Command substitution** captures a command's output into a variable:
```bash
current_user=$(whoami)          # modern syntax, preferred
current_user=`whoami`               # older backtick syntax, still valid but harder to nest/read
```

## Quoting — The Single Most Important Habit

```bash
name="John Smith"
echo $name          # BAD: word-splits into "John" and "Smith" as separate arguments
echo "$name"           # GOOD: preserved as a single value, "John Smith"
```
This isn't just cosmetic. Consider a real-world-shaped example:
```bash
file="my report.txt"
rm $file          # DANGEROUS/WRONG: attempts to remove two files, "my" and "report.txt"
rm "$file"           # CORRECT: removes the single intended file
```
**Double quotes** (`"$var"`) preserve the variable's value as a single word and still allow variable/command substitution inside them. **Single quotes** (`'$var'`) preserve everything completely literally, including `$` itself — no expansion happens at all. As a default habit: always double-quote variable expansions unless you have a specific, deliberate reason not to (such as intentionally wanting word-splitting for a controlled, known-safe list).

## Positional Parameters, Special Variables, and Arrays

When a script is run with arguments, Bash exposes them as **positional parameters**:
```bash
#!/bin/bash
echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "All arguments (as one word): $*"
echo "All arguments (each preserved separately): $@"
echo "Number of arguments: $#"
echo "Exit code of last command: $?"
echo "Process ID of this script: $$"
```
`"$@"` (quoted, with the `@`) is almost always what you want when passing arguments through to another command, because it preserves each argument as a separate word even if it contains spaces — `"$*"` collapses everything into a single string, which is rarely the intended behavior.

**Arrays** hold multiple values:
```bash
fruits=("apple" "banana" "cherry")
echo "${fruits[0]}"            # first element: "apple"
echo "${fruits[@]}"               # all elements, each preserved separately
echo "${#fruits[@]}"                 # number of elements: 3
fruits+=("date")                       # append an element

for fruit in "${fruits[@]}"; do
    echo "$fruit"
done
```

---

## Tips
- Run `shellcheck yourscript.sh` regularly — it catches quoting issues, word-splitting risks, and dozens of other common mistakes automatically, far faster than manual review.
- Use `${var:-default}` for a safe fallback value when a variable might be unset (`echo "${name:-Unknown}"`), which pairs well with `set -u` from Lesson 01.
- Prefer `"$@"` over `"$*"` any time you're forwarding arguments to another command.

## Common Mistakes
- Adding spaces around `=` in an assignment (`name = "Alice"`), which Bash interprets as attempting to run a command called `name` with arguments `=` and `"Alice"`, producing a confusing error.
- Leaving variable expansions unquoted, causing word-splitting and glob-expansion bugs — especially dangerous with filenames or any value that could contain spaces or shell metacharacters.
- Using `eval` on any value that could be influenced by external/untrusted input.
- Confusing `$*` and `$@` and being surprised when argument handling breaks for multi-word arguments.

## Common Misconceptions
- **"Quoting is just a style preference for readability."** Quoting materially changes behavior — unquoted expansion can word-split and glob-expand a value, which is a functional (and sometimes security-relevant) difference, not a cosmetic one.
- **"Variables in Bash have types like numbers or strings."** Bash variables are untyped strings by default; arithmetic context (`$(( ))`) is what makes a value behave numerically, and this is worth understanding explicitly rather than assuming type inference like in other languages.
- **"`local` variables inside functions work the same as regular variables."** (Preview for Lesson 04) Without the `local` keyword, variables assigned inside a function are global by default in Bash, which surprises people coming from languages with automatic function-scoped variables.

## Quick Practice
```bash
greeting="Hello there"
echo $greeting
echo "$greeting"
```
Observe the difference in output between the two `echo` calls and explain why in your own words.

## Knowledge Check
1. Why must there be no spaces around the `=` sign in a variable assignment?
2. What's the practical difference between `"$var"` and `$var` in a command?
3. What's the difference between `$@` and `$*` when both are quoted?
4. Why is passing untrusted input into `eval` considered dangerous?
5. What does `${var:-default}` do, and when is it useful?

<details>
<summary>Answers</summary>

1. Bash would otherwise interpret the first token as a command name and the `=` and following value as separate arguments to it, rather than as an assignment.
2. `"$var"` preserves the value as a single word (no word-splitting or glob-expansion); `$var` unquoted is subject to word-splitting on whitespace and glob-expansion, which can break or change behavior unexpectedly.
3. `"$@"` expands to each argument as a separate, preserved word; `"$*"` expands to all arguments joined into a single string — `"$@"` is almost always the correct choice for forwarding arguments.
4. `eval` executes its argument as if it were typed directly into the shell; untrusted input containing shell metacharacters can inject and execute arbitrary commands.
5. It provides a fallback value if the variable is unset or empty, which is useful for writing scripts that behave safely and predictably even with missing input, especially combined with `set -u`.
</details>

## Best Practices
- Always double-quote variable expansions unless you have a specific, deliberate reason not to.
- Never pass untrusted or externally-influenced input into `eval`.
- Use `shellcheck` regularly on any script you write or review.
- Prefer `"$@"` over `"$*"` when forwarding script arguments.
- Use `${var:-default}` or explicit checks for variables that might be unset, especially in scripts using `set -u`.

## Summary
Variables store data in Bash scripts, but the real skill lies in handling expansion and quoting correctly — unquoted expansions cause both reliability bugs (breaking on spaces/special characters) and genuine security vulnerabilities (command injection via word-splitting or `eval`). Positional parameters and arrays extend this foundation to handle script arguments and multi-value data safely, provided the same quoting discipline is applied consistently.

## What's Next?
With variables and safe data handling covered, Lesson 03 introduces loops — how to repeat actions across a set of values, files, or conditions, building directly on the array and quoting concepts from this lesson.

---


## Navigation
⬅️ [Previous: Lesson 01 – Bash Fundamentals](01-bash-fundamentals.md) | [Lab 02 : Variables](../../labs/module-04/02-variables.md) | ➡️ [Next: Lesson 03 – Loops](03-loops.md)
