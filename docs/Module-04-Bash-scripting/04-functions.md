# 📖 Lesson 04 – Functions

## Learning Outcomes
By the end of this lesson, you will be able to:
- Define and call Bash functions with and without arguments
- Use `local` variables correctly to avoid unintended global state
- Return data from functions using exit codes, `echo`, and global variable assignment
- Organize a script into logically separated, reusable functions
- Recognize how function-based structure improves the auditability of security-relevant scripts

## Estimated Study Time
50 minutes (including lab)

## Difficulty
🟡 Intermediate

## Prerequisites
- Lesson 02: Variables
- Lesson 03: Loops (functions are commonly called from within loops)

## Key Terms
| Term | Definition |
|---|---|
| **Function** | A named, reusable block of code that can be called multiple times with different arguments |
| **`local`** | A keyword that scopes a variable to the function it's declared in, rather than globally |
| **Return value** | In Bash, a function's numeric exit status (0–255); actual data is typically returned via `echo`/output capture |
| **Global variable** | A variable accessible from anywhere in the script, including inside functions, unless shadowed by `local` |
| **Recursion** | A function calling itself, directly or indirectly |

## Introduction
As scripts grow, repeating the same logic in multiple places becomes error-prone and hard to maintain. Functions solve this by packaging logic into named, reusable, testable units — the same discipline that makes code manageable in any programming language. Bash's function model has a few quirks worth understanding deliberately, especially around variable scope and how "return values" actually work, since they differ meaningfully from most other languages.

## Why This Matters
Well-structured, function-based scripts are dramatically easier to read, audit, and safely modify than long, flat scripts — a genuinely important property for any script that will be reviewed for security purposes, run with elevated privileges, or maintained by more than one person over time. Conversely, poorly scoped variables (forgetting `local`) are a real source of subtle bugs, where a function unexpectedly overwrites a variable used elsewhere in the script.

## Cybersecurity Insight
Security-critical scripts (privileged automation, deployment tooling, incident response scripts) benefit enormously from being organized into small, single-purpose functions with `local` variables — this makes each piece independently reviewable and testable, which matters a great deal when a script runs with root privileges. A large monolithic script with many global variables is significantly harder to audit for unintended side effects or injection risks than the same logic broken into clearly-scoped, well-named functions.

## Researcher's Note
When reviewing an unfamiliar script for security purposes, researchers often use function boundaries as natural units of analysis — asking "what does this specific function do, what does it take as input, what does it affect globally" one function at a time, rather than trying to hold an entire monolithic script in their head at once. Scripts that never use `local` and rely heavily on global state are noted as harder to safely audit, precisely because any function could be silently affecting variables used elsewhere.

## Real-World Example
Post-incident reviews of complex, long-lived administrative shell scripts (the kind that accumulate features over years) frequently cite unclear variable scope and lack of function decomposition as contributing factors when a script's behavior changed unexpectedly after a seemingly unrelated modification — a new function reusing a variable name already used elsewhere in the script, without `local`, silently altered behavior in a distant part of the code. This is a well-known enough failure mode that "always use `local` inside functions" appears in nearly every Bash style guide and linting tool as a default rule.

---

## Main Topic 1: Defining and Calling Functions

```bash
# Two equivalent syntaxes
greet() {
    echo "Hello, $1!"
}

function greet2 {
    echo "Hi there, $1!"
}

greet "Alice"          # call it like any other command, arguments follow the same as a script
greet2 "Bob"
```
Inside a function, `$1`, `$2`, etc. refer to the arguments *passed to the function*, not the script's own arguments — this is a common point of confusion, since the syntax looks identical to positional parameters at the script level (Lesson 02) but refers to a completely different, function-local set of values.

## Main Topic 2: Variable Scope and local

```bash
counter=10          # global variable

increment() {
    local counter          # shadows the global "counter" within this function only
    counter=$((counter + 1))
    echo "Inside function: $counter"
}

increment                # prints "Inside function: 1" (local counter starts unset -> treated as 0)
echo "Outside function: $counter"      # prints "Outside function: 10" — global untouched
```
Without `local`, any variable assigned inside a function is **global by default** in Bash — a significant difference from many other languages, and a common source of subtle bugs where a function unintentionally overwrites a variable used elsewhere in the script. As a strong default habit: declare every variable used only within a function as `local`, unless you specifically intend for it to modify global state.

## Main Topic 3: Returning Data from Functions

Bash functions don't return arbitrary data types the way functions do in most other languages — they have a numeric **exit status** (0–255, checked via `$?` or directly in a conditional), and any actual data is typically communicated either by printing to stdout (and having the caller capture it with command substitution) or by explicitly setting a global variable.

```bash
# Pattern 1: exit status for success/failure
is_valid_ip() {
    local ip="$1"
    if [[ "$ip" =~ ^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$ ]]; then
        return 0        # success
    else
        return 1            # failure
    fi
}

if is_valid_ip "192.168.1.1"; then
    echo "Valid format"
else
    echo "Invalid format"
fi

# Pattern 2: echo + command substitution, for returning actual data
get_username_from_line() {
    local line="$1"
    echo "${line%%:*}"          # everything before the first colon
}

first_user=$(get_username_from_line "root:x:0:0:root:/root:/bin/bash")
echo "$first_user"          # prints "root"
```
Mixing the two patterns carelessly — echoing informational messages *and* the actual return value from the same function — is a common mistake, since the caller's command substitution captures *everything* the function prints to stdout, not just the intended "return value" line.

---

## Tips
- Always use `local` for variables that don't need to persist outside the function — treat it as the default, not an afterthought.
- Keep functions focused on a single responsibility; if a function's name needs "and" to describe it (`validate_and_process_and_log`), it's a strong sign it should be split into smaller functions.
- Use descriptive function names (`backup_database`, not `do_stuff`) — this pays off enormously when reading or auditing a script later.

## Common Mistakes
- Forgetting `local`, causing a function to silently overwrite a global variable used elsewhere in the script.
- Trying to `return` a string directly (`return "some text"`) — `return` only accepts a numeric exit status (0–255); use `echo` + command substitution for actual data.
- Printing extra debug/informational output from a function that's also being used for its `echo`-based "return value," polluting the captured result.
- Assuming a function's positional parameters (`$1`, `$2`) are the same as the script's — they're independent and scoped to the function call.

## Common Misconceptions
- **"Bash functions can return any data type, like in Python or JavaScript."** Bash functions only have a numeric exit status as their true "return value"; anything else (strings, structured data) has to be communicated through stdout capture or global variables, which is a fundamentally different model.
- **"Variables are automatically function-scoped, like in most modern languages."** The opposite is true by default in Bash — variables are global unless explicitly declared `local`, which is a frequent source of confusion for people coming from other languages.
- **"A function that doesn't explicitly `return` anything has an undefined exit status."** It actually returns the exit status of the last command executed inside it, which is sometimes useful but can also be an accidental source of unexpected success/failure signaling if not considered deliberately.

## Quick Practice
```bash
add() {
    local sum=$(( $1 + $2 ))
    echo "$sum"
}

result=$(add 5 7)
echo "Result: $result"
```
Confirm this prints `Result: 12`, and explain in your own words why `local` matters here even though this particular example wouldn't obviously break without it.

## Knowledge Check
1. Why is it recommended to always use `local` for variables declared inside a function?
2. What's the difference between a function's exit status and the data it might produce via `echo`?
3. Why can't `return "some string"` be used to send text data back from a Bash function?
4. What does it mean that a function's positional parameters (`$1`, `$2`) are independent of the script's own positional parameters?
5. What determines a function's exit status if it doesn't explicitly call `return`?

<details>
<summary>Answers</summary>

1. Without `local`, a variable assigned inside a function is global by default, which can silently overwrite a variable of the same name used elsewhere in the script — `local` prevents this class of bug by scoping the variable to the function only.
2. The exit status is a numeric value (0–255) indicating success/failure, checked via `$?` or directly in a conditional; actual data (strings, computed values) is separate and must be communicated via stdout (captured with command substitution) or a global variable.
3. `return` in Bash only accepts a numeric exit status (0–255); it is not a general-purpose mechanism for returning arbitrary data like strings.
4. When a function is called, `$1`, `$2`, etc. refer to the arguments passed to *that specific function call*, not to the arguments the overall script was invoked with — they're a completely separate set of values scoped to the function.
5. The exit status of the last command executed within the function, if no explicit `return` is given.
</details>

## Best Practices
- Declare every function-local variable with `local` as a default habit, not an exception.
- Use exit status (`return 0`/`return 1`) for success/failure signaling, and `echo` + command substitution for returning actual data — don't mix the two in a way that pollutes captured output.
- Keep functions small and single-purpose, with descriptive names.
- Organize scripts with functions defined near the top (or in a separate sourced file for larger projects) and the main execution logic clearly separated at the bottom.

## Summary
Functions let you package reusable logic into named, testable units, but Bash's model differs meaningfully from most other languages: variables are global by default unless explicitly scoped with `local`, and a function's "return value" is fundamentally a numeric exit status, with actual data communicated separately through stdout capture. Disciplined use of `local` and clear separation of exit-status versus data-returning functions makes scripts significantly easier to read, test, and audit — particularly important for any script that will run with elevated privileges.

## What's Next?
With variables, loops, and functions all in place, Lesson 05 brings them together to build practical, real-world Bash automation — scripts that actually do useful administrative and security work end to end.

---

## Navigation
⬅️ [Previous: Lesson 03 – Loops](03-loops.md) | [ Lab 04 : Functions](../../labs/module-04/04-functions.md) | ➡️ [Next: Lesson 05 – Bash Automation](05-bash-automation.md)
